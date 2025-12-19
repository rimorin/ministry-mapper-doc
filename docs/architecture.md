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
| **Language** | Go | 1.24.7 | High-performance backend |
| **Backend Framework** | PocketBase | 0.34.2 | BaaS with SQLite |
| **Database** | SQLite | v1.40.2+ | Self-contained database |
| **Web Framework** | Echo | v5 | HTTP routing |
| **Container** | Docker | Latest | Containerization |
| **Error Tracking** | Sentry | v0.40.0 | Error monitoring |
| **Email Service** | MailerSend | v1.6.2 | Email API |
| **Feature Flags** | LaunchDarkly | v7.14.2 | Feature management |
| **Report Generation** | Excelize | v2.10.0 | Excel reports |

### Frontend Stack

| Layer | Technology | Version | Purpose |
|-------|-----------|---------|---------|
| **Framework** | React | 19.2.3 | UI library |
| **Language** | TypeScript | 5.9.3 | Type safety |
| **Build Tool** | Vite | 7.3.0 | Fast dev & build |
| **Routing** | Wouter | 3.9.0 | Lightweight routing |
| **State Management** | Context API | - | Global state |
| **UI Framework** | Bootstrap | 5.3.8 | CSS framework |
| **Mapping** | Leaflet | 1.9.4 | Interactive maps |
| **Testing** | Vitest | 4.0.16 | Unit testing |
| **Code Quality** | ESLint + Prettier | Latest | Linting & formatting |

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
Real-time Subscription (WebSocket)
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
| **Aggregate Caching** | JSON fields | Fast calculations |
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

### WebSocket Architecture

- **PocketBase Real-time API**: Server-Sent Events (SSE) or WebSocket
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

| Job | Schedule | Purpose |
|-----|----------|---------|
| **Assignment Cleanup** | Every 5 min | Delete expired assignments |
| **Territory Aggregates** | Every 15 min | Update progress statistics |
| **Message Processing** | Every 30 min | Send message notifications |
| **Instructions** | Every 30 min | Send admin messages |
| **Note Updates** | Every 1 hour | Notify of note changes |
| **Monthly Reports** | 1st of month | Generate Excel reports |

### Feature Flag Control

All background jobs controlled by LaunchDarkly feature flags:
- Enable/disable jobs without deployment
- Gradual rollout capabilities
- Emergency off switch

## Performance Optimizations

### Frontend Optimizations

- **Code Splitting**: Lazy-loaded routes
- **Bundle Optimization**: Tree shaking, minification
- **React 19 Compiler**: Automatic memoization
- **PWA Caching**: Service worker for static assets
- **Virtualized Lists**: React-window for large datasets

### Backend Optimizations

- **Database Indexes**: 25+ indexes for fast queries
- **Aggregate Caching**: Pre-calculated statistics in JSON
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
