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
- Real-time data synchronization over Server-Sent Events
- Local-first publisher updates (Smart Sync) with an offline queue
- Mobile-responsive design
- Multi-language support (8 languages)
- Progressive Web App capabilities

**This guide assumes you have**:
- Experience with Node.js and npm
- Understanding of environment variables
- Familiarity with static site deployment
- Knowledge of web application security

For complete self-hosting instructions, see the [Self-Hosting Guide](self-hosting.md).

## Quick Reference

This page provides a quick reference for frontend configuration. **For complete setup instructions, see the [Self-Hosting Guide](self-hosting.md).**

### Technology Stack

- **Node.js >=24.0.0**: Declared in `engines.node`; CI pins `node-version: 24`
- **React 19.2.8**: UI library, compiled with the **React Compiler** (`babel-plugin-react-compiler`), which is active in both the Vite build and the Vitest run
- **TypeScript 6.0.3**: `strict: true`, with the path alias `@/* → ./src/*`
- **Vite 8.2.0**: Build tool and dev server — this release is **Rolldown-based**, which changes how bundle chunking is configured
- **Vitest 4.1.11**: Test runner (jsdom environment, v8 coverage)
- **Tailwind CSS 4.3.3**: Styling, via `@tailwindcss/vite`. Tailwind 4 is **CSS-first** — there is **no `tailwind.config`** file; tokens and theme live in `src/index.css`
- **shadcn/ui on Base UI**: The component layer is shadcn/ui's **Base UI** flavour (`@base-ui/react` 1.7.0). `components.json` declares `"style": "base-vega"`
- **wouter 3.10.0**: Client-side router
- **motion 13.1.0**: Animation (the Framer Motion successor)
- **lucide-react**: Icon set
- **react-hook-form**: Form state — used **without** a schema resolver
- **Leaflet 1.9.4 + react-leaflet 5.0.0**: Interactive mapping
- **PocketBase SDK 0.27.3**: Backend connection
- **i18next 26.3.6 + react-i18next**: Multi-language support
- **@sentry/react 10.70.0**: Error tracking
- **launchdarkly-react-client-sdk**: Runtime feature flags
- **vite-plugin-pwa 1.3.0**: Progressive Web App features

!!! warning "Libraries you may see referenced in older documentation"
    The 2.0.0 release rebuilt the interface from the ground up. **Bootstrap 5 and react-bootstrap are gone**, and so are **SCSS/Sass**, **react-select** (replaced by an in-house `MultiSelect` dialog), **react-calendar** (replaced by react-day-picker) and **leaflet-geosearch** (replaced by an in-house Geoapify `SearchControl`). **sonner** was removed at 2.5.0 in favour of the Base UI toast.

    Two things are commonly assumed and are **not** true here: **Radix UI is not used** — the shadcn components in this project are the Base UI flavour, and no Radix package is installed. **Zod is not a dependency** either; the string `zod` appears in `vite.config.js` as dead chunk configuration only.

!!! note "The `data-slot` attributes are part of the component API"
    Every primitive under `src/components/ui/` stamps `data-slot="..."` attributes on its internal elements. These are **not decorative** — Tailwind selectors elsewhere in the app target them (for example to restyle a dialog's header from the outside). Removing or renaming a `data-slot` value is a breaking change to that component's API, even though nothing in TypeScript will complain.

---

!!! note "Complete Setup Instructions"
    The sections below provide a technical reference for frontend configuration. For step-by-step setup instructions with deployment options and troubleshooting, please see the **[Self-Hosting Guide](self-hosting.md)**.

---

## Environment Variables

Create a `.env` file in the root directory with these settings. A template ships in the repository:

```bash
cp .env.example .env
```

!!! warning "Only `VITE_`-prefixed variables reach the browser"
    Vite inlines every variable whose name begins with `VITE_` into the JavaScript bundle at **build time**. Anything shipped that way is public: it is readable by anyone who opens DevTools. Variables **without** the prefix are visible only to the build process and never reach the client — which is why the Sentry upload credentials are deliberately unprefixed.

    The corollary is that a `VITE_` variable is not a secret. Never put a backend API secret, a database credential, or a private token behind a `VITE_` name.

!!! note "`.env.example` vs `.env.sample`"
    The frontend template is **`.env.example`**; the backend repository uses **`.env.sample`**. The two filenames differ, and copying the wrong one is a common first-run mistake.

### Required Variables

```bash
# System Environment - specifies the deployment environment
VITE_SYSTEM_ENVIRONMENT=production

# PocketBase Backend URL - no trailing slash
VITE_POCKETBASE_URL=https://your-backend-url.com

# Geoapify API key - map tiles, address search and routing
VITE_GEOAPIFY_API_KEY=your_geoapify_api_key

# Legal Pages - required for sign-up
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
```

### Optional Variables

```bash
# About link shown in the sign-in footer
VITE_ABOUT_URL=https://your-site.com/about

# Error Tracking (Sentry) - recommended for production
VITE_SENTRY_DSN=https://your_sentry_dsn@sentry.io/123456

# Runtime feature flags (leave empty to disable flags entirely)
VITE_LAUNCHDARKLY_CLIENT_ID=your_launchdarkly_client_side_id

# Umami Analytics
VITE_UMAMI_SRC_URL=https://cloud.umami.is/script.js
VITE_UMAMI_WEBSITE_ID=your_umami_website_id
VITE_UMAMI_DOMAINS=yourdomain.com

# Maintenance Mode - build-time kill switch
VITE_MAINTENANCE_MODE=false
```

### Build-Time Only Variables

These are **not** `VITE_`-prefixed, so they are never shipped to the client. They affect only the build.

```bash
# Sentry source-map upload (the plugin runs only when NODE_ENV=production)
SENTRY_AUTH_TOKEN=your_sentry_auth_token
SENTRY_ORG=your_sentry_org_slug
SENTRY_PROJECT=your_sentry_project_slug

# Set to "production" to enable minified-build behaviour and the Sentry plugin
NODE_ENV=production

# Set to any value to emit a bundle-size report (stats.html)
ANALYZE=1
```

`CI` is also read by `vite.config.js`, but is currently unused.

### Injected Variables

`VITE_APP_VERSION` is **defined by the build, not by you**. `vite.config.js` injects it from `package.json`'s `version` field. Setting it in `.env` has no effect. Do not add it to your environment.

### Variable Details

#### VITE_POCKETBASE_URL

- **Purpose**: Base URL of your PocketBase backend server; also used to build the `/api/health` probe that drives the connection-status indicator
- **Required**: Yes
- **Format**: Full URL without trailing slash
- **Example**: `https://api.ministry-mapper.com` or `http://localhost:8090` for local
- **Effect if missing**: The app does not start normally — it renders a **"Missing PocketBase URL"** setup page instead of the sign-in screen. If you see that page, this variable was not present at build time
- **Important**: Must be accessible from wherever the frontend is deployed, and PocketBase's allowed origins must include your frontend domain

#### VITE_SYSTEM_ENVIRONMENT

- **Purpose**: Declares which environment this build represents
- **Required**: Yes
- **Values**: `local`, `development`, `staging`, `production`
- **Default**: `local` when unset
- **Effect**: Tags Sentry events with the environment name, gates whether route-level errors are forwarded to Sentry (only in `production`), and controls the STG/DEV corner badge and the version footer

#### VITE_GEOAPIFY_API_KEY

- **Purpose**: The **single** geo credential. It covers all three map-related services: **tiles** (style `osm-carto`, retina-aware, `maxZoom` 20), **geocode autocomplete** in the address search control, and **routing** for the Directions and Quick Link screens
- **Required**: Yes, for anything map-related to work
- **Get It From**: [geoapify.com](https://www.geoapify.com) (free tier available)
- **Example**: `VITE_GEOAPIFY_API_KEY=abc123def456`
- **Note**: Earlier versions used separate OpenRouteService and LocationIQ keys. **Both providers have been removed**; if your `.env` still carries `VITE_OPENROUTE_API_KEY` or `VITE_LOCATIONIQ_API_KEY`, delete those lines — nothing reads them

#### VITE_PRIVACY_URL

- **Purpose**: Link to your privacy policy page, shown on the sign-up form
- **Required**: Yes, for legal compliance (GDPR, CCPA, etc.)
- **Example**: `https://ministry-mapper.com/privacy`

#### VITE_TERMS_URL

- **Purpose**: Link to your terms of service page, shown on the sign-up form
- **Required**: Yes, for legal compliance
- **Example**: `https://ministry-mapper.com/terms`

#### VITE_ABOUT_URL

- **Purpose**: Target of the **About** button in the sign-in footer
- **Required**: No
- **Default**: Falls back to the hosted user guide when unset
- **Example**: `https://ministry-mapper.com/about`

#### VITE_LAUNCHDARKLY_CLIENT_ID

- **Purpose**: LaunchDarkly client-side ID, enabling runtime feature flags
- **Required**: No
- **Example**: `VITE_LAUNCHDARKLY_CLIENT_ID=64f0c1a2b3c4d5e6f7a8b9c0`
- **Effect if empty**: Feature flags are disabled entirely and the app renders without the flag provider. See [Feature Flags](#feature-flags) for the degradation behaviour

#### VITE_SENTRY_DSN

- **Purpose**: Sends JavaScript errors to Sentry for monitoring
- **Required**: No, but highly recommended for production
- **Get It From**: [sentry.io](https://sentry.io) (free tier available)
- **Example**: `https://your_key@your_org.sentry.io/your_project_id`
- **Effect if empty**: Sentry initializes but is inert — no events are sent

#### VITE_UMAMI_SRC_URL

- **Purpose**: URL of the Umami analytics script
- **Required**: No
- **Example**: `https://cloud.umami.is/script.js`
- **Note**: **Both this and `VITE_UMAMI_WEBSITE_ID` must be set.** If either is missing, analytics initialization returns early and no script is injected at all — there is no partial mode

#### VITE_UMAMI_WEBSITE_ID

- **Purpose**: Identifies your site inside Umami
- **Required**: No, but mandatory if `VITE_UMAMI_SRC_URL` is set
- **Example**: `VITE_UMAMI_WEBSITE_ID=8f3c1a90-1234-4c56-9abc-def012345678`

#### VITE_UMAMI_DOMAINS

- **Purpose**: Comma-separated list of domains Umami should record events from, passed through as the script's `data-domains` attribute
- **Required**: No
- **Example**: `VITE_UMAMI_DOMAINS=ministry-mapper.com,www.ministry-mapper.com`
- **Effect**: Keeps preview and localhost traffic out of your analytics

#### VITE_MAINTENANCE_MODE

- **Purpose**: Build-time kill switch. When set to `true`, the app renders the maintenance screen instead of the application
- **Required**: No
- **Values**: `true` or `false` — the check is an exact string comparison against `"true"`
- **Default**: Unset, which behaves as `false`
- **Use Case**: Planned downtime during a backend upgrade, when you would rather rebuild than manage a runtime flag
- **Note**: This variable is **not** in `.env.example`. It is one of two independent triggers for maintenance mode; the other is the LaunchDarkly `maintenance-mode` flag, which can be flipped without a rebuild. Either one on its own turns maintenance mode on

#### VITE_APP_VERSION

- **Purpose**: The application version, used as the Sentry release name, the LaunchDarkly application version, the version footer, and the value the PWA staleness check compares against `version.json`
- **Required**: **Injected — do not set.** `vite.config.js` defines it from `package.json`'s `version`
- **Effect of setting it manually**: None; the build's own definition wins

#### SENTRY_AUTH_TOKEN / SENTRY_ORG / SENTRY_PROJECT

- **Purpose**: Used during `npm run build` to upload source maps to Sentry so that minified stack traces can be resolved back to your original source code
- **Required**: No — the build still produces hidden source maps without them, but uploading greatly improves error debugging in production
- **How to Get**: Create an auth token in your Sentry organisation settings under **Settings → Auth Tokens**
- **Note**: The Sentry Vite plugin runs **only when `NODE_ENV` is `production`**. In any other mode these three variables are ignored. After a successful upload the plugin deletes the `.map` files from `build/`, so they are never served publicly

## Local Development Setup

### Prerequisites

You need Node.js **>=24.0.0**, as declared by `engines.node` in `package.json`. CI pins `node-version: 24`, so matching that locally avoids surprises.

```bash
# Check your Node.js version
node --version
```

If you don't have Node.js >=24.0.0, download it from: [nodejs.org](https://nodejs.org)

!!! note "Don't read the Node version out of `.nvmrc`"
    The repository's `.nvmrc` holds an **nvm alias name** (`mm_fe`), not a version number, and it is gitignored. `engines.node` and the CI workflow are the two places that state the real requirement.

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

This downloads all required packages. May take a few minutes depending on your internet connection. It also installs the Husky git hooks via the `prepare` script.

#### 3. Create Environment File

```bash
# Copy the example environment file
cp .env.example .env
```

Edit `.env` with your settings:

```bash
VITE_SYSTEM_ENVIRONMENT=local
VITE_POCKETBASE_URL=http://localhost:8090
VITE_GEOAPIFY_API_KEY=your_geoapify_api_key
VITE_PRIVACY_URL=http://localhost:3000/privacy
VITE_TERMS_URL=http://localhost:3000/terms
VITE_ABOUT_URL=http://localhost:3000/about
VITE_SENTRY_DSN=
VITE_LAUNCHDARKLY_CLIENT_ID=
VITE_UMAMI_SRC_URL=
VITE_UMAMI_WEBSITE_ID=
VITE_UMAMI_DOMAINS=
```

**Note**: You'll need a PocketBase backend running on port 8090. See the ministry-mapper-be repository for backend setup.

#### 4. Start Development Server

```bash
npm start
```

The app will open at `http://localhost:3000` (port 3000 is configured in `vite.config.js`).

!!! info "`prestart` and `prebuild` generate two files first"
    Both `npm start` and `npm run build` run `node scripts/prebuild.js` beforehand. It writes:

    - **`public/changelog.json`** — the in-app release notes, parsed from `release-notes/` across all locales and **capped at the 10 most recent releases**
    - **`public/version.json`** — the current version, used by the PWA staleness check

    Both files are **gitignored** and regenerated on every start and build. If the in-app "What's New" dialog is empty or the update prompt never fires, the usual cause is a build that skipped the prebuild step.

### Development Features

- **Hot Module Replacement (HMR)**: Changes appear instantly without full page reload
- **TypeScript Checking**: Errors show in terminal and browser console
- **Source Maps**: Debugging is easier with original source code references
- **Fast Refresh**: React components update without losing state
- **React Compiler**: Components are auto-memoized at build time, so most manual `useMemo`/`useCallback` wrapping is unnecessary

### Development Commands

```bash
# Start development server (port 3000); runs prestart first
npm start

# Run the full test suite once (vitest run)
npm test

# Watch mode
npm run test:watch

# Vitest UI
npm run test:ui

# Coverage report (v8)
npm run test:coverage

# Run only the hook tests
npm run test:hooks

# Run only the component tests
npm run test:components

# Lint
npm run lint

# Lint and auto-fix
npm run lint:fix

# Check code formatting
npm run prettier

# Auto-fix formatting issues
npm run prettier:fix

# Build for production; runs prebuild first
npm run build

# Preview the production build locally (vite preview)
npm run serve
```

!!! note "`npm run serve`, not `npm run preview`"
    Vite's own convention is `preview`, but this project names the script **`serve`**. It runs `vite preview` underneath. There is no `preview` script and no `dev` script — the dev server is `npm start`.

### Build Output

`npm run build` writes to **`build/`**, not Vite's default `dist/`. The relevant `vite.config.js` settings are:

- `outDir: "build"`
- `target: "esnext"` — no legacy transpilation; the app targets modern browsers only
- `sourcemap: "hidden"` — maps are generated for Sentry but not referenced from the shipped bundles

Every static-host configuration in this guide therefore points at `build`.

### Code Quality Tools

**Prettier** (code formatting):

- Configured in `.prettierrc` — double quotes, no trailing commas, 2-space indent
- `.prettierignore` excludes `*.md`
- Run manually with `npm run prettier:fix`

**ESLint** (code linting):

- Flat config in `eslint.config.mjs`: the recommended sets from ESLint, typescript-eslint and `@eslint-react`
- Unused variables must be prefixed with `_`
- `npm run lint` runs plain `eslint src`

**Husky** (git hooks):

- `.husky/pre-commit` runs lint-staged: Prettier writes `*.{css,ts,tsx}`, then ESLint fixes `*.{ts,tsx}` with **`--max-warnings 0`**
- `.husky/commit-msg` runs commitlint against `@commitlint/config-conventional`

!!! warning "The pre-commit lint is stricter than the CI lint"
    lint-staged runs `eslint --fix --max-warnings 0`, so **any warning blocks a commit**. CI runs plain `eslint src`, which does not fail on warnings. A commit that Husky rejects locally is therefore not necessarily a CI failure — and, more importantly, code that passes CI can still be blocked at commit time. If you bypass the hook, expect to re-fix warnings later.

**Conventional Commits**:

Commit messages must follow [Conventional Commits](https://www.conventionalcommits.org) — `feat:`, `fix:`, `chore:`, and so on, with `feat!:`/`fix!:` marking a breaking change. This is not cosmetic: `semantic-release` derives the next version number and the changelog directly from these messages. commitlint enforces the format on every commit.

## Feature Flags

Runtime feature flags come from **LaunchDarkly**, enabled by `VITE_LAUNCHDARKLY_CLIENT_ID`.

**Exactly one flag exists**: **`maintenance-mode`**. When it is on, the app renders the maintenance screen instead of the application. The build-time `VITE_MAINTENANCE_MODE=true` is the second, independent trigger for the same screen; either alone is sufficient.

Initialization details worth knowing:

- Pre-login sessions all share **one anonymous context** (`{ kind: "user", key: "anonymous", anonymous: true }`) rather than one per device, which keeps global flag evaluation billable as roughly one monthly active user
- Once a signed-in user's roles load, the app calls `identify()` with a **multi-context**: a billable `user` context and a non-billable `congregation` context. `email` is registered as a private attribute
- The client bootstraps from `localStorage`, so repeat loads resolve flags synchronously with no flicker
- A `<Loader/>` paints before LaunchDarkly resolves, so the maintenance gate never flashes the real application

**Three fail-open degradation layers**, so a LaunchDarkly outage can never take the app down:

1. **No client ID** — the app renders without the flag provider and flag lookups return an empty object. This is the normal state for a self-hosted instance
2. **Initialization throws** — the error is caught, reported to Sentry in production, and the app renders without flags
3. **Initialization is slow** — the 1.5-second timeout does **not** reject. It resolves with a working provider that hydrates once the client signals ready, so the timeout only bounds the cold-cache load

In all three cases the result is the same: the app runs with flags off. Nothing gates behind a successful flag fetch.

## Bundle Chunking

Because Vite 8 is Rolldown-based, vendor chunking uses **Rolldown's native `advancedChunks.groups`** API in `vite.config.js`, with an explicit `priority` on each group. `vendor-react` carries the highest priority so React can never be captured into another group.

!!! warning "Do not switch back to a `manualChunks()` function"
    This is recorded as a comment in `vite.config.js` because it was a real regression. Rolldown's compatibility emulation of Rollup's `manualChunks()` **mis-assigns `react`, `react-dom` and `jsx-runtime` into the `vendor-base-ui` group**, which drags the entire Base UI chunk (~87 kB gzipped) onto the initial preload path and slows first paint for every visitor. The native `advancedChunks` API with explicit priorities is what prevents that. If you are tempted to "simplify" the chunk configuration back into a function, don't.

Three chunks are deliberately split *out* of the large Base UI bundle because of when they load: `vendor-nice-modal` and `vendor-base-ui-toast` (the toaster mounts at app entry), and `vendor-base-ui-drawer` (only mobile bottom sheets render it, so it must not ride along in the chunk every dialog download pulls).

To inspect the result, build with `ANALYZE=1 npm run build` and open the generated `stats.html`.

## Testing

Tests run on **Vitest** in a **jsdom** environment, with **v8** coverage.

```bash
# Run everything once
npm test

# Coverage report
npm run test:coverage

# Narrow the run
npm run test:hooks
npm run test:components
```

- **103 test files, all colocated** — `useMapManagement.test.ts` sits next to `useMapManagement.ts`. There is **no `__tests__/` directory**, and new tests should not create one
- Configuration lives in `vitest.config.ts`: `environment: "jsdom"`, `globals: true`, `setupFiles: ["./src/setupTests.ts"]`, `css: false`. The React Compiler babel preset runs in tests too, so tested components behave as they do in a build
- **`fake-indexeddb`** backs the Smart Sync tests, which need a real IndexedDB implementation to exercise the offline queue
- Component tests render through the shared wrapper at **`src/utils/test/test-wrapper.tsx`**, which supplies the i18n provider, the modal provider and the theme middleware, accepts a `locale` option, and re-exports Testing Library plus `userEvent`. Use it rather than rendering bare — a component rendered without it will throw on the first translation lookup
- Hook tests declare shared mock state with `vi.hoisted()`, `vi.mock()` the PocketBase, i18n and sibling modules, then `await import()` the module under test **after** the mocks are in place

## Error Tracking with Sentry

Sentry monitors JavaScript errors in production.

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

The `environment` tag on every event comes from `VITE_SYSTEM_ENVIRONMENT`. Route-level errors are forwarded to Sentry only when that value is `production`.

### 4. How Sentry Is Configured

Initialization lives in `instrument.js`, which is imported as the very first line of `src/index.tsx` so it is in place before any application code runs.

- **`tracesSampleRate: 0`** — performance monitoring is switched off deliberately. The Sentry Vite plugin's `bundleSizeOptimizations` then tree-shakes tracing, debug statements and every Session Replay variant out of the bundle entirely, so this is a size decision as much as a data one
- **Default integrations only** — breadcrumbs, uncaught exceptions and unhandled rejections
- **`ignoreErrors`** filters the noise that is never actionable: `ResizeObserver loop`, `NetworkError`, `Failed to fetch`, `Load failed`, `cancelled`, `AbortError`. Most of these come from ordinary connectivity loss or from request cancellations the app performs on purpose
- **Browser-extension frames are dropped.** `beforeSend` discards any event whose stack contains a `chrome-extension://` or `moz-extension://` frame. These are crashes inside a visitor's extension, not in the app, and they cannot be fixed from here
- **Release tracking** uses `VITE_APP_VERSION`, so issues group per released version

### 5. Alerts

Set up in the Sentry dashboard:

- Email notifications for new issues
- Slack/Discord integration
- Custom alert rules for critical errors
- Release tracking

## Production Deployment

### Building for Production

```bash
# Build optimized production files
npm run build
```

**What happens during build:**

- `prebuild` regenerates `public/changelog.json` and `public/version.json`
- TypeScript compiles to JavaScript, with the React Compiler auto-memoizing components
- Tailwind generates the stylesheet from the classes actually used
- Code is split into vendor chunks by Rolldown's `advancedChunks` groups
- Hidden source maps are generated, uploaded to Sentry if the build-time Sentry variables are present, then deleted from the output
- A service worker is generated for PWA support
- Output goes to the `build/` directory

**Build Output:**

- `build/index.html` - Main HTML file
- `build/assets/` - JavaScript, CSS, and other assets
- Filenames include content hashes for caching
- Each language ships as its own `translation-*.js` chunk, downloaded on demand

### Continuous Integration and Release

The project's own pipeline lives in `.github/workflows/`.

**Pull requests** (`checks.yaml`, on PRs to `master` or `staging`) run four gates in order:

1. **format** — `npm run prettier`
2. **lint** — `npm run lint`
3. **test** — `npm test`
4. **build** — `npm run build`

**Push to `master`** (`release.yaml`) runs the **same four gates**, then:

5. `npx semantic-release` — derives the version from the Conventional Commit messages, writes the changelog, tags, and publishes the GitHub release
6. **POSTs to a Coolify webhook**, which triggers the deployment

```bash
curl --fail-with-body --silent --show-error \
  --request POST "$COOLIFY_WEBHOOK" \
  --header "Authorization: Bearer $COOLIFY_TOKEN"
```

!!! warning "Why that curl invocation looks the way it does"
    Two flags in that command are load-bearing. The webhook must be called with **POST**: it answers a GET with `405 This endpoint has changed to a POST request`, even though older Coolify documentation still shows GET. And without **`--fail-with-body`**, curl exits `0` on a 4xx or 5xx response — so the 405 was reported as a successful step while every release quietly deployed nothing. If you copy this pattern for your own webhook, keep both.

The release workflow deliberately repeats the PR gates because a push straight to `master` never runs PR checks, and it clones with `fetch-depth: 0` because `semantic-release` reads back to the last tag — a shallow clone yields the wrong version.

### Deployment Options

The hosted service is deployed to **Coolify** behind Cloudflare, as described above. The frontend is otherwise an ordinary static web application, so self-hosters can serve the `build/` directory from any static host. The options below are **alternatives for self-hosting**, not what the project itself uses.

#### Option 1: Coolify

Self-hostable platform; matches how the hosted service is deployed.

**Setup:**

1. Create an application in Coolify pointing at your fork
2. Configure:
   - Build Command: `npm run build`
   - Publish Directory: `build`
3. Add environment variables in the Coolify UI (they must exist at build time)
4. Copy the deploy webhook URL and token if you want to trigger releases from CI
5. Deploy

**Features:**

- Runs on your own server
- Git-push or webhook deployments
- Managed TLS certificates
- Same platform used for the backend, so one place to look

#### Option 2: Vercel

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

**Features:**

- Automatic deployments on git push
- Preview deployments for pull requests
- Custom domains
- HTTPS by default
- Global CDN
- Free for personal projects

#### Option 3: Netlify

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

**Features:**

- Automatic deployments
- Form handling
- Serverless functions (if needed)
- Custom domains
- HTTPS by default

#### Option 4: Cloudflare Pages

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

**Features:**

- Global CDN network
- DDoS protection
- Analytics
- Web Analytics without client-side tracking

#### Option 5: AWS S3 + CloudFront

More control, good for large deployments.

**Setup:**

1. Create S3 bucket
2. Enable static website hosting
3. Upload `build/` contents
4. Create CloudFront distribution
5. Configure custom domain
6. Set up SSL certificate

**Features:**

- More complex setup
- Pay per usage (usually very cheap)
- More configuration options
- Good for enterprise deployments

#### Option 6: Self-Hosted Web Server

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

        # SPA history-mode fallback: serve index.html for client-side routes
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

- Serve over HTTPS (required for PWA features, geolocation and the Web Share API)
- Configure proper CORS headers if backend is on different domain
- Set up proper caching headers for static assets
- Ensure SPA routing works (all routes serve `index.html`), so that a deep link such as `/map/<id>` opens correctly on a cold load

### Environment Variables in Deployment

**Important**: Environment variables must be set during build time, not runtime, because Vite injects them at build time. Changing a value on your host without rebuilding has no effect on an already-built bundle.

**For Coolify/Vercel/Netlify/Cloudflare:**

- Add environment variables in the platform's dashboard
- They'll be available during the build process

**For Self-Hosted:**

- Set environment variables before running `npm run build`
- Or create a `.env.production` file with your settings

## Multi-Language Support

Ministry Mapper ships **8 languages** out of the box.

### Supported Languages

- English (`en`)
- Spanish (`es` - Español)
- Chinese (`zh` - 中文)
- Tamil (`ta` - தமிழ்)
- Indonesian (`id` - Bahasa Indonesia)
- Malay (`ms` - B. Melayu)
- Japanese (`ja` - 日本語)
- Korean (`ko` - 한국어)

Locale files live at `src/i18n/locales/<code>/translation.json`. All 8 are **key-identical, 474 keys each** — a key added to one must be added to all eight.

### How Language Selection Works

- **Language is chosen inside the app**, not in your browser settings. The **Language** button appears in three places: the admin sidebar footer, the sign-in footer (so it works before you log in), and the publisher map's bottom navigation bar, where it also shows the current language
- Detection order on a first visit is **`localStorage` → `navigator`**: a previously chosen language always wins over the browser's preference. The choice is cached under the `i18nextLng` key
- `load: "languageOnly"` means a browser reporting `zh-TW` is stored and served as `zh`; regional variants collapse to their base language
- `fallbackLng: "en"` — English fills in for any key missing from another locale
- **Each language code-splits into its own chunk.** Translations are fetched on demand rather than bundled together, which is why the service worker treats them specially (see [Caching Strategy](#caching-strategy))

### Adding a New Language

1. **Create Translation File**

   - Create the directory `src/i18n/locales/[language-code]/` (e.g., `fr/` for French)
   - Add `translation.json` inside it
   - Copy the structure from `src/i18n/locales/en/translation.json`

2. **Translate All Strings**

   - Translate all 474 key-value pairs
   - Keep the same JSON structure and key names
   - Maintain placeholder variables like `{{count}}`

3. **Register the Language**
   - Edit `src/i18n/index.ts` and add the code to the supported-languages list
   - Add a label for it in `src/i18n/LanguageSelector.tsx` so it appears in the picker

Locale files are loaded by dynamic import, so no `resources` object needs editing:

```typescript
// src/i18n/index.ts — locales resolve through a dynamic import,
// which is what gives each language its own chunk.
resourcesToBackend(
  (language: string, namespace: string) =>
    import(`./locales/${language}/${namespace}.json`)
);
```

## Progressive Web App (PWA)

Ministry Mapper includes PWA support via the Vite PWA plugin.

### PWA Features

- **Installable**: Can be installed on mobile/desktop home screen
- **Offline Assets**: Static files are precached for faster loading
- **Service Worker**: Generated (`strategies: "generateSW"`) and registered automatically
- **Automatic Updates**: `registerType: "autoUpdate"`, with a user-visible reload prompt

!!! info "`manifest: false` does not mean there is no manifest"
    The plugin is configured with `manifest: false`, which tells it **not to generate** a web app manifest. The app is still installable because a hand-written manifest ships at **`public/site.webmanifest`** and is linked from `index.html`. Editing PWA metadata — name, icons, `display`, `start_url`, theme colours — therefore means editing that file, not `vite.config.js`.

    The manifest declares `display: "standalone"`, `start_url: "/"`, `scope: "/"`, and four icons (192 and 512 px in both `any` and `maskable` purposes) hosted on the asset CDN.

### PWA Configuration

Configuration in `vite.config.js`:

```javascript
VitePWA({
  strategies: "generateSW",
  registerType: "autoUpdate",
  injectRegister: "auto",
  // Use the existing site.webmanifest — don't generate a new one
  manifest: false,
  workbox: {
    globPatterns: ["**/*.{js,css,html}"],
    // Exclude translation chunks from precache — they are large (~18-42 kB each)
    // and users only ever need 1 language. Runtime CacheFirst handles them instead.
    globIgnores: ["**/translation-*.js"],
    navigateFallback: "/index.html",
    // Prevent the SW from intercepting direct file navigations (e.g. .json,
    // .webmanifest, .ico) and serving index.html in their place.
    navigateFallbackDenylist: [/\.[a-z]{2,6}$/i],
    inlineWorkboxRuntime: true,
    cleanupOutdatedCaches: true,
    runtimeCaching: [
      {
        urlPattern: /\/assets\/translation-[^/]+\.js$/,
        handler: "CacheFirst",
        options: {
          cacheName: "translations",
          expiration: { maxEntries: 4, maxAgeSeconds: 60 * 60 * 24 * 30 }
        }
      }
    ]
  },
  devOptions: { enabled: false }
});
```

### Caching Strategy

**Application shell** — precached from `globPatterns: ["**/*.{js,css,html}"]`.

**Translation chunks** — excluded from precache by `globIgnores: ["**/translation-*.js"]` and served instead by a runtime **CacheFirst** cache:

- Cache name: `translations`
- Expiration: 30 days, max **4** entries

The reason is arithmetic: each locale chunk is **18–42 kB** and a given user needs exactly one of them. Precaching all eight would add hundreds of kilobytes to every install for content that would never be read. Keeping four entries covers switching languages and switching back without a refetch.

**Navigation requests** — `navigateFallback: "/index.html"` makes deep links work offline. The `navigateFallbackDenylist` (`/\.[a-z]{2,6}$/i`) stops the service worker from answering a direct request for `version.json`, `site.webmanifest` or `favicon.ico` with the HTML shell, which would otherwise break the update check.

!!! note "What is *not* cached"
    Data operations go to PocketBase over the network. Reads fall back to a local cache when the network is unavailable (IndexedDB for map addresses, `localStorage` for the publisher link payload) and **publisher address updates are queued offline by Smart Sync**, but administrator actions require a live connection. See the [User Guide](user-guide.md) for what works offline in practice.

### How Updates Are Detected

An installed PWA can sit suspended for days, so update detection does not rely on any single signal. **Three independent paths** exist, all wired up in `src/index.tsx`:

1. **`controllerchange`** — a new service worker takes control and there was a previous controller (so a first install doesn't count as an update)
2. **Tab-focus check** — on `visibilitychange` to visible, throttled to 5 minutes, the app asks the registration to update. Skipped while a worker is already installing, or while offline
3. **`version.json` backstop** — a frozen or suspended standalone window may never fire either of the above, so the app fetches `/version.json` with `cache: "no-store"` and compares it against the compiled-in `VITE_APP_VERSION`. Also throttled to 5 minutes, but the throttle is **bypassed on a `pageshow` with `event.persisted === true`** — returning from the app switcher is precisely the moment staleness must be caught

Any of the three raises a persistent toast titled **"Update Available"** with the body "Reload to get the latest updates." and a **"Reload"** action. The app never reloads by itself, so nobody loses a half-finished edit.

**Chunk-load recovery**: a deploy can remove a lazy chunk the open tab still expects. On the resulting `vite:preloadError` the app reloads **once**, guarded by a `sessionStorage` marker, so a genuinely missing chunk falls through to the error boundary and Sentry instead of reload-looping.

## Testing Your Deployment

### Functionality Checklist

- [ ] Homepage loads correctly
- [ ] Sign up page works
- [ ] Email verification works
- [ ] Login page works
- [ ] OTP authentication works (if enabled)
- [ ] Google sign-in works, including from an installed PWA
- [ ] Password reset works
- [ ] Territory selector displays (for conductors and administrators)
- [ ] Maps load and display correctly
- [ ] Map tiles, address search and Directions all work (Geoapify key is valid)
- [ ] Can view territories
- [ ] Can update address status
- [ ] Assignment links work
- [ ] Links expire correctly
- [ ] Mobile view is responsive
- [ ] Language selector switches language and the choice survives a reload
- [ ] All modals open and close properly

### Performance Checks

- [ ] Initial page load under 3 seconds
- [ ] Lighthouse score > 90
- [ ] No console errors
- [ ] Images load quickly
- [ ] Maps are responsive
- [ ] Smooth scrolling and navigation
- [ ] Only one translation chunk is downloaded on first load

### Security Checks

- [ ] HTTPS is enforced (no HTTP access)
- [ ] Privacy policy link works
- [ ] Terms of service link works
- [ ] No secret is exposed through a `VITE_`-prefixed variable
- [ ] `SENTRY_AUTH_TOKEN` is set only in CI, never in `.env` committed to the repository
- [ ] No `.map` files are served from `build/assets/`
- [ ] CORS configured correctly for backend
- [ ] PocketBase connection is secure

### Browser Testing

Test on multiple browsers:

- [ ] Chrome/Chromium
- [ ] Firefox
- [ ] Safari (macOS/iOS)
- [ ] Edge
- [ ] Mobile browsers
- [ ] Safari Private Browsing (IndexedDB is unavailable there; the app should fall back to writing directly to the server)

## Troubleshooting

### "Missing PocketBase URL" Setup Page

**Problem**: The app renders a setup page instead of the sign-in screen

**Solutions:**

- `VITE_POCKETBASE_URL` was not present **at build time** — setting it on the host afterwards changes nothing
- Confirm the variable is defined in your host's build environment, then rebuild and redeploy
- Check for a typo in the prefix: `VITE_POCKETBASE_URL`, not `POCKETBASE_URL`

### Backend Connection Failed

**Problem**: "Cannot connect to server" or "Network Error"

**Solutions:**

- Verify `VITE_POCKETBASE_URL` is correct (no trailing slash)
- Check if PocketBase backend is running
- Test backend URL directly in browser
- Check allowed origins in PocketBase (must include your frontend domain)
- Open browser DevTools → Network tab to see exact error
- Ensure backend is accessible from your deployment location

### Maps Are Blank or Search Returns Nothing

**Problem**: Tiles don't load, address autocomplete is empty, or Directions never draws a route

**Solutions:**

- Verify `VITE_GEOAPIFY_API_KEY` is set and was present at build time — all three features share this one key, so all three fail together
- Check the Geoapify dashboard for a quota overrun
- Remove any leftover `VITE_OPENROUTE_API_KEY` or `VITE_LOCATIONIQ_API_KEY` lines; those providers were removed and setting them has no effect
- Open DevTools → Network and look for `401`/`403` responses from `api.geoapify.com`

### Build Errors

**Problem**: `npm run build` fails

**Solutions:**

- Ensure all environment variables are set correctly
- Run `npm install` to refresh dependencies
- Delete `node_modules` and reinstall: `rm -rf node_modules && npm install`
- Check Node.js version: `node --version` (needs >=24.0.0)
- Check for TypeScript errors: Look at specific error messages
- Try `npm run prettier:fix` to fix formatting issues
- Clear Vite cache: `rm -rf node_modules/.vite`

### TypeScript Errors

**Problem**: Type errors during build

**Solutions:**

- Run `npx tsc --noEmit` to see all type errors
- Check `src/utils/interface.ts` for the shared domain types and `src/env.d.ts` for the environment-variable declarations
- Remember the path alias: imports use `@/` for `src/`
- Ensure all dependencies are installed

### Styles Look Wrong After Editing a Component

**Problem**: A component under `src/components/ui/` renders unstyled or loses spacing

**Solutions:**

- Check whether a `data-slot` attribute was removed or renamed — Tailwind selectors elsewhere target these, and nothing in TypeScript will flag the break
- Remember Tailwind 4 is CSS-first: there is **no `tailwind.config`**. Theme tokens live in `src/index.css`
- Confirm the class string is statically visible; Tailwind cannot see class names assembled at runtime from fragments

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
- Build with `ANALYZE=1 npm run build` and open `stats.html` to check that React is not being pulled into another vendor chunk

### PWA Installation Issues

**Problem**: "Add to Home Screen" doesn't appear

**Solutions:**

- Must be served over HTTPS
- Check service worker registration in DevTools
- Confirm `public/site.webmanifest` is being served and is linked from `index.html` — the plugin is configured with `manifest: false`, so this file is the only manifest
- Check that the manifest request is not being answered with `index.html`; that indicates the `navigateFallbackDenylist` is not in effect
- Try different browser (Safari, Chrome have different PWA support)

### Installed App Stays on an Old Version

**Problem**: The home-screen app keeps showing an older release

**Solutions:**

- Confirm `public/version.json` exists in the deployed output — it is generated by `prebuild`, so a build that skipped that step ships without it
- Check that `/version.json` returns JSON rather than the HTML shell
- Switch away from the app and back; the resume path bypasses the 5-minute throttle deliberately
- Look for the "Update Available" toast; the app never reloads on its own

### Real-time Updates Not Working

**Problem**: Changes don't appear for other users

**Solutions:**

- Verify PocketBase realtime subscriptions are working
- Check browser console for SSE (Server-Sent Events) errors
- Ensure backend SSE endpoint is accessible
- If a reverse proxy sits in front of PocketBase, confirm response buffering is disabled — a buffering proxy holds SSE messages and the stream appears dead
- Check if multiple users are actually viewing the same territory
- Refresh the page to force reconnection

## Maintenance

### Keeping Dependencies Updated

```bash
# Pull latest changes from repository
git pull origin master

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

**Geoapify Dashboard:**

- Monitor request usage against your plan's quota — tiles, geocoding and routing all draw on the same key, so map browsing dominates the count
- Check for unusual spikes in usage
- Review billing alerts

**Umami:**

- Review page and event traffic if analytics are configured
- Confirm `VITE_UMAMI_DOMAINS` still matches your production hostnames, so preview deployments don't pollute the data

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

Ministry Mapper uses semantic versioning, generated by `semantic-release` from the Conventional Commit history. The current release is **2.7.2**.

- **Major version** (x.0.0): Breaking changes — from a `feat!:` or `fix!:` commit
- **Minor version** (2.x.0): New features — from a `feat:` commit
- **Patch version** (2.7.x): Bug fixes — from a `fix:` commit

You never edit the version by hand: the released version is whatever the commit messages imply.

## Next Steps

**For Users:**

- Read the [User Guide](user-guide.md) to learn how to use the application

**For Administrators:**

- Set up the PocketBase backend (see ministry-mapper-be repository)
- Configure congregation settings
- Invite users and assign roles
- Create territories and add addresses
