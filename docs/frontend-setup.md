# Frontend Setup Guide

!!! warning "Self-Hosting Not Recommended"
    This guide is for advanced users who want to self-host Ministry Mapper. We don't encourage self-hosting due to the technical complexity, maintenance burden, and security responsibilities involved.
    
    **Most users should**: Use our hosted service at **[ministry-mapper.com](https://ministry-mapper.com)**

!!! info "Looking for User Documentation?"
    If you're a regular user trying to learn how to use Ministry Mapper, see the [Getting Started](getting-started.md) or [User Guide](user-guide.md) instead.

## Overview

The Ministry Mapper frontend (ministry-mapper-v2) is a React-based web application that provides:

- User authentication and account management
- Interactive territory management interface
- Interactive mapping functionality
- Real-time data synchronization
- Mobile-responsive design
- Multi-language support (7 languages)
- Progressive Web App capabilities

**This guide assumes you have**:
- Experience with Node.js and npm
- Understanding of environment variables
- Familiarity with static site deployment
- Knowledge of web application security

For complete self-hosting instructions, see the [Self-Hosting Guide](self-hosting.md).

## Quick Reference

This page provides a quick reference for frontend configuration. **For complete setup instructions, see the [Self-Hosting Guide](self-hosting.md).**

## Quick Reference

This page provides a quick reference for frontend configuration. **For complete setup instructions, see the [Self-Hosting Guide](self-hosting.md).**

### Technology Stack

- **React 19**: Modern UI library
- **TypeScript**: Type-safe JavaScript
- **Vite 7**: Fast build tool and dev server
- **Bootstrap 5**: Responsive CSS framework
- **Leaflet**: Interactive mapping
- **PocketBase SDK**: Backend connection
- **Sentry**: Error tracking
- **i18next**: Multi-language support
- **Vite PWA**: Progressive Web App features

---

!!! note "Complete Setup Instructions"
    The sections below provide a technical reference for frontend configuration. For step-by-step setup instructions with deployment options and troubleshooting, please see the **[Self-Hosting Guide](self-hosting.md)**.

---

## Environment Variables

Create a `.env` file in the root directory with these settings:

### Required Variables

```bash
# System Environment - specifies the deployment environment
VITE_SYSTEM_ENVIRONMENT=production

# Version - automatically uses package.json version
VITE_VERSION=$npm_package_version

# PocketBase Backend URL - no trailing slash
VITE_POCKETBASE_URL=https://your-backend-url.com

# Legal Pages - required for production
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
VITE_ABOUT_URL=https://your-site.com/about

# Error Tracking - optional but recommended
VITE_SENTRY_DSN=https://your_sentry_dsn@sentry.io/123456
```

### Variable Details

#### VITE_SYSTEM_ENVIRONMENT

- **Purpose**: Determines which environment the app is running in
- **Values**:
  - `local` - Development on your local machine
  - `production` - Live deployment
- **Effect**: Affects Sentry error tracking configuration and logging

#### VITE_VERSION

- **Purpose**: Tracks application version in error reports
- **Value**: `$npm_package_version` (automatically reads from package.json)
- **Current Version**: 1.9.1 (as of package.json)
- **Effect**: Shown in Sentry reports and helps track which version has issues

#### VITE_POCKETBASE_URL

- **Purpose**: URL of your PocketBase backend server
- **Format**: Full URL without trailing slash
- **Example**: `https://api.ministry-mapper.com` or `http://localhost:8090` for local
- **Important**: Must be accessible from wherever the frontend is deployed

#### VITE_PRIVACY_URL

- **Purpose**: Link to your privacy policy page
- **Required**: Yes, for legal compliance (GDPR, CCPA, etc.)
- **Example**: `https://ministry-mapper.com/privacy`

#### VITE_TERMS_URL

- **Purpose**: Link to your terms of service page
- **Required**: Yes, for legal compliance
- **Example**: `https://ministry-mapper.com/terms`

#### VITE_ABOUT_URL

- **Purpose**: Link to information about your Ministry Mapper instance
- **Required**: No, but recommended
- **Example**: `https://ministry-mapper.com/about`

#### VITE_SENTRY_DSN

- **Purpose**: Sends JavaScript errors to Sentry for monitoring
- **Get It From**: [sentry.io](https://sentry.io) (free tier available)
- **Required**: No, but highly recommended for production
- **Benefits**: Track errors, monitor performance, get notified of issues
- **Note**: Only active when VITE_SYSTEM_ENVIRONMENT is "production"

## Local Development Setup

### Prerequisites

You need Node.js version 22 or higher:

```bash
# Check your Node.js version
node --version
```

If you don't have Node.js 22+, download it from: [nodejs.org](https://nodejs.org)

### Setup Steps

#### 1. Clone the Repository

```bash
git clone https://github.com/rimorin/ministry-mapper-v2.git
cd ministry-mapper-v2
```

#### 2. Install Dependencies

```bash
npm install
```

This downloads all required packages. May take a few minutes depending on your internet connection.

#### 3. Create Environment File

```bash
# Copy the example environment file
cp .env.example .env
```

Edit `.env` with your settings:

```bash
VITE_SYSTEM_ENVIRONMENT=local
VITE_VERSION=$npm_package_version
VITE_GOOGLE_MAPS_API_KEY=your_google_maps_key_here
VITE_POCKETBASE_URL=http://localhost:8090
VITE_PRIVACY_URL=http://localhost:3000/privacy
VITE_TERMS_URL=http://localhost:3000/terms
VITE_ABOUT_URL=http://localhost:3000/about
VITE_SENTRY_DSN=
```

**Note**: You'll need a PocketBase backend running on port 8090. See the ministry-mapper-be repository for backend setup.

#### 4. Start Development Server

```bash
npm start
```

The app will open at `http://localhost:3000` (port 3000 is configured in vite.config.js).

### Development Features

- **Hot Module Replacement (HMR)**: Changes appear instantly without full page reload
- **TypeScript Checking**: Errors show in terminal and browser console
- **Source Maps**: Debugging is easier with original source code references
- **Fast Refresh**: React components update without losing state

### Development Commands

```bash
# Start development server (port 3000)
npm start

# Run tests with Vitest
npm test

# Check code formatting
npm run prettier

# Auto-fix formatting issues
npm run prettier:fix

# Build for production
npm run build

# Preview production build locally
npm run serve
```

### Code Quality Tools

**Prettier** (code formatting):

- Configured in `.prettierrc`
- Automatically formats on commit via Husky
- Run manually with `npm run prettier:fix`

**ESLint** (code linting):

- Configured in `eslint.config.mjs`
- React and TypeScript rules enabled
- Checks for common errors and code quality issues

**Husky** (git hooks):

- Runs Prettier on staged files before commit
- Ensures consistent code formatting
- Validates commit messages with commitlint

## Production Deployment

### Building for Production

```bash
# Build optimized production files
npm run build
```

**What happens during build:**

- TypeScript compiles to JavaScript
- React code is optimized and minified
- CSS is processed and minified
- Source maps are generated
- Code is split into chunks for faster loading
- Output goes to `build/` directory
- Service worker is generated for PWA support

**Build Output:**

- `build/index.html` - Main HTML file
- `build/assets/` - JavaScript, CSS, and other assets
- Filenames include content hashes for caching
- Total size typically under 1MB (excluding external dependencies)

### Deployment Options

Ministry Mapper frontend is a static web application. You can deploy it to any static hosting service.

#### Option 1: Vercel (Recommended)

Fast, free tier, automatic deployments.

**Setup:**

1. Create account at [vercel.com](https://vercel.com)
2. Click "New Project"
3. Import your GitHub repository
4. Configure:
   - Framework Preset: Vite
   - Build Command: `npm run build`
   - Output Directory: `build`
   - Install Command: `npm install`
5. Add environment variables in Vercel dashboard
6. Deploy

**Vercel Features:**

- Automatic deployments on git push
- Preview deployments for pull requests
- Custom domains
- HTTPS by default
- Global CDN
- Free for personal projects

#### Option 2: Netlify

Simple deployment with great features.

**Setup:**

1. Create account at [netlify.com](https://netlify.com)
2. "Add new site" → "Import an existing project"
3. Connect GitHub repository
4. Build settings:
   - Build Command: `npm run build`
   - Publish Directory: `build`
5. Add environment variables
6. Deploy

**Netlify Features:**

- Automatic deployments
- Form handling
- Serverless functions (if needed)
- Custom domains
- HTTPS by default

#### Option 3: Cloudflare Pages

Fast CDN with generous free tier.

**Setup:**

1. Create account at [cloudflare.com](https://cloudflare.com)
2. Go to Pages → "Create a project"
3. Connect GitHub repository
4. Build settings:
   - Framework preset: Vite
   - Build command: `npm run build`
   - Build output directory: `build`
5. Add environment variables
6. Deploy

**Cloudflare Features:**

- Global CDN network
- DDoS protection
- Analytics
- Web Analytics without client-side tracking

#### Option 4: AWS S3 + CloudFront

More control, good for large deployments.

**Setup:**

1. Create S3 bucket
2. Enable static website hosting
3. Upload `build/` contents
4. Create CloudFront distribution
5. Configure custom domain
6. Set up SSL certificate

**AWS Considerations:**

- More complex setup
- Pay per usage (usually very cheap)
- More configuration options
- Good for enterprise deployments

#### Option 5: Self-Hosted

Host on your own server.

**Using Nginx:**

```nginx
server {
    listen 80;
    server_name your-domain.com;
    root /var/www/ministry-mapper/build;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Enable gzip compression
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;

    # Cache static assets
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

**Using Apache:**

```apache
<VirtualHost *:80>
    ServerName your-domain.com
    DocumentRoot /var/www/ministry-mapper/build

    <Directory /var/www/ministry-mapper/build>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted

        # Enable React Router
        RewriteEngine On
        RewriteBase /
        RewriteRule ^index\.html$ - [L]
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule . /index.html [L]
    </Directory>
</VirtualHost>
```

**Important for Self-Hosting:**

- Serve over HTTPS (required for PWA features)
- Configure proper CORS headers if backend is on different domain
- Set up proper caching headers for static assets
- Ensure SPA routing works (all routes serve index.html)

### Environment Variables in Deployment

**Important**: Environment variables must be set during build time, not runtime, because Vite injects them at build time.

**For Vercel/Netlify/Cloudflare:**

- Add environment variables in the platform's dashboard
- They'll be available during the build process

**For Self-Hosted:**

- Set environment variables before running `npm run build`
- Or create `.env.production` file with your settings

## Setting Up Sentry (Optional but Recommended)

Sentry monitors JavaScript errors and performance.

### 1. Create Sentry Account

1. Go to [sentry.io](https://sentry.io)
2. Sign up (generous free tier available)
3. Create a new project
4. Choose platform: "React"

### 2. Get Your DSN

1. After creating project, you'll see the DSN
2. Or go to Settings → Projects → [Your Project] → Client Keys (DSN)
3. Copy the DSN URL

### 3. Add to Environment

```bash
VITE_SENTRY_DSN=https://your_key@your_org.sentry.io/your_project_id
```

**Note**: Sentry is only active when `VITE_SYSTEM_ENVIRONMENT` is set to `production`.

### 4. Configure Sentry (Optional)

**Source Maps**: The build process automatically generates source maps, which help debug minified production code.

**Alerts**: Set up in Sentry dashboard:

- Email notifications for new issues
- Slack/Discord integration
- Custom alert rules for critical errors
- Release tracking

## Multi-Language Support

Ministry Mapper includes 7 languages out of the box.

### Supported Languages

- English (`en`)
- Japanese (`ja` - 日本語)
- Korean (`ko` - 한국어)
- Chinese (`zh` - 中文)
- Indonesian (`id` - Bahasa Indonesia)
- Malay (`ms` - Bahasa Melayu)
- Tamil (`ta` - தமிழ்)

### How Language Detection Works

- Uses `i18next-browser-languagedetector`
- Automatically detects from browser settings
- Falls back to English if browser language not supported
- Users cannot manually switch language (uses browser setting)

### Adding a New Language

1. **Create Translation File**

   - Go to `src/i18n/locales/`
   - Create new file: `[language-code].json` (e.g., `fr.json` for French)
   - Copy structure from `en.json`

2. **Translate All Strings**

   - Translate all key-value pairs
   - Keep the same JSON structure
   - Maintain placeholder variables like `{{variable}}`

3. **Register Language**
   - Edit `src/i18n/index.ts`
   - Import your translation file
   - Add to resources object

Example for French:

```typescript
import fr from './locales/fr.json';

resources: {
  en: { translation: en },
  fr: { translation: fr },
  // ... other languages
}
```

## Progressive Web App (PWA)

Ministry Mapper includes PWA support via Vite PWA plugin.

### PWA Features

- **Installable**: Can be installed on mobile/desktop home screen
- **Offline Assets**: Static files are cached for faster loading
- **Service Worker**: Automatically generated and registered
- **Cache Strategy**: StaleWhileRevalidate for external assets, CacheFirst for fonts

### PWA Configuration

Configuration in `vite.config.js`:

```javascript
VitePWA({
  registerType: "autoUpdate", // Auto-update service worker
  manifest: false, // No manifest (can be added if needed)
  workbox: {
    globPatterns: ["**/*.{js,css,html,ico,png,svg,woff2}"],
    skipWaiting: true, // Activate new service worker immediately
    clientsClaim: true, // Take control of clients immediately
    cleanupOutdatedCaches: true, // Remove old caches
  },
});
```

### Caching Strategy

**External Assets** (e.g., from assets.ministry-mapper.com):

- Strategy: StaleWhileRevalidate
- Cache name: "external-assets"
- Expiration: 7 days, max 50 entries

**Fonts**:

- Strategy: CacheFirst
- Cache name: "fonts"
- Expiration: 30 days, max 30 entries

**Note**: The app requires internet for data operations (PocketBase connection). Only static assets are cached offline.

## Testing Your Deployment

### Functionality Checklist

- [ ] Homepage loads correctly
- [ ] Sign up page works
- [ ] Email verification works
- [ ] Login page works
- [ ] OTP authentication works (if enabled)
- [ ] Password reset works
- [ ] Territory selector displays (for conductors/admins)
- [ ] Maps load and display correctly
- [ ] Can view territories
- [ ] Can update address status
- [ ] Assignment links work
- [ ] Links expire correctly
- [ ] Mobile view is responsive
- [ ] Language detection works
- [ ] All modals open and close properly

### Performance Checks

- [ ] Initial page load under 3 seconds
- [ ] Lighthouse score > 90
- [ ] No console errors
- [ ] Images load quickly
- [ ] Maps are responsive
- [ ] Smooth scrolling and navigation

### Security Checks

- [ ] HTTPS is enforced (no HTTP access)
- [ ] Privacy policy link works
- [ ] Terms of service link works
- [ ] API keys not exposed in browser code
- [ ] CORS configured correctly for backend
- [ ] PocketBase connection is secure

### Browser Testing

Test on multiple browsers:

- [ ] Chrome/Chromium
- [ ] Firefox
- [ ] Safari (macOS/iOS)
- [ ] Edge
- [ ] Mobile browsers

## Troubleshooting

### Backend Connection Failed

**Problem**: "Cannot connect to server" or "Network Error"

**Solutions:**

- Verify `VITE_POCKETBASE_URL` is correct (no trailing slash)
- Check if PocketBase backend is running
- Test backend URL directly in browser
- Check CORS settings in PocketBase (must allow your frontend domain)
- Open browser DevTools → Network tab to see exact error
- Ensure backend is accessible from your deployment location

### Build Errors

**Problem**: `npm run build` fails

**Solutions:**

- Ensure all environment variables are set correctly
- Run `npm install` to refresh dependencies
- Delete `node_modules` and reinstall: `rm -rf node_modules && npm install`
- Check Node.js version: `node --version` (needs 22+)
- Check for TypeScript errors: Look at specific error messages
- Try `npm run prettier:fix` to fix formatting issues
- Clear Vite cache: `rm -rf node_modules/.vite`

### TypeScript Errors

**Problem**: Type errors during build

**Solutions:**

- Check `src/utils/interface.ts` for type definitions
- Ensure imports are correct
- Run `npx tsc --noEmit` to see all type errors
- Verify all dependencies are installed

### Slow Performance

**Problem**: App is slow or laggy

**Solutions:**

- Check internet connection speed
- Clear browser cache and reload
- Ensure PocketBase backend is responding quickly
- Check browser console for JavaScript errors
- Test on different device/browser to isolate issue
- Check Lighthouse performance score
- Verify service worker is working: DevTools → Application → Service Workers

### PWA Installation Issues

**Problem**: "Add to Home Screen" doesn't appear

**Solutions:**

- Must be served over HTTPS
- Check service worker registration in DevTools
- Verify PWA requirements met (manifest, service worker, etc.)
- Try different browser (Safari, Chrome have different PWA support)

### Real-time Updates Not Working

**Problem**: Changes don't appear for other users

**Solutions:**

- Verify PocketBase realtime subscriptions are working
- Check browser console for WebSocket errors
- Ensure backend WebSocket endpoint is accessible
- Check if multiple users are actually viewing the same territory
- Refresh the page to force reconnection

## Maintenance

### Keeping Dependencies Updated

```bash
# Pull latest changes from repository
git pull origin main

# Install updated dependencies
npm install

# Check for security vulnerabilities
npm audit

# Automatically fix security issues (when possible)
npm audit fix

# Check for outdated packages
npm outdated

# Update specific package
npm update package-name
```

### Monitoring

**Sentry Dashboard:**

- Check weekly for new errors
- Review error trends
- Fix critical issues promptly
- Set up alerts for high-priority errors

**Google Cloud Console:**

- Monitor API usage to stay within quota
- Check for unusual spikes in usage
- Review billing alerts
- Ensure APIs remain enabled

**Performance:**

- Run Lighthouse audits periodically
- Monitor page load times
- Check Core Web Vitals
- Test on slow connections

**User Feedback:**

- Listen to congregation members
- Track common issues
- Document solutions
- Update documentation as needed

### Version Updates

Ministry Mapper uses semantic versioning:

- **Major version** (x.0.0): Breaking changes
- **Minor version** (1.x.0): New features
- **Patch version** (1.9.x): Bug fixes

## Next Steps

**For Users:**

- Read the [User Guide](user-guide.md) to learn how to use the application

**For Administrators:**

- Set up the PocketBase backend (see ministry-mapper-be repository)
- Configure congregation settings
- Invite users and assign roles
- Create territories and add addresses
