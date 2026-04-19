# Architecture Overview

## System Architecture

Ministry Mapper consists of two main components working together:

### High-Level Architecture Diagram

```
┌──────────────────────────────────────────────────────────────────┐
│                     Client Applications                           │
│         (Web UI, Mobile Apps, Third-party Integrations)          │
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
│  │ - Map Management (9 endpoints)                              │ │
│  │ - Territory Management (2 endpoints)                        │ │
│  │ - Configuration (1 endpoint)                                │ │
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
│  │ - Scheduled tasks (cron-based)                             │ │
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

## Technology Stack

### Backend Stack

| Layer | Technology | Version | Purpose |
|-------|-----------|---------|---------|
| **Language** | Go | 1.25.0 | High-performance backend |
| **Backend Framework** | PocketBase | 0.36.9 | BaaS with SQLite |
| **Database** | SQLite | v1.40.2+ | Self-contained database |
| **Web Framework** | Echo | v5 | HTTP routing |
| **Container** | Docker | Latest | Containerization |
| **Error Tracking** | Sentry | v0.43.0 | Error monitoring |
| **Email Service** | MailerSend | v1.6.3 | Email API |
| **Feature Flags** | LaunchDarkly | v7.14.6 | Feature management |
| **Report Generation** | Excelize | v2.10.1 | Excel reports |
| **AI Summaries** | OpenAI GPT | v3.29.0 | Intelligent report & message summaries |

### Frontend Stack

| Layer | Technology | Version | Purpose |
|-------|-----------|---------|---------|
| **Runtime** | Node.js | >=24.0.0 | JavaScript runtime (dev toolchain) |
| **Framework** | React | 19.2.4 | UI library |
| **Language** | TypeScript | 5.9.3 | Type safety |
| **Build Tool** | Vite | 8.0.2 | Fast dev & build |
| **Routing** | Wouter | 3.9.0 | Lightweight routing |
| **State Management** | Context API | - | Global state |
| **UI Framework** | Bootstrap | 5.3.8 | CSS framework |
| **Mapping** | Leaflet | 1.9.4 | Interactive maps |
| **Testing** | Vitest | 4.1.1 | Unit testing |
| **Code Quality** | ESLint + Prettier | Latest | Linting & formatting |

## External Integrations

### Third-Party Services

| Service | Purpose | Notes |
|---------|---------|-------|
| **LaunchDarkly** | Feature flags & controlled rollouts | Controls all 9 background jobs; enables zero-downtime flag toggles |
| **OpenAI (gpt-5.4-mini)** | AI-generated report summaries | Optional; temperature 0.3 for factual output; feature-flagged |
| **MailerSend** | Transactional email delivery | Used for reports and lifecycle notification emails; 3-attempt retry |
| **Umami** | Privacy-friendly analytics | Optional; 13 custom event types tracked |
| **OpenRouteService** | Routing/directions API | Driving, walking, and cycling route calculations |
| **LocationIQ** | Geocoding API | Address coordinate lookup |

## Data Architecture

### Entity Relationship Model

The system organizes data hierarchically with congregation-level isolation:

```
Congregation (Organization boundary)
    ├── Territory (Geographic region)
    │   ├── Map (Building/Location)
    │   │   └── Address (Individual unit)
    │   └── Option (Address type classification)
    ├── User (System user)
    │   └── Role (Permission assignment)
    ├── Assignment (Publisher access)
    └── Message (Notification)
```

### Core Collections

**1. Congregations** - Organization settings and boundaries
- Controls multi-tenant isolation
- Defines assignment duration and retry limits
- Regional settings (timezone, country)

**2. Territories** - Geographic divisions
- Contains multiple maps
- Tracks aggregate completion percentage
- Organizes field service areas

**3. Maps** - Specific locations (buildings)
- Single or multi-floor structures
- Geographic coordinates
- Progress tracking and statistics

**4. Addresses** - Individual units/households
- Status tracking (not done, done, not home, DNC)
- Visit notes and history
- Floor and unit information

**5. Users** - System accounts
- Email/password or OAuth2 authentication
- Multi-factor authentication support
- Congregation-specific roles

**6. Roles** - Permission assignments
- Three levels: read_only, conductor, administrator
- Congregation-specific permissions
- Role-based access control

**7. Assignments** - Publisher access tokens
- Time-limited access links
- Anonymous field worker access
- Automatic expiration

**8. Options** - Address classifications
- Customizable per congregation
- Countable vs non-countable types
- Affects progress calculations

**9. Messages** - Communications
- Publisher feedback
- Admin instructions
- Email notifications

## Frontend Architecture

### Component Organization Pattern

Ministry Mapper uses a **feature-based component organization**:

```
Pages (Routes)
  ├── Admin Dashboard (/)
  ├── Publisher Map (/map/:id)
  ├── User Management (/usermgmt)
  └── Front Page (/)

Components (Reusable UI)
  ├── Form Components (14 components)
  ├── Modal Components (24+ modals)
  ├── Navigation Components (28 components)
  ├── Table Components (9 components)
  ├── Map Components (7 components)
  ├── Middleware/Providers (5 components)
  └── Static Pages (8 components)

Business Logic (Custom Hooks)
  ├── useTerritoryManagement
  ├── useMapManagement
  ├── useCongManagement
  ├── useModalManagement
  ├── useRealtimeSubscription
  └── [4 more...]

Utilities & Types
  ├── TypeScript Interfaces
  ├── Constants
  ├── Helper Functions (30+)
  ├── PocketBase Integration
  ├── Authorization Policies
  └── i18n Configuration
```

### State Management Strategy

**Four-Layer Approach:**

1. **Global Context** - Theme, language, app mode
2. **Custom Hooks** - Domain-specific logic and data
3. **Local Component State** - UI toggles and forms
4. **Persistent State** - LocalStorage for preferences

**Benefits:**
- No Redux overhead
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
PocketBase Utils (CRUD operations)
    ↓
PocketBase Backend (permissions, database)
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
| **Aggregate Caching** | Batched cron (10 min / 11 min lookback) | Pre-calculated territory progress statistics |
| **Custom Hooks** | Business logic | Reusable logic |
| **Provider Pattern** | Context providers | Global state |
| **Lazy Loading** | Route splitting | Performance |
| **Policy Pattern** | Authorization | Security rules |

## Security Architecture

### Authentication Methods

1. **Email/Password** - Standard authentication with bcrypt hashing
2. **OAuth2 (Google)** - Social login with PKCE flow
3. **One-Time Password (OTP)** - Additional security layer
4. **Multi-Factor Authentication (MFA)** - Enhanced security for admins

### Authorization Model

**Role-Based Access Control (RBAC):**

| Role | Permissions | Use Case |
|------|-------------|----------|
| **read_only** | View data only | Observers, auditors |
| **conductor** | Create assignments, manage maps | Field coordinators |
| **administrator** | Full access | System administrators |

### Access Control Patterns

**Pattern 1: Authenticated User with Role**
```
User must be logged in
+ User has role in roles collection
+ Role is for specific congregation
```

**Pattern 2: Link-Based Access**
```
Assignment ID passed in header
+ Assignment exists and not expired
+ Assignment belongs to requested map
```

**Pattern 3: Congregation Isolation**
```
Every entity has congregation foreign key
+ Access rules filter by congregation
+ Prevents cross-congregation data access
```

## Real-Time Synchronization

### SSE Architecture

- **PocketBase Real-time API**: Server-Sent Events (SSE)
- **Automatic Subscriptions**: Components subscribe to collections
- **Live Updates**: Changes broadcast to all connected clients
- **Conflict Resolution**: Timestamp-based resolution
- **Visibility Detection**: Auto-refresh when tab becomes active

### Subscription Pattern

```typescript
// Components subscribe to data changes
useRealtimeSubscription('addresses', (data) => {
  // UI updates automatically
});
```

## Background Jobs

### Scheduled Tasks

| Job | Schedule | Flag | Description |
|-----|----------|------|-------------|
| `cleanUpAssignments` | Every 5 min | `enable-assignments-cleanup` | Remove expired map assignments |
| `updateTerritoryAggregates` | Every 10 min | `enable-territory-aggregations` | Recalculate territory progress |
| `processMessages` | Every 30 min | `enable-message-processing` | Process pending publisher messages |
| `processInstructions` | Every 30 min | `enable-instruction-processing` | Process territory assignment instructions |
| `processNotes` | Hourly | `enable-note-processing` | Process updated congregation notes |
| `generateMonthlyReport` | 1st of month @ 02:00 SGT | `enable-monthly-report` | Generate & email Excel report |
| `processUnprovisionedUsers` | Daily @ 02:00 SGT | `enable-unprovisioned-user-processing` | Warn/disable users with no role |
| `processInactiveUsers` | Daily @ 02:30 SGT | `enable-inactive-user-processing` | Warn/disable inactive accounts |
| `processNewAddresses` | Daily @ 03:00 SGT | `enable-new-addresses-notification` | **NEW** — Daily digest email for app-created addresses |

### Feature Flag Control

All background jobs controlled by LaunchDarkly feature flags:
- Enable/disable jobs without deployment
- Gradual rollout capabilities
- Emergency off switch

### AI/LLM Integration

Ministry Mapper integrates with OpenAI's GPT model for intelligent text summarisation. This is an **optional** feature gated by the `enable-report-ai-summary` LaunchDarkly flag.

**Model**: GPT (latest available via `openai-go` SDK v3.29.0)  
**Temperature**: 0.3 (factual, deterministic output)  
**Response Format**: JSON mode for structured data

**Use Cases:**

| Context | Job | Output |
|---------|-----|--------|
| Monthly Excel reports | `generateMonthlyReport` | Per-territory `summary` and `key_points` |
| Publisher message digests | `processMessages` | `overview` and `key_themes` |
| Congregation notes digests | `processNotes` | `overview` and `key_themes` |

**Graceful Degradation**: If `OPENAI_API_KEY` is not set or the `enable-report-ai-summary` flag is off, the system falls back to reports and emails without AI-generated content — no errors are raised.



### Frontend Optimizations

- **Code Splitting**: Lazy-loaded routes
- **Bundle Optimization**: Tree shaking, minification
- **React 19 Compiler**: Automatic memoization
- **PWA Caching**: Service worker for static assets
- **Virtualized Lists**: React-window for large datasets

### Backend Optimizations

- **Database Indexes**: 25+ indexes for fast queries
- **Aggregate Caching**: Territory progress statistics are pre-calculated via a batched cron job (`updateTerritoryAggregates`, every 10 minutes with an 11-minute lookback window), replacing the previous real-time debouncer approach. Results are stored as JSON for fast retrieval.
- **Connection Pooling**: Efficient database connections
- **Transaction Batching**: Reduce database round-trips

## Deployment Architecture

### Docker Containerization

**Backend:**
- Multi-stage Docker build
- Alpine base for small footprint
- Volume mounts for data persistence
- Health checks for reliability

**Frontend:**
- Static build served by CDN
- Edge caching for fast delivery
- Progressive Web App capabilities

### Infrastructure Options

1. **Hosted Service** (Recommended)
   - ministry-mapper.com
   - Managed infrastructure
   - Automatic updates

2. **Self-Hosted**
   - Railway, Render, DigitalOcean
   - Requires technical expertise
   - Full control

## Monitoring & Observability

### Error Tracking

- **Sentry Integration**: Both frontend and backend
- **Error Categorization**: By severity and type
- **Source Maps**: Debugging minified code
- **Release Tracking**: Version-specific errors

### Logging

- **Backend**: Structured JSON logs
- **Frontend**: Console logging in development
- **Production**: Errors sent to Sentry
- **Audit Trail**: User actions tracked

## Scalability Considerations

### Current Limitations

- SQLite single-writer limitation
- Suitable for small to medium congregations (< 1000 concurrent users)
- Vertical scaling (more powerful server)

### Future Scalability

- PocketBase supports read replicas
- Can migrate to PostgreSQL if needed
- CDN caching for static assets
- Edge functions for API optimization

## Technology Decisions

### Why These Technologies?

**React 19**: Latest features, automatic optimization, smaller bundle

**TypeScript**: Type safety, better IDE support, fewer runtime errors

**Vite**: Fast dev server, instant HMR, optimized builds

**PocketBase**: Self-hosted, no vendor lock-in, easy to maintain

**Wouter**: Lightweight (3KB vs 40KB for React Router)

**Bootstrap**: Battle-tested, comprehensive components, good accessibility

**Leaflet**: Free maps, no API limits, lightweight

**SQLite**: Self-contained, no separate database server, perfect for self-hosting

## Next Steps

- [Backend Setup](backend-setup.md) - Configure the backend
- [Frontend Setup](frontend-setup.md) - Deploy the frontend
- [Security Guide](security.md) - Security best practices
