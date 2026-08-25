# Architecture Overview

## System Architecture

Ministry Mapper consists of two main components working together:

### High-Level Architecture Diagram

```
┌──────────────────────────────────────────────────────────────────┐
│                     Client Applications                           │
│         (Web UI, Installable PWA, PocketBase SDK clients)        │
└──────────────────────────────┬───────────────────────────────────┘
                               │
                    HTTP REST API (Port 8080)
                               │
┌──────────────────────────────▼───────────────────────────────────┐
│                    PocketBase Application                         │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ API Layer (internal/handlers/)                              │ │
│  │ - Public / link-scoped routes (4 POST)                      │ │
│  │ - Map & floor management (10 POST)                          │ │
│  │ - Territory management (3 POST)                             │ │
│  │ - Options & report generation (2 POST)                      │ │
│  │ - Database health probe (1 GET)                             │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Middleware Layer                                            │ │
│  │ - Authentication & Authorization                            │ │
│  │ - Error Capture (Sentry)                                    │ │
│  │ - Request Logging                                           │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Background Jobs (internal/jobs/)                            │ │
│  │ - 8 scheduled tasks (cron-based, UTC)                      │ │
│  │ - Feature flag control (LaunchDarkly)                      │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Built-in Services                                           │ │
│  │ - Authentication (Email/Password, OAuth2, OTP, MFA)        │ │
│  │ - Real-time Events (SSE)                                   │ │
│  │ - Admin Dashboard                                          │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
└────────────┬──────────────────────┬────────────────┬──────────────┘
             │                      │                │
             │                      │                │
    ┌────────▼──────┐      ┌────────▼──────┐  ┌─────▼────────┐
    │   SQLite       │      │  Sentry       │  │MailerSend    │
    │   Database     │      │  (Error Track)│  │(Email)       │
    │   (/app/pb_data)│      │               │  │              │
    └────────────────┘      └────────────────┘  └──────────────┘
```

Every custom route is a `POST` except `GET /api/db-health`, the unauthenticated
database health probe used by the hosting platform. Collection CRUD is served by
PocketBase's own generated REST API at `/api/collections/{collection}/records`.

## Technology Stack

### Backend Stack

| Layer | Technology | Version | Purpose |
|-------|-----------|---------|---------|
| **Language** | Go | 1.27 | Compiled backend |
| **Backend Framework** | PocketBase | v0.40.0 | BaaS with SQLite, auth, realtime and admin UI |
| **HTTP Routing** | PocketBase `router` package | bundled | PocketBase dropped Echo at v0.23; routing is now built in |
| **Database Driver** | `modernc.org/sqlite` | v1.57.0 | Pure-Go SQLite driver — no C compiler, which is why the image builds with `CGO_ENABLED=0` |
| **Query Builder** | pocketbase/dbx | v1.12.0 | Hand-written SQL for aggregates and scope checks |
| **Validation** | ozzo-validation | v4.3.0 | Request payload validation |
| **CLI** | Cobra | v1.10.2 | Console commands (`serve`, `fix-sequences`) |
| **Container** | Docker | golang:1.27.0-alpine → alpine:3.24 | Multi-stage build, ~73.6 MB final image |
| **Error Tracking** | sentry-go | v0.48.0 | Error and log forwarding |
| **Email Service** | mailersend-go | v1.6.6 | Digest and report email |
| **Feature Flags** | LaunchDarkly go-server-sdk | v7.15.6 | Job and feature gating |
| **Report Generation** | Excelize | v2.11.0 | Excel reports |
| **AI Summaries** | openai-go | v3.52.0 | SDK version — the model is named under External Integrations |

### Frontend Stack

Application version **2.7.2**.

| Layer | Technology | Version | Purpose |
|-------|-----------|---------|---------|
| **Runtime** | Node.js | >=24.0.0 | Dev toolchain (CI pins Node 24) |
| **Framework** | React | 19.2.8 | UI library, with the React Compiler enabled |
| **Language** | TypeScript | 6.0.3 | Type safety, `strict: true` |
| **Build Tool** | Vite | 8.2.0 | Rolldown-based dev server and bundler |
| **Routing** | wouter | 3.10.0 | Minimal hook-based router |
| **State Management** | React hooks + narrow contexts | - | No state library at all — no Redux, no Zustand, no TanStack Query |
| **Styling** | Tailwind CSS | 4.3.3 | CSS-first configuration; there is no `tailwind.config` |
| **Component Layer** | shadcn/ui on Base UI (`@base-ui/react`) | 1.7.0 | `components.json` style `base-vega`; no Bootstrap, no Radix |
| **Icons** | lucide-react | 1.33.0 | Icon set |
| **Animation** | motion | 13.1.0 | The Framer Motion successor |
| **Forms** | react-hook-form | 7.85.0 | Form state, no schema resolver |
| **Mapping** | Leaflet + react-leaflet | 1.9.4 / 5.0.0 | Interactive maps |
| **Virtualization** | react-window | 2.3.0 | Long map and address lists |
| **Drag & Drop** | @dnd-kit | core 6.3.1 | Reordering options and map sequences |
| **Internationalization** | i18next + react-i18next | 26.3.6 / 17.0.11 | 8 in-app languages |
| **Offline Storage** | idb (IndexedDB) | 8.0.3 | Address cache and the offline write queue |
| **Backend SDK** | PocketBase JS SDK | 0.27.3 | REST + SSE client |
| **Error Tracking** | @sentry/react | 10.70.0 | Error monitoring |
| **PWA** | vite-plugin-pwa | 1.3.0 | Service worker and install support |
| **Testing** | Vitest | 4.1.11 | Unit tests (jsdom, v8 coverage) |
| **Code Quality** | ESLint + Prettier | 10.8.1 / 3.9.6 | Linting & formatting |

## External Integrations

### Third-Party Services

| Service | Purpose | Notes |
|---------|---------|-------|
| **LaunchDarkly** | Feature flags & controlled rollouts | Gates the 8 background jobs plus a ninth, non-job flag for AI summaries; the job flags default to **enabled** when LaunchDarkly is unconfigured, while `enable-report-ai-summary` defaults to **disabled** |
| **OpenAI (gpt-5.4-mini)** | AI-generated report and digest summaries | Optional; Chat Completions in JSON mode, temperature 0.3, 90-second timeout; requires both `OPENAI_API_KEY` and the LaunchDarkly flag |
| **MailerSend** | Transactional email delivery | Reports, digests and account-lifecycle mail; report sends retry 3 times with backoff |
| **Sentry** | Error tracking | Both stacks; the backend also mirrors PocketBase log rows into Sentry |
| **Umami** | Privacy-friendly analytics | Optional; 23 custom event types. `VITE_UMAMI_SRC_URL` **and** `VITE_UMAMI_WEBSITE_ID` must both be set or initialization returns early; `VITE_UMAMI_DOMAINS` is optional |
| **Geoapify** | Map tiles, geocoding and routing | The only geo provider. One key, `VITE_GEOAPIFY_API_KEY`, covers `osm-carto` tiles (retina-aware, max zoom 20), address autocomplete, and walking/driving routes |
| **Google OAuth2** | Social sign-in | Requires `GOOGLE_CLIENT_ID` and `GOOGLE_CLIENT_SECRET` on the backend |

## Data Architecture

### Entity Relationship Model

The system organizes data hierarchically with congregation-level isolation:

```
Congregation (Organization boundary)
    ├── Territory (Geographic region)
    │   └── Map (Building/Location)
    │       ├── Address (Individual unit)
    │       │   └── Address Option (Household type, junction row)
    │       ├── Assignment (Publisher link)
    │       └── Message (Feedback / pinned instruction)
    ├── Option (Household type classification)
    ├── User (System user)
    │   └── Role (One per user, per congregation)
    └── Audit logs (Addresses Log, Assignments Log, Roles Log)
```

### Core Collections

The whole schema lives in one authoritative snapshot migration, applied with
`deleteMissing` enabled, plus a later migration that adds the two newest audit-log
collections. That is **23 collections**: 13 application collections, 5 read-only
analytics views, and PocketBase's own 5 system collections.

**1. Congregations** - Organization settings and boundaries
- Controls multi-tenant isolation
- Defines assignment link duration (`expiry_hours`) and the not-home retry limit (`max_tries`)
- Regional settings: `timezone` (20 IANA zones) and `origin` (13 country codes)

**2. Territories** - Geographic divisions
- Contains multiple maps
- Stores a boundary polygon and an aggregate completion percentage
- Progress is summed from its maps' aggregates, never from an address scan

**3. Maps** - Specific locations (buildings)
- `type` is `single` (landed houses) or `multi` (floors and units)
- Optional `coordinates` for map view and proximity sorting
- Holds the `aggregates` JSON progress snapshot and an integer `progress`

**4. Addresses** - Individual units/households
- Status tracking: `not_done`, `done`, `not_home`, `do_not_call`, `invalid`
- `not_home_tries`, visit notes, floor, unit code and column `sequence`
- `source` records how the row was created: `app`, `admin`, `map_init`, `floor_copy`
- Location is a single `coordinates` JSON column (`{"lat": …, "lng": …}` or null) — there are no separate latitude/longitude fields

**5. Address Options** - Household types per address
- A junction collection between addresses and options
- Only addresses carrying at least one countable option count towards progress

**6. Options** - Household type classifications
- Customizable per congregation, with a display sequence and one default
- Countable vs non-countable types
- Affects progress calculations

**7. Users** - System accounts
- Email/password or OAuth2 (Google) authentication
- Email OTP and MFA supported by PocketBase
- `last_login` plus the account-lifecycle warning stamps

**8. Roles** - Permission assignments
- One row per user, per congregation
- Exactly three values: `read_only`, `conductor`, `administrator`
- Publisher is **not** a role — see Security Architecture

**9. Assignments** - Publisher access tokens
- The record id is a 25-character token and *is* the shareable link
- Scoped to exactly one map, with an expiry date
- Cleaned up by a background job that logs each expiry

**10. Messages** - Communications
- `type` is `publisher`, `conductor` or `administrator`
- Publisher feedback, plus pinned administrator instructions
- Drives the message and instruction digest emails

**11. Addresses Log** - Address status audit trail
- One row per status change, including a not-home retry increment
- Superuser-only; feeds the daily-status analytics view

**12. Assignments Log** - Assignment audit trail
- Actions: `assigned`, `unassigned`, `expired`
- Records the map, user, publisher name, type and expiry date

**13. Roles Log** - Role audit trail
- Actions: `granted`, `changed`, `revoked`, with the old and new role
- `changed_by` is empty for superuser actions, which have no user record

**Analytics views** - Five read-only SQL views, superuser-only: `analytics_maps`,
`analytics_territories`, `analytics_daily_status`, `analytics_not_home` and
`analytics_user_audit`. They back the monthly report and its AI summary.

**PocketBase system collections** - `_superusers`, `_mfas`, `_otps`,
`_externalAuths` and `_authOrigins`, managed by PocketBase itself.

## Frontend Architecture

### Component Organization Pattern

Ministry Mapper uses a **feature-based component organization**. Counts below are
as of application version 2.7.2:

```
Pages (wouter routes)
  ├── /              Front page: sign-in, verification, or the admin app
  ├── /signup        Account creation
  ├── /forgot        Password reset request
  ├── /map/:id       Publisher territory slip (link token, no login)
  ├── /usermgmt      Email verification and password reset landing
  ├── /auth/callback OAuth2 redirect target
  └── *              Not found

Components (src/components/)
  ├── ui/          33 shadcn primitives built on Base UI
  ├── navigation/  30 components
  ├── modal/       24 dialogs, sheets and flows
  ├── form/        15 components
  ├── statics/     13 static pages and placeholders
  ├── map/          8 Leaflet components
  ├── table/        8 address grid components
  ├── middlewares/  6 providers and gates
  └── common/       4 shared wrappers

Business Logic (src/hooks/) — 28 hooks, including
  ├── useTerritoryManagement
  ├── useMapManagement
  ├── useCongManagement
  ├── useModalManagement
  ├── useRealtimeSubscription
  ├── useSmartSync
  └── useNetworkStatus

Utilities & Types (src/utils/)
  ├── interface.ts   TypeScript interfaces
  ├── constants.ts   Access levels, field projections, timers
  ├── helpers/       20 helper modules
  ├── pocketbase.ts  SDK wrapper with retry and cancellation keys
  ├── policies.ts    Authorization policies
  └── i18n/          8 locales, lazily loaded
```

### State Management Strategy

**Four-Layer Approach:**

1. **Narrow Contexts** - Theme, language, network status, release notes, offline sync
2. **Custom Hooks** - Domain-specific logic and data
3. **Local Component State** - UI toggles and forms
4. **Persistent State** - localStorage for preferences, IndexedDB for cached addresses and queued edits

**Benefits:**
- No state library at all, so no store to keep in sync
- Simpler data flow
- Better code splitting
- Easier testing

### Data Flow Pattern

```
User Action
    ↓
Component Event Handler
    ↓
Custom Hook (validation, state management)
    ↓
PocketBase Utils (CRUD, retry, cancellation)
    ↓
PocketBase Backend (permissions, hooks, database)
    ↓
Real-time Subscription (SSE)
    ↓
UI Auto-updates
```

## Key Design Patterns

| Pattern | Usage | Purpose |
|---------|-------|---------|
| **Middleware Wrapper** | Sentry integration | Centralized error tracking |
| **Event Hooks** | PocketBase lifecycle | Automatic updates |
| **Job Scheduler** | Cron with feature flags | Scheduled operations |
| **Transaction Pattern** | Database operations | Data consistency |
| **Link-Based Access** | Assignment tokens | Anonymous access |
| **Hook-Driven Aggregation** | `OnRecordAfterUpdateSuccess("addresses")` | Map and territory progress recalculated as writes land |
| **Suppression Flag** | `bulk_reset:<mapID>` in the app store | One recalculation per bulk operation instead of one per address |
| **Post-Query Pruning** | List request hooks | Results trimmed to the caller's server-derived scope |
| **Custom Hooks** | Business logic | Reusable logic |
| **Provider Pattern** | Context providers | Global state |
| **Lazy Loading** | Route splitting | Performance |
| **Policy Pattern** | Authorization | Security rules |

## Security Architecture

### Authentication Methods

1. **Email/Password** - Standard authentication with bcrypt hashing
2. **OAuth2 (Google)** - Social login, completed through a full-page redirect to `/auth/callback`
3. **One-Time Password (OTP)** - A 4-digit code, valid for 180 seconds
4. **Multi-Factor Authentication (MFA)** - PocketBase's email-OTP second factor; there is no TOTP and there are no backup codes

OTP and MFA are enabled in the collection snapshot, but the `PB_OTP_ENABLED` and
`PB_MFA_ENABLED` migration variables default to false and are compared against the
literal string `true`, so `TRUE` and `1` do not switch them on.

### Authorization Model

There are **three congregation roles plus one link-based access path**:

| Access | Permissions | Use Case |
|------|-------------|----------|
| **read_only** | View territories, maps, the address grid, directions and messages | Observers, auditors |
| **conductor** | Everything read-only, plus quick links, assignment links, and creating or updating addresses; may reset and delete territories | Field coordinators |
| **administrator** | Full access, including maps, floors, territories, congregation settings, household options, user roles and reports | Territory servants, system administrators |
| **Assignment link** | Read and update the addresses of exactly one map, until the link expires | Publishers, with no account and no login |

Publisher is not a role and has no `roles` row. A publisher is simply somebody
holding a valid assignment link, presented as a `link-id` header whose value is an
`assignments` record id. The frontend's internal access scale treats a link holder
at conductor-equivalent level for the address grid only; it is a UI gating scale,
not the schema.

Removing somebody's access means deleting their `roles` row — there is no
"no access" or "delete access" role.

### Access Control Patterns

**Pattern 1: Authenticated User with Role**
```
User must be logged in
+ User has role in roles collection
+ Role is for specific congregation
```

**Pattern 2: Link-Based Access**
```
Assignment ID passed in the link-id header
+ Assignment exists and not expired
+ Assignment belongs to requested map
```

**Pattern 3: Congregation Isolation**
```
Every entity has congregation foreign key
+ Access rules filter by congregation
+ Prevents cross-congregation data access
```

**Pattern 4: Link Precedence — a link-id beats a role**
```
Superuser            -> allow
Non-empty link-id    -> the link check alone decides
                        (a failed link is refused even when the same request
                         also carries a valid administrator token)
Otherwise            -> the role check decides
```

A link is a bearer credential handed to publishers. Falling back to the caller's
own role when the link is expired or points at the wrong map would silently widen
a link-scoped session's reach, so the link path never falls back. A link is scoped
to exactly one map, and a list or subscribe request naming more than one map id is
refused outright.

Two spellings of the header exist and both are honoured: Go reads `link-id`, while
PocketBase collection rules see `@request.headers.link_id`.

**Pattern 5: Filter Scoping — defence in depth on list and subscribe**
```
The client's filter must name only ids the caller may see
+ The returned rows are independently pruned to the caller's server-derived scope
+ A realtime filter must be provably unable to match anything outside that scope
```

Authorizing ids extracted from the client's own filter is not sufficient on its
own: a filter such as `map="myMap" || id!=""` names one authorized id, passes that
check, and then returns every row in the table. So results are pruned *after* the
query has run, and realtime filters are additionally evaluated against the
collection restricted to out-of-scope maps — if anything comes back, the
subscription is dropped. That check **fails closed**: an empty filter, an empty
allow-list, a parse error and a resolver error all count as an escape.

Related hardenings worth knowing about: moving a map to a territory in another
congregation is refused with a single generic `400 Invalid destination territory`
(so the response cannot be used to probe foreign territory ids), and minting a
territory quick link requires a role in that congregation.

Clients cannot supply `created_by` or `updated_by`. The server derives the actor:
the signed-in user's name, or, for link access, the assignment's publisher name.

## Real-Time Synchronization

### SSE Architecture

- **PocketBase Real-time API**: Server-Sent Events (SSE). There are no WebSockets anywhere
- **Subscription Requests**: sent as `POST /api/realtime`, validated by an `OnRealtimeSubscribeRequest` hook
- **Automatic Subscriptions**: Components subscribe to collections or to a single record id
- **Live Updates**: Changes broadcast to all connected clients, then batched by the client (100 ms) so a map reset does not re-render the grid per address
- **Conflict Resolution**: Timestamp-based resolution
- **Visibility Detection**: Auto-refresh and resubscribe when the tab becomes active
- **Reverse proxies**: SSE needs `proxy_buffering off;` in Nginx, or events queue up instead of streaming

**A subscription string is an SSE channel name, not configuration.** The server
broadcasts each event under the exact string it stored, and the browser's
`EventSource` listens under the string it sent. Rewriting either side publishes to
a channel nobody is listening on and every event is discarded silently, with no
error on either end. The authorization hook may therefore only **keep or drop** a
subscription — never rewrite it.

Only three collections are validated: `messages`, `addresses` and
`address_options`. Everything else, including `maps`, passes through verbatim.
That asymmetry explains a real past failure: while the hook still rewrote strings,
map-level realtime kept working and address-level realtime was silently dead.

Whether a write reaches subscribers is a deliberate choice of write path. Ordinary
saves and deletes fire hooks and realtime events; raw SQL suppresses them, which
is why territory cascade deletes and sequence repairs do not spam publishers who
are working a map.

Realtime subscriptions do not inherit the publisher header, so the frontend passes
`link-id` explicitly in the subscription's `headers` option.

### Subscription Pattern

```typescript
// Components subscribe to data changes
useRealtimeSubscription(
  "addresses",
  handleSubscription,
  {
    filter: `map="${mapId}"`,
    fields: PB_FIELDS.ADDRESSES_SUBSCRIPTION,
    // publisher access must pass the link header explicitly
    ...(assignmentId && { headers: { "link-id": assignmentId } })
  },
  [mapId, assignmentId],
  !!mapId,
  REALTIME_DEBOUNCE_MS
);
```

## Background Jobs

### Scheduled Tasks

Eight cron jobs run in the backend process. **All schedules are UTC.**

| Job | Cron (UTC) | Cadence | Flag | Description |
|-----|-----------|---------|------|-------------|
| `cleanUpAssignments` | `1,6,11,...,56 * * * *` | Every 5 min | `enable-assignments-cleanup` | Remove expired map assignments and log each expiry |
| `processMessages` | `8,38 * * * *` | Every 30 min | `enable-message-processing` | Email a publisher-feedback digest per congregation |
| `processInstructions` | `18,48 * * * *` | Every 30 min | `enable-instruction-processing` | Email pinned administrator instructions per map |
| `processNotes` | `28 * * * *` | Hourly | `enable-note-processing` | Email a digest of updated property notes |
| `generateMonthlyReport` | `0 18 1 * *` | Monthly, 1st | `enable-monthly-report` | Generate and email the Excel activity report |
| `processUnprovisionedUsers` | `0 18 * * *` | Daily | `enable-unprovisioned-user-processing` | Warn, then disable, then delete accounts with no role |
| `processInactiveUsers` | `30 18 * * *` | Daily | `enable-inactive-user-processing` | Warn, then disable inactive accounts |
| `processNewAddresses` | `0 19 * * *` | Daily | `enable-new-addresses-notification` | Digest email for addresses added from the app |

The daily and monthly jobs are deliberately staggered across 18:00-19:00 UTC,
which is 02:00-03:00 in Singapore — clear of the 08:00-12:00 local field service
peak, so nobody's email digest lands mid-ministry.

**There is no aggregate cron.** Map and territory progress used to be recalculated
by a batched job; that job was deleted. Progress is now recalculated as writes
land, by an `OnRecordAfterUpdateSuccess("addresses")` hook that fires only when
the status or the not-home try count actually changed. Bulk operations — a map or
territory reset — set a `bulk_reset:<mapID>` flag in the application store before
their transaction and clear it afterwards, so a 300-unit reset triggers one
recalculation instead of 300.

### Feature Flag Control

Background jobs are gated by LaunchDarkly feature flags:
- Enable/disable jobs without deployment
- Gradual rollout capabilities
- Emergency off switch

Flags are environment-scoped, not per-congregation. Their defaults are
deliberately asymmetric when LaunchDarkly is not configured at all:

| Flag group | Default without LaunchDarkly |
|------------|------------------------------|
| The 8 job flags | **Enabled** — a self-hoster with no LaunchDarkly account still gets every scheduled job |
| `enable-report-ai-summary` | **Disabled** — AI summaries stay off even when `OPENAI_API_KEY` is set |

### AI/LLM Integration

Ministry Mapper can use an OpenAI model to summarise text. This is an **optional**
feature that needs both `OPENAI_API_KEY` and the `enable-report-ai-summary`
LaunchDarkly flag.

**Model**: `gpt-5.4-mini` via the `openai-go` SDK  
**Request**: Chat Completions, JSON mode, temperature 0.3, 90-second timeout

**Use Cases:**

| Context | Job | Output |
|---------|-----|--------|
| Monthly and on-demand activity reports | `generateMonthlyReport`, `POST /report/generate` | `covered_activity`, `territory_analysis`, `conclusion` |
| Publisher message digests | `processMessages` | `overview` and `key_themes` |
| Property notes digests | `processNotes` | `overview` |
| Pinned instruction digests | `processInstructions` | `overview` |

The scheduled report covers the previous full calendar month; the on-demand report
covers a rolling 30 days.

**Privacy note**: when the feature is on, the text of publisher messages and
property notes is sent to OpenAI for summarisation.

**Graceful Degradation**: if the key is missing, the flag is off, the API errors,
or the response will not parse, the summary is simply marked unavailable and the
email omits that section. No errors are raised.

## Performance

### Frontend Optimizations

- **Code Splitting**: Lazy-loaded routes, plus explicit vendor chunk groups so React never rides along with a large UI chunk
- **Bundle Optimization**: Tree shaking, minification, hidden source maps
- **React 19 Compiler**: Automatic memoization; floor rows are kept referentially stable so a realtime update re-renders one floor, not the whole grid
- **PWA Caching**: Service worker for static assets, with translation chunks cached on demand rather than precached
- **Virtualized Lists**: react-window for large map lists
- **Cheap Compositing**: No always-on GPU layer promotion and no backdrop blur, both of which cost battery and scroll smoothness on phones
- **Payload Discipline**: Every list and subscription requests an explicit field projection

### Backend Optimizations

- **Database Indexes**: 29 indexes across the application collections, composite and ordered to match the actual query shapes. Six prefix-redundant indexes were dropped once a wider index already answered the query — verified against a copy of production carrying 1,160,985 addresses — which reclaimed roughly 27 MB and removed six index writes per address insert
- **Hook-Driven Aggregates**: Map progress is recalculated in a tracked background routine when an address status or retry count changes, and territory progress is summed from the maps' `aggregates` JSON rather than by scanning addresses
- **Bulk Suppression**: Resets recalculate once per map instead of once per address
- **Caching**: A congregation expiry cache in front of the quick-link path (invalidated by a congregation update hook, so admin-UI edits count too), PocketBase's cached collection lookups in hot loops, the report template parsed once, and package-level compiled regexes
- **Transaction Batching**: Reduce database round-trips; sequence renumbering is atomic

## Deployment Architecture

### Docker Containerization

**Backend:**
- Multi-stage Docker build with a persisted Go build cache
- `alpine:3.24` runtime base for a small footprint (~73.6 MB), built for `amd64`
- The image carries `tzdata` for congregation time zones and `curl` for the platform health check
- `pb_data/` is the entire application state and must be a persistent volume at `/app/pb_data`
- The health probe is `GET /api/db-health`, configured on the platform side

**Frontend:**
- Static build served by CDN
- Edge caching for fast delivery
- Progressive Web App capabilities, with three independent update paths

### Infrastructure Options

1. **Hosted Service** (Recommended)
   - ministry-mapper.com
   - Both stacks are deployed on **Coolify**, with Cloudflare in front
   - The frontend release workflow finishes by calling a Coolify deploy webhook
   - Managed infrastructure and automatic updates

2. **Self-Hosted**
   - Any Docker host: Coolify, Railway, Render, DigitalOcean and similar
   - Requires technical expertise
   - Full control

## Monitoring & Observability

### Error Tracking

- **Sentry Integration**: Both frontend and backend
- **Error Categorization**: By severity and type
- **Source Maps**: Uploaded at build time for debugging minified code
- **Release Tracking**: Version-specific errors, keyed to the deployed commit

### Logging

- **Backend**: Structured logs in PocketBase's `logs.db`, mirrored into Sentry
- **Frontend**: Console logging in development
- **Production**: Errors sent to Sentry
- **Audit Trail**: Address status changes, assignment grants and expiries, and role changes are written to three superuser-only log collections

## Scalability Considerations

### Current Limitations

- SQLite single-writer limitation
- Designed for small to medium congregations (< 1000 concurrent users)
- Vertical scaling only: a bigger server, not more of them

### Scaling Approach

PocketBase runs on SQLite and only SQLite. There are no read replicas and no
PostgreSQL migration path, so the honest scaling story is:

- **Scale up, not out** - more CPU, more RAM and faster disk on the single instance
- **Keep writes cheap** - the index work above exists to keep the single writer fast
- **Push reads to the edge** - CDN caching for the static frontend and its assets
- **Trim payloads** - explicit field projections and one-request page loads
- **Back up deliberately** - `pb_data/` is the whole state; PocketBase's built-in backups are the mechanism

## Technology Decisions

### Why These Technologies?

**React 19**: Latest features, automatic optimization via the React Compiler, smaller bundle

**TypeScript**: Type safety, better IDE support, fewer runtime errors

**Vite**: Fast dev server, instant HMR, optimized Rolldown builds

**PocketBase**: Self-hosted, no vendor lock-in, easy to maintain

**wouter**: A minimal hook-based router, far smaller than a full routing framework

**Tailwind CSS + shadcn/ui on Base UI**: Components live in the repository and can be edited directly, with accessible Base UI behaviour underneath

**Leaflet**: Free maps, no API limits, lightweight

**SQLite**: Self-contained, no separate database server, perfect for self-hosting

## Next Steps

- [Backend Setup](backend-setup.md) - Configure the backend
- [Frontend Setup](frontend-setup.md) - Deploy the frontend
