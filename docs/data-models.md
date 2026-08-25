# Data Models & Schema

## Overview

Ministry Mapper uses SQLite via PocketBase with a hierarchical data model designed for multi-tenant congregation management. This document provides comprehensive documentation of all data models, relationships, and business rules.

The schema is defined by a single generated snapshot migration, `migrations/1777788260_collections_snapshot.go`, plus a small number of follow-up migrations (see [Migration & Schema Updates](#migration-schema-updates)).

**Collection counts.** The snapshot defines **21 collections**: 5 PocketBase system collections, 11 application collections and 5 analytics views. A later migration adds the two audit-log collections `assignments_log` and `roles_log`, so the running system has **23 collections: 13 application + 5 analytics views + 5 PocketBase system**.

## Entity Relationship Diagram

```
Congregation (Root entity - multi-tenant isolation)
    │
    ├─── Territory (1:M)
    │      │
    │      └─── Map (1:M)
    │             │
    │             ├─── Address (1:M)
    │             │      ├─── addresses_log (1:M)
    │             │      └─── address_options (1:M)
    │             ├─── Assignment (1:M)
    │             │      └─── assignments_log (1:M)
    │             └─── Message (1:M)
    │
    ├─── Option (1:M)
    │      └─── address_options (M:M bridge with Address)
    │
    ├─── User (1:M via Role)
    │      │
    │      ├─── Role (1:M)
    │      │      └─── roles_log (1:M)
    │      └─── Assignment (1:M)
    │
    └─── Message (1:M)
```

Progress and per-status counts are **not** a separate collection: they live in the `aggregates` JSON column on `maps` and in the `progress` number column on `maps` and `territories`.

## Core Collections

There are 13 application collections. Three of them are audit logs - `addresses_log`, `assignments_log` and `roles_log`. **There is no collection named `audit_logs`.**

In the field tables below, **Required** describes whether the application always populates the field. At the PocketBase schema level only record ids, `users.password`, `users.tokenKey`, `users.name` and `addresses.code` actually carry the `required` flag.

### 1. Congregations

**Purpose:** Central entity for multi-tenant isolation and organization settings

**Type:** Base collection

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | text(15) | Yes | Auto-generated unique ID |
| `code` | text | Yes | Organization code |
| `name` | text | Yes | Organization name |
| `expiry_hours` | number | Yes | Assignment link duration (hours) |
| `max_tries` | number | Yes | "Not home" attempts after which an address counts as completed |
| `origin` | select | Yes | Country/region code (single select, 13 values) |
| `timezone` | select | Yes | IANA timezone for date calculations (single select, 20 values) |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

**Origin Values (13):**
`us`, `cn`, `in`, `mx`, `eg`, `sa`, `bd`, `br`, `id`, `jp`, `kr`, `sg`, `my`

**Timezone Values (20 IANA zones):**
`America/New_York`, `America/Chicago`, `America/Denver`, `America/Los_Angeles`, `America/Mexico_City`, `America/Sao_Paulo`, `Asia/Shanghai`, `Asia/Kolkata`, `Asia/Dhaka`, `Asia/Jakarta`, `Asia/Tokyo`, `Asia/Seoul`, `Asia/Singapore`, `Asia/Kuala_Lumpur`, `Asia/Riyadh`, `Asia/Dubai`, `Africa/Cairo`, `Africa/Johannesburg`, `Australia/Sydney`, `Pacific/Auckland`

**Indexes:**
- None. Congregation records are always fetched by primary key.

**Access Rules:**
- **List:** Superuser only (`listRule` is `null`)
- **View:** User with role in the congregation OR valid link-based access
- **Update:** Administrator
- **Create/Delete:** Superuser only

**Business Rules:**
- Controls default assignment link duration for all maps in the congregation
- `max_tries` determines when a "not home" address counts as completed
- Timezone affects date-based calculations and reports
- Updating `expiry_hours` invalidates the in-memory expiry cache through an after-update hook

**Example:**
```json
{
  "id": "abc123",
  "code": "CONG001",
  "name": "North Congregation",
  "expiry_hours": 24,
  "max_tries": 3,
  "origin": "sg",
  "timezone": "Asia/Singapore"
}
```

---

### 2. Territories

**Purpose:** Geographic divisions within congregation for organizing field service

**Type:** Base collection

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | text(15) | Yes | Auto-generated unique ID |
| `congregation` | relation | Yes | FK to Congregations (cascade delete) |
| `code` | text | Yes | Territory identifier (auto: "0") |
| `description` | text | No | Human-readable description |
| `progress` | number | Auto | Completion percentage, integer 0-100 |
| `coordinates` | JSON | No | Optional territory centre, `null` or `{"lat": <float>, "lng": <float>}` |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

**Indexes:**
- `(congregation, code)` - Lookup by congregation and code

The single-column `(congregation)` index was dropped as a redundant prefix of `(congregation, code)`. See [Index Strategy](#index-strategy).

**Relationships:**
- 1:M with Maps
- 1:M with Addresses (denormalized for performance)

**Access Rules:**
- **List:** Authenticated user, filter must name a `congregation`; results are pruned to congregations the caller holds a role in
- **View:** Authenticated user, filter must name a `user`
- **Create/Update/Delete:** Administrator
- **Reset/Delete via endpoints:** `/territory/reset` and `/territory/delete` accept Administrator **or** Conductor

**Progress Calculation:**
Derived from the map `aggregates` of all maps in the territory. See [Business Rules](#business-rules).

**Example:**
```json
{
  "id": "terr_abc",
  "congregation": "cong_123",
  "code": "1A",
  "description": "Downtown District",
  "progress": 65,
  "coordinates": {"lat": 1.3521, "lng": 103.8198}
}
```

---

### 3. Maps

**Purpose:** Represents specific locations or buildings containing addresses

**Type:** Base collection

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | text(15) | Yes | Auto-generated unique ID |
| `congregation` | relation | Yes | FK to Congregations (cascade delete) |
| `territory` | relation | Yes | FK to Territories (cascade delete) |
| `sequence` | number | Yes | Display order within the territory (integer) |
| `code` | text | Yes | Map identifier/number (auto: "0") |
| `description` | text | No | Location description (plain string or localized object) |
| `progress` | number | Auto | Completion percentage |
| `type` | select | Yes | `single` or `multi` (floor type) |
| `coordinates` | JSON | No | `null` or `{"lat": <float>, "lng": <float>}` |
| `aggregates` | JSON | Auto | Cached per-status counts (max 2MB) |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

**Indexes:**
- `(territory, code)` - Primary lookup
- `(territory, sequence)` - Ordering within a territory
- `(congregation)` - Congregation-wide map listing

The single-column `(territory)` index was dropped as a redundant prefix of `(territory, sequence)`. See [Index Strategy](#index-strategy).

**Cascade Deletes:**
- Addresses
- Assignments
- Messages

**Localized Description:**
`description` may hold either a plain string or a `{lang: value}` object. When it is an object the backend resolves it with a fallback chain: exact locale, then the base subtag (`zh-TW` to `zh`), then `en`, then the first available value.

**Aggregates Structure (a contract, not an internal detail):**
```json
{
  "notDone": 30,
  "done": 45,
  "notHome": 12,
  "invalid": 2,
  "dnc": 5,
  "completed": 53,
  "total": 87
}
```

These seven keys are **camelCase on purpose**, unlike the snake_case used for every other field name in the schema. The shape is a contract in two places:

- Territory progress is computed by SUMming `$.completed` and `$.total` out of this JSON across the territory's maps. Renaming or removing either key silently breaks territory progress - no error is raised, the percentage simply stops being correct.
- `analytics_maps` reads `$.done`, `$.notDone`, `$.notHome`, `$.dnc` and `$.invalid` with `json_extract`, so the reporting layer breaks the same silent way.

**Map Types:**
- **single**: Private addresses (landed houses)
- **multi**: Public addresses (multi-floor buildings)

**Access Rules:**
- **List:** Authenticated user, filter must name a `territory` or `congregation`; results are pruned to congregations the caller holds a role in
- **View:** User with role in the map's congregation OR valid link for that map
- **Create:** `/map/add` endpoint (Administrator)
- **Update/Delete:** Administrator

**Example:**
```json
{
  "id": "map_xyz",
  "territory": "terr_abc",
  "congregation": "cong_123",
  "sequence": 5,
  "code": "12",
  "description": "Main Street Apartments",
  "type": "multi",
  "coordinates": {"lat": 1.3521, "lng": 103.8198},
  "aggregates": {
    "notDone": 10,
    "done": 20,
    "notHome": 4,
    "invalid": 1,
    "dnc": 2,
    "completed": 23,
    "total": 34
  },
  "progress": 68
}
```

---

### 4. Addresses

**Purpose:** Individual units or households within maps

**Type:** Base collection

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | text(15) | Yes | Auto-generated unique ID |
| `congregation` | relation | Yes | FK to Congregations (denormalized, cascade delete) |
| `territory` | relation | Yes | FK to Territories (denormalized, cascade delete) |
| `map` | relation | Yes | FK to Maps (cascade delete) |
| `floor` | number | Yes | Floor number (0-based) |
| `code` | text | Yes | Unit/door code, min 1 char, pattern `^[a-zA-Z0-9-]+$` |
| `status` | select | Yes | Address visit status |
| `sequence` | number | Yes | Column order within the map |
| `not_home_tries` | number | Auto | Counter for "not home" attempts |
| `notes` | text | No | Field worker notes |
| `last_notes_updated` | date | Auto | When notes last changed (via hook) |
| `last_notes_updated_by` | text | Auto | Name of last note updater (via hook) |
| `dnc_time` | date | No | When marked "do not call" |
| `coordinates` | JSON | No | `null` or `{"lat": <float>, "lng": <float>}` |
| `updated_by` | text | Auto | Server-derived name of the last updater |
| `source` | select | No | How the address was created |
| `created_by` | text | Auto | Server-derived name of the creator |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

There is **no** `type` field on `addresses`. Household options are linked through the `address_options` junction collection.

**Status Values:**
- `not_done` - Not yet visited (initial state)
- `done` - Successfully completed
- `not_home` - No one home (can retry)
- `do_not_call` - Excluded from work
- `invalid` - Address doesn't exist

**Source Values:**
- `app` - Created by a publisher or administrator in the app
- `admin` - Created through an administrative action
- `map_init` - Created when the map was initialized
- `floor_copy` - Created by copying an existing floor

**Coordinates:**
`coordinates` is a single JSON column, shaped either `null` or `{"lat": <float>, "lng": <float>}`. There are **no** `latitude` / `longitude` columns anywhere in the schema.

- `/address/update` and `/address/add` accept the value as raw JSON. An empty value or the literal `null` clears the field; anything else is stored verbatim.
- Read endpoints return it as raw JSON or `null`.
- The column has existed since the first backend commit, which is why the app's "capture this house's location from the device" feature needed **no migration** - it simply started populating a field that was already there.
- `maps.coordinates` and `territories.coordinates` are separate fields of the same shape.

**Indexes:**
- `(code, map)` - Code lookup within a map
- `(floor, map)` - Floor organization
- `(map, status)` - Status distribution queries and map filtering
- `(territory, status)` - Territory-wide status
- `(source, created)` - New-address notification job

The single-column `(map)` index was dropped as a redundant prefix of `(map, status)`. See [Index Strategy](#index-strategy).

**Auto Hooks:**
- A pre-save hook stamps `last_notes_updated` and `last_notes_updated_by` when `notes` changed
- An after-update hook writes an `addresses_log` row for the status change
- An after-update hook recalculates the map `aggregates` and rolls the result up to the territory, **in real time** - there is no aggregate cron job. Bulk operations set a `bulk_reset:<mapID>` flag in the application store to suppress per-address recalculation and then recalculate once at the end.
- A `not_home_tries` increment on a `not_home` address triggers both the audit log and the aggregate recalculation even though the status string did not change, because the aggregate bucket moves.

**Access Rules:**
- **List:** Authenticated user **or** valid `link-id`; the filter must name a `map` and explicit `fields`. Link holders may name exactly one map, and it must be the linked map. Results are additionally pruned server-side to the caller's authorized maps.
- **View/Create/Update/Delete:** Superuser only at the collection API level (all four rules are `null`).
- **Application writes:** every mutation goes through the custom endpoints - `/address/update` and `/address/add` (valid `link-id` or any congregation role), and the administrator-only map endpoints for structural changes (`/map/code/add`, `/map/codes/update`, `/map/code/delete`, `/map/floor/add`, `/map/floor/remove`, `/map/reset`).
- `updated_by` and `created_by` cannot be supplied by a client; the server derives the actor from the JWT user's name or, for link access, from the assignment's `publisher` field.

**Example:**
```json
{
  "id": "addr_123",
  "congregation": "cong_123",
  "territory": "terr_abc",
  "map": "map_xyz",
  "floor": 2,
  "code": "201",
  "status": "not_home",
  "sequence": 5,
  "not_home_tries": 2,
  "notes": "Try after 6pm",
  "last_notes_updated": "2026-08-19T14:30:00Z",
  "last_notes_updated_by": "John Doe",
  "coordinates": {"lat": 1.3521, "lng": 103.8198},
  "source": "app",
  "created_by": "John Doe",
  "updated_by": "John Doe"
}
```

---

### 5. Users

**Purpose:** System user accounts with authentication

**Type:** Auth collection (PocketBase user-auth-type), 17 fields

**System Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | text(15) | Yes | Auto-generated unique ID |
| `email` | email | Yes | Login identity (unique where non-empty) |
| `emailVisibility` | bool | Auto | Whether email is public (set true on create) |
| `verified` | bool | Auto | Email verification status |
| `password` | password | Yes | Bcrypt hashed (cost: 10, min 6 chars) |
| `tokenKey` | text | Hidden | Token generation key (30-60 chars) |

**Custom Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | text | Yes | Display name (2-50 chars) |
| `disabled` | bool | No | Account disabled flag |
| `last_login` | date | Auto | Last authentication timestamp (via hook) |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |
| `unprovisioned_since` | date | Auto | When the user was first seen with no role rows |
| `unprovisioned_warning_sent_at` | date | Auto | Unprovisioned warning stamp |
| `unprovisioned_final_warning_sent_at` | date | Auto | Unprovisioned final warning stamp |
| `admin_alerted_at` | date | Auto | When superusers were alerted about the account |
| `inactive_warning_sent_at` | date | Auto | Inactivity warning stamp |
| `inactive_final_warning_sent_at` | date | Auto | Inactivity final warning stamp |

The six lifecycle date fields drive the daily unprovisioned-user and inactive-user jobs. A successful login clears both inactivity stamps.

**Name Validation Pattern:**
`^[A-Za-z][\w\s\.\-']*$` (starts with letter, allows word chars, spaces, dots, hyphens, apostrophes)

**Autogen Pattern:**
`user[0-9]{5}[A-Za-z]` (e.g., user12345A)

**Indexes:**
- `email` (unique, partial: `WHERE email != ''`) - Fast auth lookup
- `tokenKey` (unique) - Token validation

**Auth Configuration:**
- **Password Auth:** Enabled, identity field `email` (min 6 chars, bcrypt cost 10)
- **OAuth2:** Google PKCE flow
  - Maps `name` from OAuth provider
  - Auto-creates user on first login
- **MFA:** Enabled (duration: 1800s) with rule `@request.context != 'oauth2'` - so MFA is skipped for OAuth2 sign-in
- **OTP:** Enabled (duration: 180s, length: **4 digits**)
- **Auth Token:** Duration 1209600s (14 days)
- **Verify Email:** Duration 604800s (7 days)
- **Password Reset:** Duration 1800s (30 min)
- **Email Change:** Duration 1800s (30 min)
- **Auth Alert:** Enabled (notifies on login from new location)

**MFA/OTP - two switches, both real:**
Documentation elsewhere shows `PB_OTP_ENABLED=false` and `PB_MFA_ENABLED=false`, which looks like it contradicts the "Enabled" above. Both statements are true, because two different mechanisms write the same setting:

1. The collection snapshot ships the `users` collection with **MFA and OTP enabled**, together with the durations and the 4-digit OTP length listed above. This is the state a database gets from importing the snapshot.
2. The `initial_user_auth.go` migration then sets `users.OTP.Enabled` and `users.MFA.Enabled` from `PB_OTP_ENABLED` / `PB_MFA_ENABLED`, which **default to false**. The comparison is against the literal string `"true"`, so `TRUE`, `True` and `1` do not enable anything.

Because that migration is one of the `initial_*.go` files, it runs *after* the snapshot (see [Migration & Schema Updates](#migration-schema-updates)) and therefore wins. Like every env var read inside a migration, it only takes effect on the **first** migration run against a given database; changing it later has no effect until the setting is changed in the admin UI.

**There is no TOTP and there are no backup codes.** PocketBase MFA here is email one-time-password: a 4-digit code, valid for 180 seconds, delivered by email.

**Access Rules:**
- **List:** Administrator (in any congregation). The collection rule additionally restricts listing to `email~` / `name~` searches on verified users with an explicit `fields` parameter.
- **View:** Own record always; otherwise Administrator
- **Create:** Public - either an OAuth2 context, or a body carrying `email`, `name` and `password`
- **Update:** Self only
- **Delete:** Superuser only

**File Token:** 120s duration

**Relationships:**
- 1:M with Roles
- 1:M with Assignments

**Example:**
```json
{
  "id": "user_abc",
  "email": "john.doe@example.com",
  "name": "John Doe",
  "verified": true,
  "disabled": false,
  "last_login": "2026-08-19T14:30:00Z"
}
```

---

### 6. Roles

**Purpose:** Permission assignments linking users to congregations

**Type:** Base collection

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | text(15) | Yes | Auto-generated unique ID |
| `user` | relation | Yes | FK to Users (cascade delete) |
| `congregation` | relation | Yes | FK to Congregations (cascade delete) |
| `role` | select | Yes | Permission level (single select) |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

**Role Values - there are exactly three:**
- `read_only` - View only access
- `conductor` - Can manage assignments, addresses and territory resets
- `administrator` - Full access

This collection is the authoritative definition of what a "role" is in Ministry Mapper. **`publisher` is not a role and has no `roles` row.** A publisher is someone holding a valid assignment link: the credential is the `link-id` header, whose value is an `assignments` record id, and it comes with no user account and no login. So the system has **three congregation roles plus one link-based access path**, not four roles.

The frontend keeps a separate client-side gating scale (`no_access: -1`, `read_only: 1`, `publisher: 2`, `conductor: 2`, `administrator: 3`). That is UI gating, not schema - `publisher` there is the level a link holder is treated as. There is likewise no "no access" or "delete access" role: removing someone's access means deleting their `roles` row.

**Indexes:**
- `(congregation, role)` - Role distribution within a congregation
- `(user, congregation, role)` - Covering index for the per-user role lookup

The `(user)` and `(user, congregation)` indexes were dropped as redundant prefixes of `(user, congregation, role)`. See [Index Strategy](#index-strategy).

**Access Rules:**
- **Create/Update/Delete:** Administrator
- **List:** Authenticated user; with `congregation=` filters the caller must hold a role in each, and a `user=` filter is allowed only for the caller's own id
- **View:** Superuser only (`viewRule` is `null`); role data reaches clients through the list endpoint

**Relationships:**
- M:N between Users and Congregations
- Every create, role change and revocation writes a `roles_log` row

**Business Rules:**
- One role per user per congregation
- Role determines permissions for all actions
- Deleting a user's last role stamps `users.unprovisioned_since` and clears the unprovisioned warning stamps, restarting the grace period

**Example:**
```json
{
  "id": "role_xyz",
  "user": "user_abc",
  "congregation": "cong_123",
  "role": "conductor"
}
```

---

### 7. Assignments

**Purpose:** Publisher access tokens for time-limited map access

**Type:** Base collection

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | text(25) | Yes | **Serves as the anonymous access token**, 25 chars matching `[a-zA-Z0-9]` |
| `congregation` | relation | Yes | FK to Congregations (cascade delete) |
| `map` | relation | Yes | FK to Maps (cascade delete) |
| `user` | relation | No | Publisher user (optional, no cascade delete) |
| `type` | select | Yes | Assignment classification |
| `expiry_date` | date | Yes | Automatic expiry timestamp |
| `publisher` | text | No | Publisher name/identifier |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

**Assignment Types:**
- `normal` - Regular territory work
- `personal` - Personal ministry territory

**Indexes:**
- `(expiry_date)` - For the background cleanup job
- `(user, created)` - User assignment history
- `(map, expiry_date)` - Expiry check per map

The single-column `(map)` index was dropped as a redundant prefix of `(map, expiry_date)`. See [Index Strategy](#index-strategy).

**Access Rules:**
- **Create/Delete:** Administrator or Conductor
- **View:** User with a role in the assignment's congregation, OR a `link-id` equal to this record's id - a link may read only itself - and `expiry_date > now`
- **List:** Authenticated user; `map=` filters require authorization for every map named, and another user's history requires Administrator or Conductor somewhere

**Workflow:**
1. A Conductor or Administrator creates the assignment via the `/territory/link` endpoint (any congregation role may mint a quicklink, but only for their own congregation's territory)
2. The publisher receives the 25-character assignment id as the link token
3. The publisher's requests carry that token as an HTTP header
4. The server validates it in SQL on every request: `id = ? AND map = ? AND expiry_date > datetime('now')`
5. A background job deletes expired assignments every 5 minutes and writes an `expired` row to `assignments_log`

**Header Naming Trap:**
The same header has two spellings depending on where you are looking:

| Context | Spelling |
|---|---|
| Go handler code, and the header actually sent on the wire | `link-id` (hyphen) |
| PocketBase API-rule expressions | `@request.headers.link_id` (underscore) |

PocketBase normalizes header names to underscores inside rule expressions, so both appear in the schema and both are correct in their own context. The realtime path accepts either spelling inside the subscription's `options.headers`. Realtime subscriptions do not inherit the header from the HTTP session, so it must be passed explicitly in the subscription options.

**Precedence:** a non-empty `link-id` decides the request on its own. If the link check fails the request is refused **even when the caller also presented a valid administrator JWT** - it never falls back to the role check. A link is scoped to exactly one map, and list or subscribe requests naming more than one map id are refused outright.

**Audit Trail:** creation and deletion write `assignments_log` rows with actions `assigned` and `unassigned`; cron-driven expiry writes `expired`.

**Example:**
```json
{
  "id": "k3n8sq2vt7yb1dp9wx4gc6h0z",
  "congregation": "cong_123",
  "map": "map_xyz",
  "user": "user_abc",
  "type": "normal",
  "expiry_date": "2026-08-20T14:30:00Z",
  "publisher": "John Doe"
}
```

---

### 8. Options

**Purpose:** Household type classifications (customizable per congregation)

**Type:** Base collection

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | text(15) | Yes | Auto-generated unique ID |
| `congregation` | relation | Yes | FK to Congregations (cascade delete) |
| `code` | text | Yes | Option identifier (e.g., "RES", "BUS") |
| `description` | text | Yes | Human-readable label |
| `is_countable` | bool | Yes | Include in progress calculations |
| `is_default` | bool | Yes | Default option for new addresses |
| `sequence` | number | Yes | Display/sort order (integer) |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

**Indexes:**
- `(congregation, sequence)` - Ordering within congregation
- `(congregation, is_default)` - Find default option

**Constraints:**
- Exactly one `is_default = true` per congregation (enforced at application layer)
- Codes unique per congregation
- Sequences unique per congregation

**Impact on Progress:**
- Only addresses carrying at least one countable option (`is_countable = true`) are counted in progress at all
- Non-countable options are tracked but excluded from the progress calculation
- When an option is deleted, affected addresses receive the congregation's default option

**Access Rules:**
- **List:** Authenticated user or valid `link-id`; the filter must name a `congregation`
- **View:** User with a role in the option's congregation, or a link valid for that congregation
- **Create/Update/Delete:** `/options/update` endpoint (Administrator). All three collection-level rules are `null`.

**Relationships:**
- M:M with Addresses through the `address_options` junction collection

**Example:**
```json
{
  "id": "opt_res",
  "congregation": "cong_123",
  "code": "RES",
  "description": "Residential",
  "is_countable": true,
  "is_default": true,
  "sequence": 1
}
```

---

### 9. Messages

**Purpose:** Communications and notifications between users

**Type:** Base collection

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | text(15) | Yes | Auto-generated unique ID |
| `congregation` | relation | Yes | FK to Congregations (cascade delete) |
| `map` | relation | Yes | FK to Maps (cascade delete) |
| `message` | text | Yes | Message content |
| `created_by` | text | Yes | Name of creator |
| `read` | bool | Yes | Whether read by administrator |
| `pinned` | bool | Yes | Admin pinned instruction flag |
| `type` | select | Yes | Intended recipient type |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

**Message Types:**
- `publisher` - From publishers to admins
- `conductor` - From conductors
- `administrator` - Admin instructions (can be pinned)

**Indexes:**
- `(map, pinned, created)` - Recent pinned messages
- `(map, type, pinned)` - Filter by recipient type and pin status
- `(map, type, read)` - Unread count per type

**Access Rules:**
- **Create:** Any congregation role OR valid link access
- **List:** Authenticated user or valid `link-id`; the filter must name a `map`
- **Update/Delete:** Administrator
- **View:** Superuser only (`viewRule` is `null`); messages reach clients through the list endpoint

**Background Processing (all cron times UTC):**
- `processMessages` at `:08` and `:38` - unread publisher messages emailed to administrators
- `processInstructions` at `:18` and `:48` - pinned administrator instructions emailed to publishers
- `processNotes` at `:28` - property note updates emailed to administrators

**Example:**
```json
{
  "id": "msg_abc",
  "congregation": "cong_123",
  "map": "map_xyz",
  "message": "Please revisit unit 205",
  "created_by": "John Doe",
  "read": false,
  "pinned": false,
  "type": "publisher"
}
```

---

### 10. Addresses Log

**Purpose:** Audit trail of address status changes - every time an address status changes, a record is written here

**Type:** Base collection

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | text(15) | Yes | Auto-generated unique ID |
| `address` | relation | Yes | FK to Addresses (no cascade delete) |
| `congregation` | relation | Yes | FK to Congregations (no cascade delete) |
| `territory` | text | Yes | Territory id, stored as plain text |
| `map` | text | Yes | Map id, stored as plain text |
| `old_status` | text | Yes | Previous address status value |
| `new_status` | text | Yes | New address status value |
| `changed_by` | text | No | Name of the actor, copied from `addresses.updated_by` |
| `created` | date | Auto | Timestamp of the change |
| `updated` | date | Auto | Last update timestamp |

`territory` and `map` are plain text, not relations, so log rows survive the deletion of the records they describe.

**Indexes:**
- `(territory, created)` - Territory activity over time
- `(map, created)` - Map activity over time

**Relationships:**
- M:1 with Addresses (each log entry belongs to one address)
- M:1 with Congregations

**Write Rules:**
- Written by an after-update hook on `addresses`
- Nothing is written when either status is empty
- A row **is** written when the status is unchanged but `not_home_tries` increased on a `not_home` address, because the aggregate bucket moves

**Access Rules:**
- **All operations:** Superuser only. Every collection rule (list, view, create, update, delete) is `null`.

**Example:**
```json
{
  "id": "log_abc123",
  "address": "addr_123",
  "congregation": "cong_123",
  "territory": "terr_abc",
  "map": "map_xyz",
  "old_status": "not_done",
  "new_status": "not_home",
  "changed_by": "John Doe"
}
```

---

### 11. Assignments Log

**Purpose:** Audit trail of assignment link grants, revocations and expiries

**Type:** Base collection

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | text(15) | Yes | Auto-generated unique ID |
| `assignment` | text | Yes | The assignment id, stored as plain text |
| `congregation` | relation | Yes | FK to Congregations (no cascade delete) |
| `map` | relation | Yes | FK to Maps (no cascade delete) |
| `user` | relation | No | FK to Users (no cascade delete) |
| `publisher` | text | No | Publisher name recorded on the assignment |
| `type` | text | No | The assignment's `type` at the time of the action |
| `action` | text | Yes | What happened |
| `expiry_date` | date | No | The assignment's expiry at the time of the action |
| `changed_by` | relation | No | FK to Users - who performed the action |
| `created` | date | Auto | Timestamp of the action |
| `updated` | date | Auto | Last update timestamp |

**Action Values:**
- `assigned` - Link created
- `unassigned` - Link deleted by a person
- `expired` - Link removed by the 5-minute cleanup job

**Indexes:**
- `(map, created)` - Assignment history per map
- `(user, created)` - Assignment history per user

**Write Rules:**
- Written by hooks on `assignments` create and delete, which run **after** the request succeeded, so a rejected request leaves no audit row
- The expiry cleanup job writes its `expired` rows inside the cleanup transaction
- `changed_by` is `""` when the actor is a superuser or the cron job. A superuser has no `users` record, and passing a superuser id into a relation pointing at `users` would fail validation and abort the audit write.

**Access Rules:**
- **All operations:** Superuser only. Every collection rule is `null`.

**Example:**
```json
{
  "id": "alog_abc123",
  "assignment": "k3n8sq2vt7yb1dp9wx4gc6h0z",
  "congregation": "cong_123",
  "map": "map_xyz",
  "user": "user_abc",
  "publisher": "John Doe",
  "type": "normal",
  "action": "assigned",
  "expiry_date": "2026-08-20T14:30:00Z",
  "changed_by": "user_admin1"
}
```

---

### 12. Roles Log

**Purpose:** Audit trail of role grants, changes and revocations

**Type:** Base collection

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | text(15) | Yes | Auto-generated unique ID |
| `congregation` | relation | Yes | FK to Congregations (no cascade delete) |
| `user` | relation | Yes | FK to Users - whose role changed (no cascade delete) |
| `old_role` | text | No | Role before the change |
| `new_role` | text | No | Role after the change |
| `action` | text | Yes | What happened |
| `changed_by` | relation | No | FK to Users - who performed the action |
| `created` | date | Auto | Timestamp of the action |
| `updated` | date | Auto | Last update timestamp |

**Action Values:**
- `granted` - Role created; `new_role` is the new role
- `changed` - Role updated; `old_role` is captured before the write
- `revoked` - Role deleted; `old_role` is the removed role and `new_role` is `""`

**Indexes:**
- `(congregation, created)` - Role activity per congregation
- `(user, created)` - Role history per user

**Write Rules:**
- Written by hooks on `roles` create, update and delete, all running after the request succeeded
- A no-op update where the old and new role are identical writes nothing
- `changed_by` is `""` for superuser actors, for the same relation-validation reason as `assignments_log`

**Access Rules:**
- **All operations:** Superuser only. Every collection rule is `null`.

**Example:**
```json
{
  "id": "rlog_abc123",
  "congregation": "cong_123",
  "user": "user_abc",
  "old_role": "read_only",
  "new_role": "conductor",
  "action": "changed",
  "changed_by": "user_admin1"
}
```

---

### 13. Address Options

**Purpose:** Tracks which household options (from the `options` collection) apply to individual addresses - the junction table for the Address to Option many-to-many relationship

**Type:** Base collection

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | text(15) | Yes | Auto-generated unique ID |
| `address` | relation | Yes | FK to Addresses (cascade delete) |
| `map` | relation | Yes | FK to Maps (denormalized for fast queries) |
| `option` | relation | Yes | FK to Options |
| `congregation` | relation | Yes | FK to Congregations (denormalized for fast queries) |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

**Indexes:**
- `(address, option, map)` - **Unique**; one row per address/option/map triple
- `(map)` - Fetch every option assignment for a map in one query

**Relationships:**
- M:1 with Addresses
- M:1 with Options
- M:1 with Maps
- M:1 with Congregations

**Business Rules:**
- Every address must have at least one row here; new addresses receive the congregation's default option
- An address with no countable option is uncountable and never appears as "still needs a call"

**Access Rules:**
- **List:** Authenticated user or valid `link-id`; the filter must name a `map` and explicit `fields`
- **View:** Authenticated user or valid `link-id`, checked against the record's map
- **Create/Update/Delete:** Superuser only at the collection API level (all three rules are `null`); the application writes these rows through `/address/add`, `/address/update` and `/options/update`

**Example:**
```json
{
  "id": "aopt_abc123",
  "address": "addr_123",
  "map": "map_xyz",
  "option": "opt_res",
  "congregation": "cong_123"
}
```

---

## Analytics Collections

Analytics collections are **read-only SQL views**, not tables: they hold no rows of their own and are computed at query time from `maps`, `territories`, `addresses`, `addresses_log`, `users` and `roles`. All five have every API rule set to `null`, so they are readable by superusers only, and they are the data source for the monthly territory activity report. These collections are managed internally and their exact fields may evolve over time; they are documented here by purpose rather than individual fields.

### analytics_territories

**Purpose:** Territory-level rollup - joins territories to their addresses and congregation, and reports the congregation name, total address count and a count per status alongside the stored territory progress.

### analytics_maps

**Purpose:** Map-level rollup - flattens each map's `aggregates` JSON into `done` / `not_done` / `not_home` / `dnc` / `invalid` columns and its `coordinates` JSON into `lat` / `lng`, next to the map's code, type, sequence and progress.

### analytics_daily_status

**Purpose:** Daily status-change counts derived from `addresses_log`, grouped by day, congregation, territory and new status. Powers day-over-day and week-over-week trend reporting.

### analytics_not_home

**Purpose:** Every address currently marked `not_home`, joined to its congregation's `max_tries` and labelled `maxed_out` or `retrying`. Used to surface follow-up targets and persistently unreachable addresses.

### analytics_user_audit

**Purpose:** User accountability view - name, email, `last_login`, computed `days_inactive`, disabled and verified flags, role count and a concatenated list of "congregation (role)". Ordered so that users with no roles come first, then the most inactive.

> **Note:** Analytics collections are views over the operational tables. They are never written to - reads should go through the reporting layer, and application code should not attempt to insert or update rows here.

---

## PocketBase System Collections

Five system collections are owned and managed by PocketBase itself. They are listed for completeness; application code does not read or write them directly.

- `_superusers` - superuser (admin) accounts, separate from `users`
- `_mfas` - in-flight multi-factor authentication challenges
- `_otps` - issued one-time passwords
- `_externalAuths` - links between a `users` record and an OAuth2 provider identity
- `_authOrigins` - remembered device fingerprints, used by the new-location auth alert

---

## Database Technology

**Engine:** SQLite via PocketBase (modernc.org/sqlite v1.57.0)

**Features:**
- Pure Go implementation (CGo-free) - enables cross-platform compilation
- Based on C SQLite transpiled to Go using ccgo/v4
- Actively maintained with regular security updates

**SQL Features Used:**
- JSON fields for flexible data (coordinates, aggregates), read with `json_extract`
- Composite indexes for query optimization
- Transactions for ACID compliance
- SQL views for the analytics layer

**Advantages:**
- Self-contained file-based database
- No separate database service needed
- Perfect for self-hosted deployments
- No CGO dependency enables static binary builds
- Proven stability with security patches

---

## Index Strategy

**Total: 29 indexes across the 13 application collections**, counted after the redundant-index migration below. The five analytics views carry no indexes of their own; the PocketBase system collections carry theirs.

| Collection | Indexes |
|--------------|---------|
| `users` | 2 |
| `congregations` | 0 |
| `territories` | 1 |
| `maps` | 3 |
| `addresses` | 5 |
| `address_options` | 2 |
| `options` | 2 |
| `assignments` | 3 |
| `roles` | 2 |
| `messages` | 3 |
| `addresses_log` | 2 |
| `assignments_log` | 2 |
| `roles_log` | 2 |

### Dropped Redundant Indexes

Migration `1786627200_drop_redundant_indexes.go` dropped six indexes. Each was a strict leading prefix of a wider index that already answers the same queries, so SQLite can use the wider index for both:

| Dropped | Collection | Covered instead by |
|---------|------------|--------------------|
| `idx_7CBdHug (map)` | `addresses` | `idx_Fx581hd (map, status)` |
| `idx_O2TlLJr (territory)` | `maps` | `idx_TzbzxPXi9e (territory, sequence)` |
| `idx_Otsl0yR (congregation)` | `territories` | `idx_fMh5sfU (congregation, code)` |
| `idx_pI4sxv2 (map)` | `assignments` | `idx_Su1rP10S5r (map, expiry_date)` |
| `idx_iPooFW46s8 (user)` | `roles` | `idx_PUEoaq44d4 (user, congregation, role)` |
| `idx_Dya44KEsGS (user, congregation)` | `roles` | `idx_PUEoaq44d4 (user, congregation, role)` |

This was verified against a copy of production data holding 1,160,985 addresses: every affected query keeps an indexed search, and both `roles` lookups stay covering. The result is roughly 27 MB reclaimed and six fewer index writes on every address insert. The migration is idempotent - it skips collections and indexes that are already in the desired state, so it is safe on a fresh database.

### Deliberately Kept: `addresses.idx_4xBUDiPsKJ (source, created)`

This index is about 38 MB for a few hundred matching rows, which looks like an obvious candidate for a partial index on `source = 'app'`. It is kept in full on purpose. PocketBase compiles the literal in `source = 'app'` down to a **bound parameter**, and SQLite cannot prove that a bound parameter satisfies a partial-index predicate. A partial index would therefore be ignored and the query would fall back to a full table scan.

### Freelist and VACUUM

Dropping an index returns its pages to the SQLite **freelist**, not to the filesystem: the database file does not shrink on its own. Reclaiming that space requires a manual `VACUUM`, which **cannot** be run from a migration because migrations execute inside a transaction. Plan it as a separate maintenance step during a quiet window.

---

## Key Relationships

| Relationship | Cardinality | Cascade Behavior |
|--------------|-------------|------------------|
| Congregation → Territory | 1:M | Delete cascade |
| Congregation → Map | 1:M | Delete cascade |
| Congregation → Address | 1:M | Delete cascade |
| Congregation → Option | 1:M | Delete cascade |
| Congregation → Role | 1:M | Delete cascade |
| Congregation → Assignment | 1:M | Delete cascade |
| Congregation → Message | 1:M | Delete cascade |
| Territory → Map | 1:M | Delete cascade |
| Territory → Address | 1:M | Delete cascade (denormalized) |
| Map → Address | 1:M | Delete cascade |
| Map → Assignment | 1:M | Delete cascade |
| Map → Message | 1:M | Delete cascade |
| Address → address_options | 1:M | Delete cascade |
| Option → address_options | 1:M | No cascade; handled by `/options/update` |
| Address ↔ Option | M:M | Through `address_options` |
| User → Role | 1:M | Delete cascade |
| User → Assignment | 1:M | No cascade; assignments are kept |
| Address → addresses_log | 1:M | No cascade; log rows are retained |
| Assignment → assignments_log | 1:M | No cascade; `assignment` is a plain id |
| Role change → roles_log | 1:M | No cascade; references `user` and `congregation` |

---

## Sequences

"Sequence" means two different things in this schema, and they are unrelated:

| Field | Scope | Meaning |
|-------|-------|---------|
| `addresses.sequence` | Per map | **Column ordering of the map grid.** Shared by every floor of the same `code` |
| `maps.sequence` | Per territory | **Display order of maps** in the territory list |

**Invariant: one `sequence` per `code` per map.** Two codes sharing a sequence puts a unit's cells under its neighbour's column header, and because the Excel report keys its grid on `sequence`, the second of a tied pair silently overwrites the first and disappears from the export.

Gaps in sequence values are legal; only collisions and partial payloads are refused. `/maps/sequence` and `/map/codes/update` both require a complete, duplicate-free ordering and renumber atomically. New collisions can no longer be introduced: `/map/code/add` and `/address/add` read the high-water mark inside their transaction, and `/map/floor/add` copies an existing floor's sequences.

A console command repairs historical collisions:

```bash
./main fix-sequences [--apply] [--include-unclear] [--map <id>]
```

It is a **dry run unless `--apply`** is passed. It detects both violations - two codes sharing a sequence, and one code holding two sequences - and renumbers affected maps to `0..N-1` while preserving the map's own column direction (a corridor numbered high-to-low is as normal as low-to-high). Maps where a code holds more than one sequence are skipped, because renumbering cannot decide which duplicate to keep; maps with no dominant column direction are reported as UNCLEAR and skipped unless `--include-unclear` is passed. The repair writes with raw SQL, so it does not stamp `updated` / `updated_by` and does not broadcast realtime events to publishers working the map.

---

## Business Rules

### Progress Calculation

Only addresses carrying at least one **countable** household option are counted at all. Addresses with status `do_not_call` or `invalid` are excluded from the denominator entirely.

**Map progress:**
```
total     = done + not_done + not_home_max_tries + not_home_less_tries
completed = done + not_home_max_tries
progress  = round(completed / total * 100)
```

- `not_home_max_tries` are `not_home` addresses whose `not_home_tries` has reached the congregation's `max_tries`. They count as **completed**.
- `not_home_less_tries` are `not_home` addresses still within the limit. They count towards `total` but not towards `completed`.
- Rounding is arithmetic rounding, not truncation: 199 of 200 renders as 100%, not 99%.
- If `max_tries <= 0` the congregation has **no limit**, so nothing is ever completed through the not-home path.

**Territory progress** is derived from the map aggregates, never from a fresh address scan:
```
territory.progress = round(
    SUM(json_extract(maps.aggregates, '$.completed')) /
    SUM(json_extract(maps.aggregates, '$.total')) * 100
)
```
A map with no countable addresses contributes 0/0 and is invisible to the percentage rather than dragging it down.

**About `max_tries`:** it is a per-congregation setting with no universal default. `/link/map` coalesces a missing value to **1**, and the seed and example data in this documentation use **3**. Do not assume a value when reading a congregation's records.

### Address Lifecycle

```
not_done → not_home (retry attempts) → done
        → do_not_call (excluded)
        → invalid (excluded)
```

`do_not_call` and `invalid` leave the progress denominator entirely; a `not_home` address re-enters `completed` once its tries reach `max_tries`.

### Auto-Update Triggers

1. **Address Update:**
   - Updates `last_notes_updated` and `last_notes_updated_by` if notes changed
   - Writes an `addresses_log` row for the status change
   - Recalculates the map `aggregates` and `progress` **in real time**, then rolls the result up to the territory. This replaced an earlier batch cron job - there is no aggregate cron.
   - Skips the recalculation entirely when neither `status` nor `not_home_tries` changed, so a notes-only edit does no aggregate work

2. **Bulk Map Operations:**
   - Set a `bulk_reset:<mapID>` flag in the application store before the transaction, so N address writes do not trigger N recalculations
   - Clear the flag and recalculate once when the transaction finishes

3. **Assignment Creation:**
   - Sets expiry from the congregation's `expiry_hours`
   - Generates a unique 25-character id, which is itself the access token

4. **Role Changes:**
   - Write a `roles_log` row on grant, change and revocation
   - Deleting a user's last role restarts their unprovisioned grace period

---

## Migration & Schema Updates

**Schema Snapshot:** `migrations/1777788260_collections_snapshot.go`

The snapshot is a single generated JSON array imported with `deleteMissing = true`. That makes the snapshot **authoritative**: any collection that is not in it is dropped. It defines 21 collections (5 PocketBase system + 11 application + 5 analytics views); the two audit-log collections are added by a later migration.

### Migration Order

PocketBase runs registered migrations in **filename order**, and digits sort before letters - so the four `initial_*.go` files run **last**, after the snapshot. This is not obvious and it matters: settings, the bootstrap superuser and the Google OAuth2 provider are all applied on top of the imported collections, and `initial_user_auth.go` has the final word on the `users` MFA/OTP flags.

1. `1767764876_initial_origins.go` - adds the `congregations.origin` and `congregations.timezone` select fields, idempotently
2. `1777788260_collections_snapshot.go` - the whole schema, imported with `deleteMissing = true`
3. `1778400000_create_log_collections.go` - creates `assignments_log` and `roles_log`, all API rules left `null`
4. `1780000000_seed_test_data.go` - **test fixtures only**; guarded by the `testdata` build tag, so it is never compiled into a production binary
5. `1780000001_add_bulk_reset_address_source.go` - adds `bulk_reset` to the `addresses.source` select values
6. `1780000002_remove_bulk_reset_address_source.go` - reverses the previous migration after hook suppression moved to the application store; clears leftover `source = 'bulk_reset'` values
7. `1781395200_backfill_map_territory_progress.go` - backfills `maps.aggregates.$.completed` / `$.total`, recomputes `maps.progress`, then recomputes `territories.progress` from the map JSON. Maps with no countable addresses are deliberately left untouched.
8. `1786627200_drop_redundant_indexes.go` - drops the six redundant indexes described in [Index Strategy](#index-strategy)
9. `initial_admin_usr.go` - bootstraps the `_superusers` record from `PB_ADMIN_EMAIL` / `PB_ADMIN_PASSWORD`; skips if that email already exists
10. `initial_google_oauth.go` - appends the `google` OAuth2 provider to `users`, only when both `GOOGLE_CLIENT_ID` and `GOOGLE_CLIENT_SECRET` are set
11. `initial_settings.go` - writes PocketBase settings from the environment: app name and URL, sender identity, SMTP, hidden controls, batch API limits and rate-limit rules
12. `initial_user_auth.go` - sets `users.OTP.Enabled` and `users.MFA.Enabled` from `PB_OTP_ENABLED` / `PB_MFA_ENABLED`

### Migration Process

1. Migrations are applied **automatically when the server starts** (`serve`). There is no manual `./main migrate` step in the normal flow, and the bootstrap superuser is created as part of that first run.
2. *Automigrate* - generating new migration files from schema edits made in the PocketBase admin UI - is a separate feature and is enabled **only under `go run`**. A compiled production binary never automigrates, so admin-UI schema changes there are not captured in code.
3. Applied migrations are tracked in the `_migrations` system table.
4. Environment variables read **inside** a migration only take effect on the **first** migration run against a given database. Changing them later does not re-apply them - this is the usual explanation for "I set the variable and nothing happened".
5. Schema changes require a new migration file, named `<unix-timestamp>_snake_description.go`, registered with `m.Register(up, down)` and written to be idempotent.

---

## Next Steps

- [Architecture](architecture.md) - System architecture
- [Backend Setup](backend-setup.md) - Configure database
