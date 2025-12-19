# Welcome to Ministry Mapper

## What is Ministry Mapper?

Ministry Mapper is a modern, open-source web application that helps congregations manage their field ministry territories efficiently. Instead of using paper territory slips, Ministry Mapper provides a comprehensive digital solution for organizing, tracking, and managing field service work from any device with an internet connection.

## Our Story

We started Ministry Mapper in 2022 as a simple project to help our local congregation manage territories more effectively. As more congregations discovered the benefits of digital territory management, we continued to enhance the platform. Today, Ministry Mapper is used by congregations around the world, helping thousands of publishers stay organized in their ministry work with features like real-time synchronization, interactive mapping, and multi-language support.

## What Ministry Mapper Does

Ministry Mapper provides comprehensive territory management capabilities:

### Core Features

- **🗺️ Territory Management**: Organize geographic regions into territories with automatic completion tracking
- **🏢 Interactive Mapping**: Leaflet-based maps with OpenStreetMap integration, directions, and geolocation
- **📱 Real-time Collaboration**: Live data synchronization across all devices via WebSocket
- **🌍 Multi-language Support**: 7 languages (English, Indonesian, Japanese, Korean, Malay, Tamil, Chinese)
- **👥 Role-based Access**: Publisher, Conductor, Administrator, and Read-only roles with fine-grained permissions
- **🔗 Assignment Links**: Secure, time-limited sharing links for territory access without user accounts
- **📊 Progress Tracking**: Automatic completion status with visual indicators and statistics
- **✉️ Email Notifications**: HTML email templates for messages, notes, and monthly reports
- **📈 Excel Reporting**: Monthly congregation reports with detailed statistics
- **🔐 Multi-factor Authentication**: OTP, MFA, and OAuth2 (Google) support
- **🌙 Dark Mode**: System-aware theming with light/dark/system options
- **📲 Progressive Web App**: Installable on mobile and desktop with offline asset caching

### For Different Users

- **Territory Servants**: Manage all territory records digitally with comprehensive statistics and reporting
- **Field Service Conductors**: Quickly assign territories with intelligent algorithms and track publisher activity
- **Publishers**: Access assigned maps via simple links, update address status in real-time, and collaborate effectively
- **Administrators**: Full system control with user management, congregation settings, and audit trails

## Problems Ministry Mapper Solves

### Going Paperless

Traditional paper territory slips get lost, damaged, or become hard to read over time. Ministry Mapper keeps everything digital and safe in the cloud. You can access your territories from your phone, tablet, or computer anytime.

### Better Organization

When territories are shared between multiple publishers, it's easy to accidentally work the same addresses. Ministry Mapper helps everyone see what's happening in real-time, so you don't waste time covering the same ground.

### Instant Updates

When someone updates a territory, everyone sees the changes right away. No waiting for papers to be passed around or meetings to share updates.

### Easier Record Keeping

Instead of manually writing down every change on paper slips, Ministry Mapper automatically tracks everything. This saves hours of work and prevents mistakes.

## How Ministry Mapper Works

### Modern Technology Stack

Ministry Mapper leverages cutting-edge web technologies for optimal performance:

**Frontend (ministry-mapper-v2):**

- **React 19.2.3**: Latest UI library with automatic compiler optimization
- **TypeScript 5.9.3**: Type-safe development for fewer bugs
- **Vite 7.3.0**: Lightning-fast build tool and dev server
- **Leaflet 1.9.4**: Interactive mapping with OpenStreetMap (no API limits)
- **Bootstrap 5.3.8**: Modern, responsive UI framework
- **Wouter 3.9.0**: Lightweight routing (3KB vs 40KB alternatives)

**Backend (ministry-mapper-be):**

- **Go 1.24.7**: High-performance compiled language
- **PocketBase 0.34.2**: Self-hosted backend-as-a-service with SQLite
- **SQLite 1.40.2+**: Reliable, self-contained database (pure Go implementation)
- **Echo v5**: Minimalist, high-performance web framework
- **Sentry 0.40.0**: Real-time error tracking and monitoring
- **MailerSend 1.6.2**: Transactional email service
- **LaunchDarkly 7.14.2**: Feature flag management
- **Excelize 2.10.0**: Excel report generation

### Architecture Highlights

- **Self-Hosted**: No vendor lock-in, complete control over your data
- **Multi-Tenant**: Congregation-level data isolation for security
- **Real-Time Sync**: WebSocket-based live updates across all clients
- **Progressive Web App**: Installable on all devices with offline asset support
- **Docker Containerization**: Easy deployment and scaling
- **API-First Design**: RESTful API with comprehensive PocketBase SDK support

## Important Considerations

### Privacy First

Ministry Mapper tracks residential addresses, which means you need to follow privacy laws in your area. Different countries have different rules (like GDPR in Europe or CCPA in California). Make sure you understand and follow your local regulations before using Ministry Mapper.

### Internet Required

Ministry Mapper works over the internet, so you'll need a connection to use it. If your area has unreliable internet, this might cause challenges. The app does work well on mobile data if WiFi isn't available.

### Google Maps Availability

Ministry Mapper uses Google Maps to show territories. In some countries, Google Maps may not work properly due to local restrictions. Check if Google Maps works in your area before setting up Ministry Mapper.

### Learning Curve

Moving from paper to digital is a change, especially for those less comfortable with technology. We provide help and support to make the transition easier for everyone in your congregation.

## Getting Started

### For Users

The easiest way to use Ministry Mapper is through our hosted service:

**[ministry-mapper.com](https://ministry-mapper.com)**

Simply:

1. Visit [ministry-mapper.com](https://ministry-mapper.com)
2. Create an account using the sign-up page
3. Verify your email address
4. Contact your congregation's administrator for proper permissions

**Quick Links:**

- **[Get Started →](getting-started.md)**
- **[Features Overview →](features.md)**
- **[User Guide →](user-guide.md)**
- **[FAQ →](faq.md)**

### For Administrators

If you're considering Ministry Mapper for your congregation:

- **Recommended**: Use our hosted service at [ministry-mapper.com](https://ministry-mapper.com)

  - No technical setup required
  - Professional support available
  - Automatic updates and maintenance
  - Better reliability and security

- **Alternative**: Review the [Deployment Guide](deployment.md) (advanced users only)

We don't encourage self-hosting due to the technical complexity and ongoing maintenance requirements. The hosted service at ministry-mapper.com provides a simpler, more reliable solution.

**Administrator Resources:**

- **[Getting Started Guide →](getting-started.md)**
- **[Deployment Options →](deployment.md)**
- **[Self-Hosting Guide →](self-hosting.md)**

### For Developers

If you're interested in the technical aspects or contributing:

**Technical Documentation:**

- **[Architecture Overview →](architecture.md)** - System design and technology decisions
- **[Data Models & Schema →](data-models.md)** - Database structure and relationships
- **[Backend Setup →](backend-setup.md)** - PocketBase configuration
- **[Frontend Setup →](frontend-setup.md)** - React application setup

**Development:**

- **Frontend Repository:** [github.com/rimorin/ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2)
- **Backend Repository:** [github.com/rimorin/ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)
- **Issues:** GitHub Issues on respective repositories
- **Contributing:** See repository CONTRIBUTING.md files
