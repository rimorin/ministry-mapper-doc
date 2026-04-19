# Features Overview

## Introduction

Ministry Mapper is a comprehensive digital territory management platform designed for religious congregations. This page provides a detailed overview of all features and capabilities.

---

## 🗺️ Territory Management

### Organize Geographic Regions

**Create and Manage Territories:**
- Create unlimited territories per congregation
- Assign unique codes and descriptions
- Organize territories by priority with drag-and-drop sequencing
- Track completion percentage automatically
- Reset territory status to restart tracking
- Move maps between territories easily

**Territory Statistics:**
- Aggregate completion tracking
- Visual progress indicators
- Real-time update of statistics
- Per-territory breakdowns
- Historical tracking

**Use Case:** Divide your congregation's service area into manageable territories for organized field service work.

---

## 🏢 Map & Address Management

### Interactive Location Tracking

**Map Types:**

**1. Public Addresses (Multi-floor Buildings)**
- Support for multi-story buildings
- Add/remove floors dynamically
- Each floor contains multiple units
- Automatic address code application to all floors
- Floor-by-floor organization

**2. Private Addresses (Single-story Residences)**
- Individual houses or standalone properties
- Simpler structure without floor subdivision
- Quick address entry

**Map Features:**
- Attach GPS coordinates for precise location
- Add detailed descriptions
- Sequence numbers for organized visiting
- Visual map integration with Leaflet/OpenStreetMap
- Get directions to any address
- Geolocation support

**Address Operations:**
- Add/remove address codes
- Resequence addresses for optimal routing
- Bulk operations for efficiency
- Copy addresses across floors
- Rename and reorganize

**Add Address On The Fly** *(v1.33+)*
- Publishers can add missing addresses directly during the mapping process — no admin intervention required
- A **"+"** card appears at the end of the address list as a quick-add entry point
- Ideal for congregations that are still building out their territory records

![The "+" card at the end of an address list for adding a missing address](../assets/screenshots/add_more_add.png)

---

## 📊 Unit/Household Tracking

### Comprehensive Visit Status Management

**Status Types:**

| Status | Description | Impact on Progress |
|--------|-------------|-------------------|
| **Not Done** | Initial state, not yet visited | Counted in incomplete |
| **Done** | Successfully completed | Counted as complete |
| **Not Home** | No one home, can retry | Counted after max tries |
| **Do Not Call (DNC)** | Household requests no visits | Excluded from counting |
| **Invalid** | Unit doesn't exist | Excluded from counting |

**Not Home Tracking:**
- Configurable maximum tries per congregation
- Automatic counter increment
- Treated as "done" after max attempts
- Prevents indefinite revisits

**Completion Logic:**
```
Complete when:
  - Status = Done, OR
  - Status = Not Home AND tries >= max_tries

Progress % = Completed / Total Countable Units × 100
```

**Household Options:**
- Customizable address classifications
- Countable vs non-countable types
- Default options for new addresses
- Multiple options per address
- Affects progress calculations

**Visit Notes:**
- Add detailed notes per address
- Automatic timestamp tracking
- Track who made updates
- Email notifications for note changes
- Historical note viewing

---

## 🔗 Assignment System (Publisher Links)

### Secure, Time-Limited Access

**Link Features:**
- **Time-Limited:** Configurable expiry (default 24 hours)
- **Token-Based:** Secure 20-character access tokens
- **No Account Needed:** Publishers access via link only
- **Two Types:**
  - Normal assignments for regular territory work
  - Personal assignments for personal ministry

**Intelligent Assignment Algorithm:**

When requesting a new assignment, the system:

1. **Primary Factor:** Selects map with fewest active assignments
   - Prevents overloading single maps
   - Balances workload

2. **Secondary Factor:** Prefers geographically closer maps
   - Uses GPS coordinates
   - 50-meter threshold
   - Reduces travel time

3. **Tertiary Factor:** Prioritizes lower completion progress
   - Ensures balanced territory completion
   - Prevents some areas being neglected

**Assignment Workflow:**
```
1. Conductor generates assignment link
2. Link shared with publisher (QR, email, text)
3. Publisher accesses /map/:assignmentId
4. System validates token and expiry
5. Publisher updates address status in real-time
6. Link expires automatically
7. Admin receives real-time updates
```

**Assignment Management:**
- View active assignments per map
- See who has which territory
- Track assignment history
- Manual assignment deletion
- Automatic cleanup of expired links (every 5 minutes)

---

## 👥 User Management & Access Control

### Role-Based Permission System

**Four User Roles:**

#### 1. Publisher (Most Restricted)
**Access Method:** Time-limited assignment links only

**Permissions:**
- ✅ View assigned map only
- ✅ Update address/unit status
- ✅ Add visit notes
- ✅ Send feedback messages
- ❌ Cannot see other territories
- ❌ Cannot create territories
- ❌ Cannot manage users

**Use Case:** Field service publishers working territories

---

#### 2. Read-Only
**Access Method:** User account with read-only role

**Permissions:**
- ✅ View all congregation data
- ✅ View territories and maps
- ✅ View address information
- ✅ View messages
- ❌ Cannot modify anything
- ❌ Cannot create assignments
- ❌ Cannot manage users

**Use Case:** Observers, auditors, service committee members

---

#### 3. Conductor (Mid-level)
**Access Method:** User account with conductor role

**Permissions:**
- ✅ All Read-Only permissions
- ✅ Create and manage territories
- ✅ Create and manage maps
- ✅ Add/update addresses
- ✅ Generate publisher assignment links
- ✅ View all congregation data
- ✅ Manage household options
- ✅ Reset territories
- ❌ Cannot manage user roles
- ❌ Cannot configure congregation settings
- ❌ Cannot delete users

**Use Case:** Field service overseers, territory coordinators

---

#### 4. Administrator (Full Access)
**Access Method:** User account with administrator role

**Permissions:**
- ✅ All Conductor permissions
- ✅ Create and manage users
- ✅ Assign user roles
- ✅ Configure congregation settings
- ✅ Manage household options
- ✅ Delete territories and maps
- ✅ Delete user accounts
- ✅ View all assignments and messages
- ✅ Configure maximum not-home tries
- ✅ Set link expiry duration
- ✅ Access system logs

**Use Case:** Territory servants, system administrators

---

### User Account Management

**Features:**
- Email-based authentication
- Email verification required
- Password reset functionality
- OAuth2 Google authentication
- Multi-factor authentication (MFA) support
- One-time password (OTP) support
- Account disable/enable
- Last login tracking
- Multi-congregation support (different roles per congregation)

---

## 🛠️ Congregation Settings

### Customizable Configuration

**Core Settings:**

**1. Maximum Not-Home Tries**
- Set how many "not home" visits before considering done
- Typical: 2-3 tries
- Affects progress calculations
- Congregation-specific

**2. Assignment Link Expiry**
- Configure how long links remain valid
- Default: 24 hours
- Customizable per congregation
- Automatic cleanup of expired links

**3. Household Options**
- Create custom address classifications
- Examples: "Residential", "Business", "High-rise", "Language: Spanish"
- Mark options as "countable" (affects completion %)
- Set default option for new addresses
- Reorder with drag-and-drop
- View which addresses have each option

**4. Regional Settings**
- Timezone configuration
- Country/region selection
- Affects date calculations and reports

---

## 💬 Communication & Messaging

### In-App Communication System

**Message Types:**

**1. Publisher Messages**
- From publishers to conductors/admins
- Attached to specific maps
- Territory-specific feedback
- Real-time delivery

**2. Conductor Messages**
- Administrative communications
- Map-specific instructions
- Coordination messages

**3. Admin Messages**
- Can be pinned for visibility
- Read status tracking
- Important announcements
- Broadcast to publishers

**4. Pinned Messages**
- Remain visible until unpinned
- Read tracking per user
- Auto-email to relevant users
- Priority display

**Email Notifications:**

Automated email digests sent for:
- **Every 30 minutes:** Unread messages to administrators
- **Every 30 minutes:** Pinned admin messages to publishers
- **Every 1 hour:** Note updates to administrators
- **Monthly:** Congregation reports with Excel attachment

**Email Templates:**
- Professional HTML templates
- Mobile-responsive design
- Includes relevant links
- Customizable content

---

## 🔄 Real-Time Collaboration

### Live Data Synchronization

**Real-Time Features:**

**SSE Subscriptions:**
- Live updates via Server-Sent Events (SSE)
- Automatic reconnection on disconnect
- Low latency (<100ms typical)

**Concurrent Editing:**
- Multiple users can work simultaneously
- No conflicts or data loss
- Last-write-wins strategy
- Timestamp-based resolution

**Automatic Refresh:**
- Data updates immediately when changed
- No manual page refresh needed
- Visual indicators for updates
- Optimistic UI updates

**Visibility Detection:**
- Automatically refreshes when tab becomes visible
- Saves bandwidth when tab is hidden
- Prevents stale data

**Collaboration Scenarios:**
- Admin creates territory while conductor viewing list → Instantly appears
- Publisher marks units complete → Admin sees progress update live
- Multiple conductors managing different territories → No conflicts
- Real-time statistics updates as work progresses

---

## 📱 Progressive Web App (PWA)

### Native App Experience

**PWA Capabilities:**

**Installable:**
- Add to home screen (mobile)
- Install as desktop app
- Appears in app launcher
- Full-screen mode
- Custom app icon

**Desktop Installation:**
```
Visit URL → Browser menu → "Install app"
```

**Mobile Installation:**
```
Visit URL → Share → "Add to home screen"
```

**Offline Support:**
- Static assets cached via service worker
- Continue viewing cached data when offline
- Automatic sync when connection restored
- Background updates

**Performance Benefits:**
- Faster load times (cached assets)
- Reduced data usage
- Works on slow connections
- Smooth animations

**Native Features:**
- Push notifications (planned)
- Background sync
- Native file handling
- Share API integration

---

## 🌍 Internationalization

### Multi-Language Support

**Supported Languages:**
1. **English** (en) - Primary language
2. **Spanish** (es) - Español
3. **Indonesian** (id) - Bahasa Indonesia
4. **Japanese** (ja) - 日本語
5. **Korean** (ko) - 한국어
6. **Malay** (ms) - Bahasa Melayu
7. **Chinese** (zh) - 中文

**i18n Features:**
- Language selector in navigation
- Persistent preference (localStorage)
- Browser language detection
- Complete UI translation
- Dynamic switching (no reload)
- Easy to add new languages

**Localized Release Notes:**
- In-app changelog displayed in the user's currently selected language
- Supports all 8 languages
- A modal is shown automatically when a new application version is detected, so publishers always know what's new without leaving the app

**Adding New Language:**
1. Create translation file in `src/i18n/locales/[lang].json`
2. Copy structure from `en.json`
3. Translate all strings
4. Register in `src/i18n/index.ts`

---

## 🌙 Dark Mode

### System-Aware Theming

**Theme Options:**
- **Light Mode:** Traditional light theme
- **Dark Mode:** Eye-friendly dark theme
- **System:** Follows OS preference

**Benefits:**
- Reduced eye strain
- Better battery life (OLED screens)
- Accessibility compliance
- Modern user experience

**Implementation:**
- CSS custom properties
- Smooth transitions
- Persistent preference
- No flash of wrong theme

---

## 📈 Reporting & Analytics

### Comprehensive Statistics

**Monthly Congregation Reports:**
- **Schedule:** 1st of each month at midnight
- **Format:** Excel workbook (.xlsx)
- **Delivery:** Email to all administrators

**Report Contents:**

**1. Congregation Overview**
- Congregation name and details
- Reporting period
- Overall statistics

**2. Territory Breakdown**
- Completion percentage per territory
- Number of maps per territory
- Address distribution
- Progress over time

**3. Address Statistics**
- Total addresses by type
- Status distribution (done, not done, etc.)
- Countable vs non-countable breakdown
- DNC and invalid counts

**4. Publisher Assignments**
- Active assignments count
- Assignment history
- Territory coverage

**5. Progress Tracking**
- Month-over-month comparison
- Trends and insights
- Areas needing attention

**Excel Features:**
- Professional formatting
- Charts and graphs
- Pivot-ready data
- Print-friendly layout

**Real-Time Dashboard:**
- Live statistics on admin panel
- Territory completion tracking
- Address status distribution
- Active assignment monitoring
- Recent activity feed

**AI-Generated Report Summaries:**
- Monthly congregation reports can include AI-generated summaries of key trends drawn from territory notes and messages
- Powered by **OpenAI gpt-4o-mini**
- Controlled by the `enable-report-ai-summary` feature flag — opt-in per deployment so congregations choose whether to enable it
- Requires an `OPENAI_API_KEY` to be configured in the deployment environment

---

## 🔐 Security Features

### Enterprise-Grade Security

**Authentication Methods:**

**1. Email/Password**
- Bcrypt password hashing (cost: 10)
- Minimum 6 characters
- Email verification required
- Password reset via email

**2. OAuth2 (Google)**
- PKCE flow for security
- Automatic user creation
- No password management needed
- Secure token handling

**3. One-Time Password (OTP)**
- Optional 4-digit code
- 180-second validity
- Additional security layer
- Email delivery

**4. Multi-Factor Authentication (MFA)**
- TOTP support
- Enhanced security for admins
- 1800-second session
- Backup codes

**Access Control:**

**Role-Based Access Control (RBAC):**
- Fine-grained permissions
- Congregation-level isolation
- Multi-tenant architecture
- Secure by default

**Link-Based Access:**
- Temporary anonymous access
- Token-based authentication
- Automatic expiration
- Revocable access

**Security Best Practices:**
- HTTPS enforcement
- CORS configuration
- Rate limiting
- SQL injection prevention (parameterized queries)
- XSS protection
- CSRF protection
- Input validation
- Output sanitization

**Audit Trail:**
- User action logging
- Change tracking
- Who updated what when
- IP address logging
- Authentication attempts

**User Lifecycle Management:**

Automated handling of inactive and unprovisioned user accounts, aligned with **NIST AC-2** and **AC-2(3)** security controls:

- **Unprovisioned Users:** Accounts that have never completed setup receive multi-stage warning emails before being automatically disabled and ultimately deleted
- **Inactive Users:** Accounts with no login activity for a configurable period are warned, then disabled
- Background jobs run daily to evaluate and enforce lifecycle policies — no manual intervention needed
- Keeps congregation user lists accurate and minimises the attack surface from dormant accounts

**Error Tracking:**
- Sentry integration
- Real-time error monitoring
- Stack traces for debugging
- Release tracking
- Performance monitoring

---

## 🗺️ Interactive Mapping

### Leaflet + OpenStreetMap Integration

**Mapping Features:**

**Base Features:**
- Interactive pan and zoom
- Street-level detail
- Satellite imagery option
- Custom markers for addresses
- Territory boundary visualization
- Cluster markers for dense areas

**Navigation:**
- Get directions to any address
- Integration with device maps app
- Distance calculation
- Optimal route planning
- GPS location support

**Routing Service (Turn-by-Turn Directions):**
- Built-in routing powered by **OpenRouteService**
- Supports three travel modes: **driving**, **walking**, and **cycling**
- Publishers can request directions to any address directly from the map view
- Address geocoding handled by **LocationIQ**

![Routing panel showing travel mode options and a plotted route on the map](../assets/screenshots/map_routing.png)

**Geolocation:**
- Detect current location
- Center map on user
- Proximity-based assignment
- Distance to territories

**Custom Markers:**
- Status-based color coding
- Custom icons
- Info popups
- Click interactions
- Marker clustering

**Performance:**
- Lazy loading of tiles
- Efficient marker rendering
- Viewport-based loading
- Mobile-optimized

**Advantages:**
- No API limits
- Free to use
- No billing required
- OpenStreetMap data
- Full offline tile caching possible

---

## 📧 Email System

### Automated Email Notifications

**Email Service:** MailerSend API v1

**Email Types:**

**1. Message Notifications**
- Schedule: Every 30 minutes
- Recipients: Administrators
- Content: Unread publisher messages
- Template: Professional HTML

**2. Admin Instructions**
- Schedule: Every 30 minutes
- Recipients: Publishers (non-admin users)
- Content: Pinned administrator messages
- Template: Instruction format

**3. Note Updates**
- Schedule: Every 1 hour
- Recipients: Administrators
- Content: Address note changes
- Template: Note digest format

**4. Monthly Reports**
- Schedule: 1st of month
- Recipients: Administrators
- Content: Statistical report with optional AI-generated territory summaries
- Attachment: Excel workbook
- AI Summary: When enabled via `enable-report-ai-summary` feature flag and `OPENAI_API_KEY` is configured, each territory sheet includes an AI-generated overview of key trends and notable observations

**5. System Emails**
- Email verification
- Password reset
- Account alerts
- Authentication notifications

**Template Features:**
- Mobile-responsive HTML
- Professional design
- Inline CSS for compatibility
- Plain text fallback
- Unsubscribe links
- Tracking pixels (optional)

---

## 🔄 Background Jobs

### Automated Task Processing

**Job Scheduler:** Cron-based with LaunchDarkly feature flags

**Scheduled Jobs:**

| Job | Schedule | Purpose |
|-----|----------|---------|
| Assignment Cleanup | Every 5 min | Delete expired assignments |
| Territory Aggregates | Every 10 min | Update progress statistics |
| Message Processing | Every 30 min | Send message notifications |
| Instructions | Every 30 min | Send admin messages |
| Note Updates | Every 1 hour | Notify of note changes |
| Monthly Reports | 1st of month | Generate Excel reports (with optional AI summaries) |
| Unprovisioned Users | Daily 01:00 UTC | Enforce user lifecycle (warnings → disable → delete) |
| Inactive Users | Daily 01:30 UTC | Warn and disable inactive accounts |

**Feature Flag Control:**
- Enable/disable jobs without deployment
- Gradual rollout capabilities
- A/B testing
- Emergency off switch
- Per-congregation control

**Job Monitoring:**
- Execution logs
- Success/failure tracking
- Duration metrics
- Error alerting
- Retry logic

---

## 🎨 User Interface

### Modern, Responsive Design

**UI Framework:** Bootstrap 5.3.8 + Custom Components

**Design Principles:**
- Mobile-first approach
- Touch-friendly interactions
- Accessibility compliant (WCAG 2.1)
- Consistent visual language
- Intuitive navigation

**Key UI Elements:**

**Navigation:**
- Responsive navbar
- Congregation selector
- Speed dial (floating action button)
- Breadcrumb navigation
- Quick links

**Forms:**
- Inline validation
- Error messaging
- Auto-save drafts
- Keyboard shortcuts
- Progressive disclosure

**Tables:**
- Sortable columns
- Inline editing
- Bulk actions
- Pagination
- Search and filter
- Export capabilities

**Modals:**
- 24+ specialized modals
- Keyboard navigation
- Focus management
- Stacking support
- Mobile-optimized

**Feedback:**
- Toast notifications
- Loading indicators
- Progress bars
- Success confirmations
- Error alerts

**Responsive Breakpoints:**
- Mobile: <768px
- Tablet: 768px-1024px
- Desktop: >1024px
- Large desktop: >1440px

---

## 🚀 Performance

### Optimized for Speed

**Frontend Optimizations:**
- Code splitting by route
- Lazy loading components
- Image optimization
- CSS minification
- JavaScript bundling
- Tree shaking
- Service worker caching

**React 19 Compiler:**
- Automatic memoization
- No manual optimization needed
- Smaller bundle size
- Faster rendering

**Backend Optimizations:**
- Database indexing (25+ indexes)
- Query optimization
- Connection pooling
- Aggregate caching
- Transaction batching
- Gzip compression

**Caching Strategy:**
- Browser caching (static assets)
- Service worker (offline assets)
- CDN edge caching
- Database query caching

**Performance Targets:**
- **First Contentful Paint:** <1.5s
- **Time to Interactive:** <3s
- **Lighthouse Score:** >90
- **Core Web Vitals:** All green

---

## 🔌 API & Integration

### RESTful API + SSE

**API Features:**
- RESTful endpoints
- JSON payloads
- JWT authentication
- Pagination support
- Filtering and sorting
- Rate limiting
- CORS support

**SSE (Server-Sent Events):**
- Real-time subscriptions
- Automatic reconnection
- Low latency
- Event-driven updates

**Integration Options:**
- PocketBase SDK (JavaScript/TypeScript)
- Direct HTTP requests
- SSE client
- OAuth2 integration
- Webhook support (planned)

---

## 📊 Data Management

### Robust Data Layer

**Database:** SQLite via PocketBase

**Features:**
- ACID transactions
- Foreign key constraints
- Cascade deletes
- JSON field support
- Full-text search
- Automatic backups

**Data Import/Export:**
- Excel export (reports)
- CSV export (planned)
- JSON export via API
- Backup/restore

**Data Integrity:**
- Referential integrity
- Validation rules
- Unique constraints
- Check constraints
- Auto-generated IDs

---

## 🎯 Future Roadmap

### Planned Features

**Near Term (Q1-Q2 2025):**
- Push notifications
- CSV import/export
- Advanced filtering
- Custom reports
- Batch operations
- Print layouts

**Medium Term (Q3-Q4 2025):**
- Mobile apps (iOS/Android)
- Webhook integrations
- Custom fields
- Advanced analytics
- Team collaboration tools
- Integration marketplace

**Long Term (2026+):**
- AI-powered insights
- Predictive analytics
- Voice commands
- Augmented reality navigation
- Blockchain audit trail
- Multi-org hierarchies

---

## 📚 Documentation & Support

### Comprehensive Resources

**Documentation:**
- [Getting Started](getting-started.md)
- [User Guide](user-guide.md)
- [Architecture](architecture.md)
- [Deployment Guide](deployment.md)
- [FAQ](faq.md)

**Support Channels:**
- GitHub Issues
- Community Forums
- Email Support (hosted service)
- Documentation Site
- Video Tutorials (planned)

---

## ✅ Conclusion

Ministry Mapper provides a complete, modern solution for digital territory management with:

- ✅ Comprehensive territory and address management
- ✅ Real-time collaboration
- ✅ Secure, role-based access control
- ✅ Multi-language support
- ✅ Progressive web app capabilities
- ✅ Automated reporting and notifications
- ✅ Interactive mapping
- ✅ Enterprise-grade security
- ✅ Open-source and self-hostable
- ✅ Active development and support

**Ready to get started?**
- [Quick Start Guide](getting-started.md)
- [View Demo](https://ministry-mapper.com)
- [Self-Host Instructions](deployment.md)
