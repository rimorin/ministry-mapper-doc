# Data Models & Schema

## Overview

Ministry Mapper uses SQLite via PocketBase with a hierarchical data model designed for multi-tenant congregation management. This document provides comprehensive documentation of all data models, relationships, and business rules.

## Entity Relationship Diagram

```
Congregation (Root entity - multi-tenant isolation)
    │
    ├─── Territory (1:M)
    │      │
    │      └─── Map (1:M)
    │             │
    │             ├─── Address (1:M)
    │             ├─── Assignment (1:M)
    │             └─── Message (1:M)
    │
    ├─── Option (1:M)
    │      └─── Address (M:M via JSON array)
    │
    ├─── User (1:M via Role)
    │      │
    │      ├─── Role (1:M)
    │      └─── Assignment (1:M)
    │
    └─── Message (1:M)
```

## Core Collections

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
| `max_tries` | number | Yes | Max "not home" attempts before marking done |
| `origin` | select | Yes | Country/region (sg, my) |
| `timezone` | select | Yes | Timezone for date calculations |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

**Access Rules:**
- **List:** User must have role in congregation
- **View:** User with role OR link-based access
- **Update:** Administrator only
- **Delete:** Administrator only

**Business Rules:**
- Controls default assignment duration for all maps
- `max_tries` determines when "not home" addresses count as done
- Timezone affects date-based calculations and reports

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
| `progress` | number | Auto | Completion percentage (0-100) |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

**Indexes:**
- `(congregation, code)` - Fast lookup by congregation
- `(congregation)` - Congregation filter

**Relationships:**
- 1:M with Maps
- 1:M with Addresses (denormalized for performance)

**Access Rules:**
- **List:** User with role in congregation
- **View:** User with role OR link access
- **Create/Update/Delete:** Conductor or Administrator

**Progress Calculation:**
Automatically calculated from aggregate of all maps within territory

**Example:**
```json
{
  "id": "terr_abc",
  "congregation": "cong_123",
  "code": "1A",
  "description": "Downtown District",
  "progress": 65.5
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
| `territory` | relation | Yes | FK to Territories (cascade delete) |
| `congregation` | relation | Yes | FK to Congregations (cascade delete) |
| `sequence` | number | Auto | Order within territory (auto-increment) |
| `code` | text | Yes | Map identifier/number (auto: "0") |
| `description` | text | No | Location description |
| `type` | select | Yes | `single` or `multi` (floor type) |
| `coordinates` | JSON | No | Geographic location `{lat, lng}` |
| `aggregates` | JSON | Auto | Cached statistics (max 2MB) |
| `progress` | number | Auto | Completion percentage |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

**Indexes:**
- `(territory, code)` - Primary lookup
- `(territory, sequence)` - Ordering
- `(territory)` - Territory filter

**Cascade Deletes:**
- Addresses
- Assignments
- Messages

**Aggregates Structure:**
```json
{
  "done": 45,
  "not_done": 30,
  "dnc": 5,
  "invalid": 2,
  "not_home_max_tries": 8
}
```

**Map Types:**
- **single**: Private addresses (houses)
- **multi**: Public addresses (multi-floor buildings)

**Access Rules:**
- **List:** User with role OR link access
- **View:** User with role OR link access
- **Create/Update:** Conductor or Administrator
- **Delete:** Administrator only

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
    "done": 20,
    "not_done": 10,
    "dnc": 2,
    "invalid": 1,
    "not_home_max_tries": 3
  },
  "progress": 66.7
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
| `map` | relation | Yes | FK to Maps (cascade delete) |
| `congregation` | relation | Yes | FK to Congregations (denormalized) |
| `territory` | relation | Yes | FK to Territories (denormalized) |
| `floor` | number | Yes | Floor number (0-based) |
| `code` | text | Yes | Unit/door code (e.g., "101", "A") |
| `sequence` | number | Yes | Order within map |
| `status` | select | Yes | Address visit status |
| `not_home_tries` | number | Auto | Counter for "not home" attempts |
| `type` | relation | No | M:M to Options (JSON array) |
| `notes` | text | No | Field worker notes |
| `last_notes_updated` | date | Auto | When notes last changed (via hook) |
| `last_notes_updated_by` | text | Auto | Username of last updater (via hook) |
| `dnc_time` | date | No | When marked "do not call" |
| `coordinates` | JSON | No | Fine-grained GPS `{lat, lng}` |
| `updated_by` | text | No | Track who made updates |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

**Status Values:**
- `not_done` - Not yet visited (initial state)
- `done` - Successfully completed
- `not_home` - No one home (can retry)
- `do_not_call` - Excluded from work
- `invalid` - Address doesn't exist

**Indexes (7 total):**
- `(map)` - Map filter
- `(map, status)` - Status distribution queries
- `(map, code)` - Code lookup
- `(map, floor)` - Floor organization
- `(type, congregation)` - Option distribution
- `(congregation, last_notes_updated_by)` - Recent notes by user
- `(territory, status)` - Territory-wide status

**Auto Hooks:**
- Updates trigger `last_notes_updated` and `last_notes_updated_by` fields
- Triggers aggregate recalculation on map
- Updates territory progress

**Access Rules:**
- **List:** User with role OR link access
- **View:** User with role OR link access
- **Create:** Conductor or Administrator
- **Update:** User with role OR link access (publishers can update)
- **Delete:** Administrator only

**Example:**
```json
{
  "id": "addr_123",
  "map": "map_xyz",
  "congregation": "cong_123",
  "territory": "terr_abc",
  "floor": 2,
  "code": "201",
  "sequence": 5,
  "status": "not_home",
  "not_home_tries": 2,
  "type": ["opt_residential"],
  "notes": "Try after 6pm",
  "last_notes_updated": "2025-12-19T14:30:00Z",
  "last_notes_updated_by": "john.doe",
  "coordinates": {"lat": 1.3521, "lng": 103.8198}
}
```

---

### 5. Users

**Purpose:** System user accounts with authentication

**Type:** Auth collection (PocketBase user-auth-type)

**System Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | text(15) | Yes | Auto-generated unique ID |
| `email` | email | Yes | Login identity (unique) |
| `emailVisibility` | bool | Yes | Whether email is public |
| `verified` | bool | Auto | Email verification status |
| `password` | password | Yes | Bcrypt hashed (cost: 10, min 6 chars) |
| `tokenKey` | text | Hidden | Token generation key (30-60 chars) |

**Custom Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | text | Yes | Display name (2-50 chars) |
| `disabled` | bool | No | Account disabled flag |
| `last_login` | date | Auto | Last authentication timestamp (via hook) |

**Name Validation Pattern:**
`^[A-Za-z][\w\s\.\-']*$` (starts with letter, allows word chars, spaces, dots, hyphens, apostrophes)

**Autogen Pattern:**
`user[0-9]{5}[A-Za-z]` (e.g., user12345A)

**Indexes:**
- `email` (unique) - Fast auth lookup
- `tokenKey` (unique) - Token validation

**Auth Configuration:**
- **Password Auth:** Enabled (min 6 chars)
- **OAuth2:** Google PKCE flow
  - Maps `name` from OAuth provider
  - Auto-creates user on first login
- **MFA:** Enabled (duration: 1800s) - Disabled for OAuth2
- **OTP:** Enabled (duration: 180s, length: 4 digits)
- **Verify Email:** Duration 604800s (7 days)
- **Password Reset:** Duration 1800s (30 min)
- **Auth Alert:** Enabled (notifies on login from new location)

**Access Rules:**
- **List:** Administrators only
- **View:** Administrators or self
- **Create:** Public (OAuth or explicit registration)
- **Update:** Self only
- **Delete:** Administrators only

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
  "last_login": "2025-12-19T14:30:00Z"
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
| `role` | select | Yes | Permission level |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

**Role Values:**
- `read_only` - View only access
- `conductor` - Can manage assignments and maps
- `administrator` - Full access

**Indexes:**
- `(user, congregation)` - Composite key (one role per user per congregation)
- `(user)` - User's roles
- `(congregation, role)` - Role distribution
- `(user, congregation, role)` - Full composite lookup

**Access Rules:**
- **Create/Update/Delete:** Administrators only
- **List:** User's own roles
- **View:** User's own roles

**Relationships:**
- M:N between Users and Congregations

**Business Rules:**
- One role per user per congregation
- Role determines permissions for all actions

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
| `id` | text(20) | Yes | **Serves as anonymous access token** |
| `congregation` | relation | Yes | FK to Congregations (cascade delete) |
| `map` | relation | Yes | FK to Maps (cascade delete) |
| `user` | relation | No | Publisher user (optional) |
| `type` | select | Yes | Assignment classification |
| `expiry_date` | date | Yes | Automatic expiry timestamp |
| `publisher` | text | No | Publisher name/identifier |
| `created` | date | Auto | Creation timestamp |
| `updated` | date | Auto | Last update timestamp |

**Assignment Types:**
- `normal` - Regular territory work
- `personal` - Personal ministry territory

**Indexes:**
- `(map)` - Active assignments per map
- `(expiry_date)` - For background cleanup job
- `(user, created)` - User assignment history
- `(map, expiry_date)` - Expiry check per map

**Access Rules:**
- **Create/Delete:** Conductor or Administrator
- **View:** User with role OR link_id matching this ID + not expired
- **List:** User with role

**Workflow:**
1. Conductor creates via `/territory/link` endpoint
2. Publisher receives 20-char `link_id` (this assignment's ID)
3. Publisher passes `link_id` via HTTP header `@request.headers.link_id`
4. PocketBase validates: link_id matches assignment.id AND expiry_date > now
5. Background job deletes expired assignments every 5 minutes

**Example:**
```json
{
  "id": "asgn_1234567890abcdef",
  "congregation": "cong_123",
  "map": "map_xyz",
  "user": "user_abc",
  "type": "normal",
  "expiry_date": "2025-12-20T14:30:00Z",
  "publisher": "John Doe"
}
```

---

### 8. Options

**Purpose:** Address type classifications (customizable per congregation)

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
| `sequence` | number | Yes | Display/sort order |
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
- Countable options (`is_countable=true`) included in progress % calculation
- Non-countable options tracked separately but excluded from progress
- When option deleted, addresses get default option added automatically

**Access Rules:**
- **List/View:** User with role OR link access
- **Create/Update/Delete:** Use `/options/update` endpoint (Conductor or Administrator)

**Relationships:**
- M:M with Addresses (stored as JSON array in address.type field)

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
| `map` | relation | Yes | FK to Maps (cascade delete) |
| `congregation` | relation | Yes | FK to Congregations (cascade delete) |
| `message` | text | Yes | Message content |
| `created_by` | text | Yes | Username of creator |
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
- **Create:** User with role OR link access
- **Update:** Administrators only
- **List:** User with role OR link access

**Background Processing:**
- Every 30 min: Unread messages sent to administrators via email
- Every 30 min: Pinned admin messages sent to publishers via email
- Every 1 hour: Note updates sent to administrators

**Example:**
```json
{
  "id": "msg_abc",
  "map": "map_xyz",
  "congregation": "cong_123",
  "message": "Please revisit unit 205",
  "created_by": "john.doe",
  "read": false,
  "pinned": false,
  "type": "publisher"
}
```

---

## Database Technology

**Engine:** SQLite via PocketBase (modernc.org/sqlite v1.40.2+)

**Features:**
- Pure Go implementation (CGo-free) - enables cross-platform compilation
- Based on C SQLite transpiled to Go using ccgo/v4
- Actively maintained with regular security updates (SQLite 3.50+ aligned)

**SQL Features Used:**
- JSON fields for flexible data (coordinates, aggregates)
- Composite indexes for query optimization
- Transactions for ACID compliance
- CTEs (Common Table Expressions) for complex aggregations

**Advantages:**
- Self-contained file-based database
- No separate database service needed
- Perfect for self-hosted deployments
- No CGO dependency enables static binary builds
- Proven stability with security patches

---

## Key Relationships

| Relationship | Cardinality | Cascade Behavior |
|--------------|-------------|------------------|
| Congregation → Territory | 1:M | Delete cascade |
| Territory → Map | 1:M | Delete cascade |
| Map → Address | 1:M | Delete cascade |
| Map → Assignment | 1:M | Delete cascade |
| Map → Message | 1:M | Delete cascade |
| Address → Options | M:M | JSON array |
| User → Role | M:M | Through roles table |
| User → Assignment | 1:M | Keep assignments |
| Congregation → Option | 1:M | Delete cascade |
| Congregation → Role | M:M | Through roles table |

---

## Business Rules

### Progress Calculation

**Formula:**
```
completed = (done + not_home_max_tries) / total_countable * 100
```

**Countable Addresses:**
- Only addresses with `is_countable=true` options
- Excludes DNC and invalid addresses

**Completion Criteria:**
- Status = `done`, OR
- Status = `not_home` AND `not_home_tries >= congregation.max_tries`

### Address Lifecycle

```
not_done → not_home (retry attempts) → done
        → do_not_call (excluded)
        → invalid (excluded)
```

### Auto-Update Triggers

1. **Address Update:**
   - Updates `last_notes_updated` if notes changed
   - Sets `last_notes_updated_by` to current user
   - Triggers aggregate recalculation
   - Updates map progress
   - Updates territory progress

2. **Map Operations:**
   - Recalculates aggregates when addresses change
   - Updates territory progress

3. **Assignment Creation:**
   - Sets expiry based on congregation settings
   - Generates unique 20-char ID as access token

---

## Migration & Schema Updates

**Schema File:** `migrations/1764846700_collections_snapshot.go`

**Size:** 55,459 lines (generated by PocketBase)

**Migration Process:**
1. PocketBase automatically applies migrations on startup
2. Migrations tracked in `_migrations` system collection
3. Schema changes require new migration files

---

## Next Steps

- [Architecture](architecture.md) - System architecture
- [Backend Setup](backend-setup.md) - Configure database
