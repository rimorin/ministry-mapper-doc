# Backend Setup Guide

!!! warning "Self-Hosting Not Recommended"
    This guide is for advanced users who want to self-host Ministry Mapper. We don't encourage self-hosting due to the technical complexity, maintenance burden, and security responsibilities involved.

    **Most users should**: Use our hosted service at **[ministry-mapper.com](https://ministry-mapper.com)**

!!! info "Looking for User Documentation?"
    If you're a regular user trying to learn how to use Ministry Mapper, see the [Getting Started](getting-started.md) or [User Guide](user-guide.md) instead.

## Overview

The Ministry Mapper backend (ministry-mapper-be) is built with Go and PocketBase. It provides:

- Data storage and management (SQLite database)
- User authentication and authorization
- Real-time data synchronization (Server-Sent Events)
- Custom API endpoints for territory management
- Background jobs for automated tasks
- Email notifications (MailerSend for digests and reports, SMTP for auth mail)

**This guide assumes you have**:
- Experience with server administration
- Understanding of Docker and containerization
- Familiarity with environment variables
- Knowledge of security best practices

For complete self-hosting instructions, see the [Self-Hosting Guide](self-hosting.md).

## Quick Reference

This page provides a quick reference for backend configuration. **For complete setup instructions, see the [Self-Hosting Guide](self-hosting.md).**

### Technology Stack

- **Go 1.27**: the toolchain version pinned in `go.mod`
- **PocketBase v0.40.0**: backend framework with a built-in admin panel
- **SQLite**: the entire database lives in the `pb_data` folder, reached through the pure-Go `modernc.org/sqlite` driver (which is why the binary builds with `CGO_ENABLED=0`)
- **Docker**: multi-stage build — `golang:1.27.0-alpine` builder, `alpine:3.24` runtime

### Key Features

- Data storage and management
- User authentication
- Real-time subscriptions (Server-Sent Events)
- Custom API endpoints
- Scheduled background jobs (8 cron jobs)
- Email notifications (MailerSend + SMTP)
- Error tracking (Sentry)
- Feature flags (LaunchDarkly)
- Optional AI report and digest summaries (OpenAI)

### Prerequisites

- **Go 1.27** — the version in `go.mod`. An older toolchain will download 1.27 at build time; the Docker builder pins `golang:1.27.0-alpine` to avoid that.
- **The `sqlite3` command-line tool** — a hard dependency of `./scripts/test.sh`, which uses it to checkpoint the WAL and to verify seed counts. CI installs it explicitly (`sudo apt-get install -y sqlite3`). Without it the integration suite cannot run.
- **Docker** with BuildKit enabled (the Dockerfile uses a `--mount=type=cache` build step) if you intend to build the image.
- Roughly **2 GB of RAM to build** — see [About the Docker Image](#about-the-docker-image).

---

!!! note "Complete Setup Instructions"
    The sections below provide a technical reference for backend configuration. For step-by-step setup instructions with context and explanations, please see the **[Self-Hosting Guide](self-hosting.md)**.

---

## Environment Variables Explained

The backend reads **25** environment variables. They fall into two very different groups, and mixing them up is the single most common self-hosting mistake.

!!! warning "Migration-Read Variables Only Take Effect Once"
    Sixteen of the variables below are read **only by migrations**, which means they apply **only on the first migration run against a given database**. Once `pb_data` exists, editing them and restarting does nothing — the settings already live in the database and must be changed in the admin panel (or by starting from an empty `pb_data`).

    The migration-read variables are: `PB_APP_NAME`, `PB_APP_URL`, `PB_ADMIN_EMAIL`, `PB_ADMIN_PASSWORD`, `PB_SMTP_HOST`, `PB_SMTP_PORT`, `PB_SMTP_USERNAME`, `PB_SMTP_PASSWORD`, `PB_SMTP_SENDER_ADDRESS`, `PB_SMTP_SENDER_NAME`, `PB_HIDE_CONTROLS`, `PB_ENABLE_RATE_LIMITING`, `PB_OTP_ENABLED`, `PB_MFA_ENABLED`, `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`.

    (`PB_APP_URL` is the one exception that is *also* read at runtime, for the links inside lifecycle emails.)

    The remaining variables — `SOURCE_COMMIT`, `SENTRY_DSN`, `SENTRY_ENV`, `PB_ALLOW_ORIGINS`, `PB_APP_URL`, `LAUNCHDARKLY_SDK_KEY`, `LAUNCHDARKLY_CONTEXT_KEY`, `MAILERSEND_API_KEY`, `MAILERSEND_FROM_EMAIL`, `OPENAI_API_KEY` — are read by the running application, so a restart is enough to pick up a change.

!!! info "Nothing Is Strictly Required"
    Every variable has a fallback value or a "feature disabled" path, so the server starts with an empty `.env`. The only practical requirement is **MailerSend**: without `MAILERSEND_API_KEY` and `MAILERSEND_FROM_EMAIL` the email jobs (digests, reports, lifecycle notices) have nowhere to send and fail. Treat the "Required" column below as guidance about what breaks, not as a start-up requirement.

### Service Integration Keys

```bash
# MailerSend API key - used by all digest and report emails
MAILERSEND_API_KEY=

# Email address for MailerSend sender (the display name is hard-coded "Ministry Mapper")
MAILERSEND_FROM_EMAIL=

# LaunchDarkly SDK key - for feature flags to control background jobs
# The 8 job flags default to ENABLED if not configured
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=

# OpenAI API key - for AI-generated summaries in reports and digests
# Optional: reports and digests work fine without this; AI sections are simply omitted
OPENAI_API_KEY=
```

#### Email (MailerSend)

| Variable | Required | Description |
|----------|----------|-------------|
| `MAILERSEND_API_KEY` | Needed for email | MailerSend API key. Used for every digest, report and lifecycle email. Unset means those sends error out — the server still runs. This is **not** a fallback for SMTP: MailerSend and SMTP serve different purposes (see below). |
| `MAILERSEND_FROM_EMAIL` | Needed for email | Sender email address for MailerSend. The display name is hard-coded to "Ministry Mapper". |

!!! note "MailerSend and SMTP Are Not Alternatives"
    MailerSend sends the application's own mail — message digests, notes digests, instruction digests, new-address digests, monthly reports and user-lifecycle notices. The `PB_SMTP_*` settings configure PocketBase's own transactional mail only: email verification, password reset, email change and OTP. Configuring one does not cover the other.

#### Feature Flags (LaunchDarkly)

| Variable | Required | Description |
|----------|----------|-------------|
| `LAUNCHDARKLY_SDK_KEY` | Optional | LaunchDarkly server-side SDK key. Unset means no client is created: the 8 job flags then evaluate to **enabled**, and `enable-report-ai-summary` evaluates to **disabled**. |
| `LAUNCHDARKLY_CONTEXT_KEY` | Optional | Context key for the evaluation context (kind `environment`), e.g. `production`. |

#### AI Summaries (OpenAI)

| Variable | Required | Description |
|----------|----------|-------------|
| `OPENAI_API_KEY` | Optional | OpenAI API key. Unset means no client is created and every AI section is omitted. Setting it is **not sufficient on its own** — the `enable-report-ai-summary` flag must also be on, and that flag is off when LaunchDarkly is unconfigured. |

### Application Settings

```bash
# Name shown in emails and admin panel
PB_APP_NAME=Ministry Mapper

# URL where your frontend is hosted - also used in lifecycle email links
PB_APP_URL=http://localhost:3000
```

| Variable | Required | Description |
|----------|----------|-------------|
| `PB_APP_NAME` | Optional | PocketBase application name. Defaults to `Ministry Mapper`. Migration-read. |
| `PB_APP_URL` | Recommended | Frontend URL. Written into PocketBase settings by migration **and** read at runtime for the links in unprovisioned/inactive user emails. Defaults to a placeholder domain, so set it or those emails link nowhere useful. |

### Default Admin Account

```bash
# Initial superuser created on the FIRST migration run
# The shipped defaults are public - change them before you ever expose the server
PB_ADMIN_EMAIL=testing_account@ministry-mapper.com
PB_ADMIN_PASSWORD=pb123456789
```

!!! warning "Change These Before First Start — Not After"
    `testing_account@ministry-mapper.com` / `pb123456789` are the **defaults compiled into the code** and published in this documentation. If you start the server without setting your own values, that account is created with that password and anyone who reads these docs can log into your admin panel.

    Because this is a migration-read setting, changing `.env` afterwards does **not** update the account. Fix it either by setting both variables before the very first start, or — if you already started — by changing the password in the admin panel at `/_/` immediately.

    The superuser is created automatically; there is no manual admin-creation step.

| Variable | Required | Description |
|----------|----------|-------------|
| `PB_ADMIN_EMAIL` | Set it | Bootstrap superuser email. Migration-read; skipped if that email already exists. |
| `PB_ADMIN_PASSWORD` | Set it | Bootstrap superuser password. Migration-read. |

### Email Configuration (SMTP)

These configure PocketBase's built-in mail (verification, password reset, OTP) — not the digests and reports.

```bash
# SMTP server address (e.g., smtp.gmail.com, smtp.mailersend.net)
PB_SMTP_HOST=

# SMTP password (use App Password for Gmail, not your regular password)
PB_SMTP_PASSWORD=

# SMTP username (usually your email address)
PB_SMTP_USERNAME=

# SMTP port (587 for TLS, 465 for SSL)
PB_SMTP_PORT=587

# Email address shown in the "From" field
PB_SMTP_SENDER_ADDRESS=support@ministry-mapper.com

# Name shown in the "From" field
PB_SMTP_SENDER_NAME=MM Support
```

| Variable | Required | Description |
|----------|----------|-------------|
| `PB_SMTP_HOST` | Optional | SMTP host. Defaults to `smtp.gmail.com`. Migration-read. |
| `PB_SMTP_PORT` | Optional | SMTP port; falls back to `587` if the value is not a number. Migration-read. |
| `PB_SMTP_USERNAME` | Optional | SMTP username. Has a placeholder default. Migration-read. |
| `PB_SMTP_PASSWORD` | Optional | SMTP password. Has a placeholder default — set a real one. Migration-read. |
| `PB_SMTP_SENDER_ADDRESS` | Optional | "From" address for PocketBase mail. Migration-read. |
| `PB_SMTP_SENDER_NAME` | Optional | "From" display name for PocketBase mail. Migration-read. |

### Security & Access Control

```bash
# Comma-separated list of allowed origins for CORS
# Use * for development, specific domains for production
# Example: https://app.example.com,https://app2.example.com
PB_ALLOW_ORIGINS=*

# Hide the admin UI controls (true/false)
# Set to true in production
PB_HIDE_CONTROLS=false
```

| Variable | Required | Description |
|----------|----------|-------------|
| `PB_ALLOW_ORIGINS` | Optional | CORS allow list, split on commas. Defaults to `*` — set your real origins in production. Runtime-read, so a restart applies it. |
| `PB_HIDE_CONTROLS` | Optional | Hides the PocketBase admin UI controls. Defaults to `false`. Migration-read. |

### Authentication Settings

```bash
# Enable one-time password login - must be the exact lowercase string "true"
PB_OTP_ENABLED=false

# Enable multi-factor authentication - must be the exact lowercase string "true"
PB_MFA_ENABLED=false
```

!!! warning "These Two Are Compared Against the Literal String `true`"
    `PB_OTP_ENABLED` and `PB_MFA_ENABLED` are evaluated as an exact string comparison against `true`. `TRUE`, `True`, `1` and `yes` all silently mean *disabled*. Unlike the other boolean settings on this page, these do not go through a permissive boolean parse.

    Both are also migration-read, so they only matter on the first run. In an existing database, OTP and MFA are toggled per collection in the admin panel (Collections → `users` → Options).

| Variable | Required | Description |
|----------|----------|-------------|
| `PB_OTP_ENABLED` | Optional | Enables email OTP on the `users` collection (4-digit code, 180-second validity). Migration-read; literal `true` only. |
| `PB_MFA_ENABLED` | Optional | Enables PocketBase MFA on the `users` collection (email OTP as the second factor — there is no TOTP and no backup codes). Migration-read; literal `true` only. |

### Google Sign-In (Missing from `.env.sample`)

```bash
# Google OAuth2 credentials - NOT present in .env.sample
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
```

!!! warning "Nothing in `.env.sample` Hints That Google Sign-In Is Configurable"
    `GOOGLE_CLIENT_ID` and `GOOGLE_CLIENT_SECRET` are read by a migration that enables the Google OAuth2 provider on the `users` collection, but neither appears in `.env.sample`. If you copy the sample and never read the source, Google sign-in simply never gets configured and nothing tells you why.

    Both must be set, and — being migration-read — they must be set **before the first migration run**. On an existing database, add the provider in the admin panel instead (Collections → `users` → Options → OAuth2).

| Variable | Required | Description |
|----------|----------|-------------|
| `GOOGLE_CLIENT_ID` | Optional | Google OAuth2 client id. Both this and the secret must be set or the provider is skipped. Migration-read. |
| `GOOGLE_CLIENT_SECRET` | Optional | Google OAuth2 client secret. Migration-read. |

### Rate Limiting

```bash
# Enable PocketBase rate limiting (true/false)
PB_ENABLE_RATE_LIMITING=false
```

| Variable | Required | Description |
|----------|----------|-------------|
| `PB_ENABLE_RATE_LIMITING` | Optional | Turns on PocketBase's rate limiter. Defaults to `false`. Migration-read — in an existing database use Settings → Rate limiting in the admin panel. |

### Error Tracking and Build Metadata

`SENTRY_DSN`, `SENTRY_ENV` and `SOURCE_COMMIT` are read at runtime. `SENTRY_DSN` and `SENTRY_ENV` are in `.env.sample`; `SOURCE_COMMIT` is not, because hosting platforms inject it.

```bash
# Sentry DSN for error monitoring and tracking - empty disables the SDK
SENTRY_DSN=

# Environment name for Sentry (development, staging, production)
SENTRY_ENV=production

# Git commit hash - automatically injected by the hosting platform (Coolify)
SOURCE_COMMIT=
```

| Variable | Required | Description |
|----------|----------|-------------|
| `SENTRY_DSN` | Optional | Sentry DSN. Empty means the Sentry SDK is disabled. |
| `SENTRY_ENV` | Optional | Sentry environment; also selects the trace sample rate (`production` 0.2, `staging` 0.5, anything else enables debug mode). Defaults to `development`. |
| `SOURCE_COMMIT` | Optional | Commit SHA logged at boot and used as the Sentry release. Defaults to `development-build`. |

## Feature Flags

Ministry Mapper uses [LaunchDarkly](https://launchdarkly.com) to gate background job execution. Each job can be independently enabled or disabled without redeployment. Flags are read at fire time, so a flag change takes effect on the job's next run.

There are **8 job flags plus one non-job flag**:

| Flag Key | Controlled Job | Description |
|----------|----------------|-------------|
| `enable-assignments-cleanup` | `cleanUpAssignments` | Delete expired map assignments and log them as `expired` (every 5 min) |
| `enable-message-processing` | `processMessages` | Email a digest of unread publisher messages per congregation (every 30 min) |
| `enable-instruction-processing` | `processInstructions` | Email pinned administrator instructions to a map's publishers (every 30 min) |
| `enable-note-processing` | `processNotes` | Email a digest of recently updated address notes (hourly) |
| `enable-monthly-report` | `generateMonthlyReport` | Generate and email the Excel territory activity report (1st of the month) |
| `enable-unprovisioned-user-processing` | `processUnprovisionedUsers` | Warn, disable and eventually delete accounts with no congregation role (daily) |
| `enable-inactive-user-processing` | `processInactiveUsers` | Warn and disable inactive accounts — NIST SP 800-53 AC-2(3) (daily) |
| `enable-new-addresses-notification` | `processNewAddresses` | Email a daily digest of addresses added from the app (daily) |
| `enable-report-ai-summary` | *not a job* | Adds the OpenAI-generated summary to reports and digests. Read on demand, not on a schedule. |

!!! warning "The Defaults Are Not Symmetrical"
    Without `LAUNCHDARKLY_SDK_KEY`, no LaunchDarkly client is created, and the two groups of flags then behave in opposite ways:

    - The **8 job flags default to enabled** — every job runs on schedule.
    - **`enable-report-ai-summary` defaults to disabled** — so AI summaries are **off** on a self-hosted install with no LaunchDarkly, even when `OPENAI_API_KEY` is set.

    Flags are scoped to the deployment environment (the LaunchDarkly context is of kind `environment`). There is no per-congregation flag control.

## Installation Steps

### Option 1: Docker Deployment (Recommended)

Docker makes deployment simple and consistent across platforms.

#### 1. Build the Docker Image

```bash
docker build -t ministry-mapper-backend .
```

#### 2. Run the Container

```bash
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**Important Notes:**

- The container runs on port **8080** (not 8090)
- `-p 8080:8080` maps the container's port 8080 to your host port 8080
- `-v /path/to/pb_data:/app/pb_data` saves your database permanently (replace `/path/to/pb_data` with a real path on your system). `pb_data` is the **entire** application state — `data.db`, `auxiliary.db`, `logs.db` and the PocketBase configuration. Losing it means losing everything.
- `--env-file .env` loads your environment variables from the `.env` file
- `-d` runs the container in the background (detached mode)
- There is no `docker-compose.yml` in the backend repository — the raw `docker build` / `docker run` pair above is the supported path.

#### About the Docker Image

Worth knowing before you build or deploy:

- **`.dockerignore` is an allowlist.** It denies everything with `*` and then un-ignores exactly `go.mod`, `go.sum`, `main.go`, `internal/`, `migrations/` and `templates/`. That is deliberate: the previous two-line denylist let `COPY . .` sweep the developer's **`.env`** (with live MailerSend, LaunchDarkly, SMTP and Sentry credentials) and an **~880 MB production database dump** into the builder stage. The published image was never affected — the runtime stage copies only the binary and templates — but a builder layer carrying credentials leaks the moment build cache is exported. The allowlist cut the build context from **911 MB to 815 kB** and the builder image from **2.89 GB to 1.66 GB**.
- **A BuildKit cache mount holds the Go build cache** (`--mount=type=cache,target=/root/.cache/go-build`). Because `COPY . .` invalidates the build layer, a rebuild without it recompiled all 497 packages including the SQLite driver's; with it a one-line change rebuilds in **3.7 s instead of 21.4 s**. Module downloads are a plain layer on purpose, so a source-only change never re-downloads them.
- **`GOARCH=amd64` only.** There is no arm64 or multi-arch build. On an Apple Silicon machine you are building an emulated amd64 image.
- **Cold builds peak at about 1.76 GB** — the single-threaded linker, not the compiler. A 1.9 GB host should build the image elsewhere and pull it rather than building in place.
- **The container runs as root.** There is no `USER` directive.
- **There is no `HEALTHCHECK` instruction** in the Dockerfile. The probe is configured platform-side, which is exactly why `curl` is installed in the runtime image. Point it at `GET /api/db-health`.
- **The final image is about 73.6 MB** — the stripped binary (`CGO_ENABLED=0`, `-ldflags="-w -s"`) plus `templates/` on top of `alpine:3.24` with `tzdata` and `curl`.
- **`templates/` must sit beside the binary.** Email templates are parsed by relative path at runtime, so the working directory has to contain `templates/`.

!!! info "Hosting Platform"
    The hosted service runs on **Coolify**, which builds the image straight from the repository, with Cloudflare in front. `curl` exists in the runtime image specifically for the Coolify healthcheck. There is no deploy or image-publish GitHub workflow — the repository's two workflows are pull-request checks only.

### Option 2: Local Development

For development and testing on your computer:

#### 1. Install Go Dependencies

```bash
./scripts/install.sh
```

This script runs `go get ./...` and `go mod tidy`, then installs `.githooks/pre-push` into `.git/hooks/`.

#### 2. Set Up Environment Variables

```bash
cp .env.sample .env
# Edit .env - at minimum PB_ADMIN_EMAIL, PB_ADMIN_PASSWORD and MAILERSEND_API_KEY
```

Remember that `.env.sample` does not include `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET` or `SOURCE_COMMIT`.

#### 3. Run the Application

```bash
./scripts/start.sh
```

This script exports environment variables from `.env` and runs `go run main.go serve`.

The backend will start on **http://localhost:8090** by default (PocketBase's default port — the script passes no `--http`). Migrations, including the superuser bootstrap, run automatically on the first start.

## Accessing the Admin Panel

Once your backend is running:

1. Navigate to `http://your-backend-url/_/` (note the underscore and trailing slash)
2. Log in with your admin credentials from `PB_ADMIN_EMAIL` and `PB_ADMIN_PASSWORD`
3. You'll see the PocketBase admin dashboard

### What You Can Do in the Admin Panel

- **Collections**: View and edit all database tables (users, congregations, territories, maps, addresses, etc.)
- **Logs**: View application and request logs
- **Settings**: Configure authentication, email, backups, and more
- **Files**: Manage uploaded files and storage
- **API Rules**: Set permissions for each collection

**Security Note**: Set `PB_HIDE_CONTROLS=true` before the first start to hide the admin UI controls in production. On an existing database, change the equivalent setting in the admin panel.

## Database Management

### Backing Up Your Data

Your data lives in the `pb_data` folder. Regular backups are crucial. **The repository contains no backup tooling** — no cron job, no snapshot script, no restore script. You have two practical options.

#### Option A: PocketBase's Built-In Backups (Recommended)

PocketBase can snapshot `pb_data` itself while the server is running, which avoids copying a database mid-write:

1. Open the admin panel at `/_/`
2. Go to **Settings → Backups**
3. Create a backup, download it, or restore a previous one

The same feature is available over the API at `/api/backups` (superuser authentication required), so a scheduled job on your side can create and fetch snapshots. Note that "automatic backups" are not configured for you — this mechanism is manual or API-driven.

#### Option B: Copying `pb_data` Yourself

If you copy the folder directly, **checkpoint the write-ahead log first** so the `.db` files are complete on their own:

```bash
# Flush the WAL into the main database files before copying
sqlite3 /path/to/pb_data/data.db "PRAGMA wal_checkpoint(TRUNCATE);"
sqlite3 /path/to/pb_data/auxiliary.db "PRAGMA wal_checkpoint(TRUNCATE);"

# Then archive the whole folder
tar -czf "ministry-mapper-backup-$(date +%Y%m%d).tar.gz" /path/to/pb_data/
```

Copying a live `pb_data` without checkpointing can capture a `data.db` whose most recent writes are still sitting in the `-wal` file.

#### Automated Daily Backup

Set up a cron job to run the same sequence:

```bash
# Add to crontab (run: crontab -e)
0 2 * * * sqlite3 /path/to/pb_data/data.db "PRAGMA wal_checkpoint(TRUNCATE);" && tar -czf /backups/ministry-mapper-backup-$(date +\%Y\%m\%d).tar.gz /path/to/pb_data/
```

This runs at 2 AM daily and creates timestamped backups.

#### Restoring from Backup

```bash
# Stop the application first, then extract the archive you want
tar -xzf "/backups/ministry-mapper-backup-$(date +%Y%m%d).tar.gz" -C /path/to/
```

Substitute the timestamp of the archive you are actually restoring. To restore a snapshot created by PocketBase's built-in backups, use **Settings → Backups → Restore** in the admin panel instead.

### Reclaiming Disk Space

Deleting rows or dropping indexes returns pages to SQLite's freelist but not to the filesystem. A manual `VACUUM` is required to shrink the file, and it cannot be done from a migration because migrations run inside a transaction. Run it with the server stopped:

```bash
sqlite3 /path/to/pb_data/data.db "VACUUM;"
```

## Security Checklist

Before going to production:

- [ ] Set `PB_ADMIN_EMAIL` and `PB_ADMIN_PASSWORD` **before the first start** — the compiled defaults are published in this guide
- [ ] If you already started with the defaults, change the admin password in `/_/` immediately
- [ ] Set `PB_ALLOW_ORIGINS` to your actual domain(s), not `*`
- [ ] Enable HTTPS (never use HTTP in production)
- [ ] Set `PB_HIDE_CONTROLS=true` before the first start to hide the admin UI controls
- [ ] Consider enabling rate limiting with `PB_ENABLE_RATE_LIMITING=true`
- [ ] Configure Sentry (`SENTRY_DSN` and `SENTRY_ENV`) for error monitoring
- [ ] Point your platform's healthcheck at `GET /api/db-health`
- [ ] Set up backups (PocketBase Settings → Backups, or your own checkpoint-and-archive job)
- [ ] Keep `.env` out of the Docker build context — the shipped `.dockerignore` allowlist already does this; don't loosen it
- [ ] Use strong, unique passwords for all accounts
- [ ] For Gmail SMTP, use App Passwords instead of your regular password
- [ ] Keep Go dependencies updated regularly with `./scripts/update.sh`

## Updating the Backend

To update to a new version:

### Docker Update

```bash
# Pull the latest code
git pull origin main

# Rebuild the Docker image
docker build -t ministry-mapper-backend .

# Stop and remove the old container
docker stop ministry-mapper
docker rm ministry-mapper

# Start the new container (use port 8080, not 8090)
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

### Local Development Update

```bash
# Pull the latest code
git pull origin main

# Update dependencies
./scripts/update.sh

# Restart the application
./scripts/start.sh
```

**Note**: Your data in `pb_data` is preserved during updates. Any new migrations shipped with the update apply automatically on start.

## Database Migrations

Migrations live in the `migrations/` folder and **apply automatically when the server starts** (`serve`) — in development and in production alike. You'll see messages in the logs like:

```
Running migrations...
Migration 1777788260_collections_snapshot completed
```

**How it Works:**

- `1777788260_collections_snapshot.go` is the authoritative schema snapshot. It is applied with `deleteMissing = true`, so anything absent from the snapshot is dropped from the database.
- The superuser account is created by the `initial_admin_usr.go` migration on the first run, from `PB_ADMIN_EMAIL` / `PB_ADMIN_PASSWORD`. **There is no manual admin-creation step.** If you ever need the escape hatch, PocketBase still offers `go run main.go superuser create <email> <password>`.
- **There is no manual `./main migrate` step in the normal flow.** Pending migrations run at bootstrap.
- What `go run` adds is *automigrate*: schema edits made in the admin UI generate new migration files, but **only** when the process was started with `go run`. A compiled production binary never automigrates.
- Environment variables read inside migrations apply only on the first migration run against a database — see the callout in [Environment Variables Explained](#environment-variables-explained).

**Important:** Never delete or modify migration files manually. This can break your database.

## Console Commands

The binary is a PocketBase application, so it inherits `serve`, `superuser`, `migrate` and the rest. One custom command is registered.

### `fix-sequences`

```bash
# Dry run - report what would change, write nothing
./main fix-sequences

# In local development
go run main.go fix-sequences

# Apply the repair
./main fix-sequences --apply

# Also repair maps with no dominant column direction
./main fix-sequences --apply --include-unclear

# Limit the scan to a single map
./main fix-sequences --map <map_id>
```

| Flag | Effect |
|------|--------|
| `--apply` | Actually write the renumbering. **Without it the command is a dry run** and ends with `Dry run - nothing written. Re-run with --apply to write.` |
| `--include-unclear` | Also repair maps where the column direction could not be determined confidently. Skipped by default. |
| `--map <id>` | Restrict the scan to one map instead of the whole database. |

**What it repairs.** Every address column in a map is identified by its `sequence`, and the invariant is **one `sequence` per `code` per map**. The command sweeps for both ways that can break:

- two different codes sharing the same `sequence`, and
- one code holding two different `sequence` values.

**Why an operator needs it.** A map renders one row of column headers taken from its highest floor while each floor sorts its own units, so a tied pair can appear under its neighbour's header on floors where the two units differ in status. Worse, the Excel report keys its grid on `sequence` — so the second of a tied pair silently overwrites the first and **disappears from the export entirely**. When the command was introduced, 451 production maps were affected (413 with a shared sequence, 38 with a split code).

**What it does.** For each affected map it picks each code's dominant sequence and renumbers the surviving columns **0..N-1**, preserving the map's own column direction — a corridor numbered high-to-low is as legitimate as one numbered low-to-high. Only tied codes move relative to each other; untied columns keep their order. Before writing, the plan is verified to be a gapless `0..N-1` permutation with no duplicate assignment.

**What it skips.**

- Maps where a code holds more than one sequence are **skipped entirely** (reported as `SKIPPED - duplicate unit records`), because renumbering cannot decide which duplicate record to keep.
- Maps with no dominant column direction are reported as `UNCLEAR - no dominant column direction` and skipped unless `--include-unclear` is passed.
- When nothing is wrong it prints `No sequence collisions found.` and exits 0.

!!! note "Repairs Are Silent by Design"
    The renumbering is written with raw SQL rather than through the record API. That deliberately avoids stamping `updated` / `updated_by` and avoids broadcasting realtime events, so publishers working the map are not spammed with updates while a repair runs.

    New collisions can no longer be introduced by the application: the address-adding endpoints read the high-water sequence *inside* their transaction, adding a floor copies an existing floor's sequences, and the code-reordering endpoint rejects collisions and partial payloads. `fix-sequences` is for cleaning up historical data.

## Testing

Tests come in two tiers, split by the `testdata` build tag.

```bash
# Tier 1 - unit tests. No database, nothing spun up.
go test ./...

# Tier 2 - integration tests. Requires the sqlite3 CLI on PATH.
./scripts/test.sh
```

**Unit tests** (`go test ./...`) cover handlers, middleware, jobs and console commands — roughly 90 top-level tests. CI runs the same set excluding `internal/setup`. Integration test files carry `//go:build testdata`, so plain `go test` does not compile them.

**Integration tests** (`./scripts/test.sh`) do rather more than run `go test`. The script:

1. Kills anything left on port 19090 and deletes `test_pb_data/` and the old test binary
2. Builds a `-tags testdata` server binary — the tag pulls in the seed-data migration
3. Boots it on `127.0.0.1:19090` with `--dir=test_pb_data`, purely so migrations and the seed run into a fresh database
4. Polls `/api/health` once a second for up to 60 seconds; failing to come up is a hard error
5. Stops the bootstrap server
6. Runs `PRAGMA wal_checkpoint(TRUNCATE)` on `data.db` and `auxiliary.db` with the **`sqlite3` CLI** so the files are fully flushed before tests open them
7. Gates on seed counts — at least 2 congregations, 5 users and 30 addresses
8. Runs `go test -tags testdata -v -timeout 120s ./internal/setup/ ./internal/jobs/`

An `EXIT`/`INT`/`TERM` trap removes `test_pb_data/` and the test binary. No live server runs during the tests themselves — each test app copies the seeded database to its own temporary directory. Seeded ids are stable and meant to be hard-coded in tests; every seeded user has the password `Test1234!`.

!!! warning "The Test Script Does Not Clear Your Environment"
    `scripts/test.sh` contains no `unset` and no `env -i`, so the bootstrap server inherits whatever is exported in your shell. If you run it in a session that already exported a real `.env`, your production `PB_ADMIN_*` and SMTP values are written into the generated test database. Run it from a clean shell.

**Pre-push hook.** `.githooks/pre-push` — installed by `./scripts/install.sh` — runs the unit tests and then the full integration suite, and aborts the push if either fails. That is why the `sqlite3` CLI is a prerequisite for contributors, not just for CI.

**CI.** Two pull-request-only workflows: one runs `go mod tidy && go mod verify`, `go build`, `go vet` and the unit tests; the other installs `sqlite3` and runs `./scripts/test.sh`. **`go vet` is the only linter** — there is no golangci-lint. Neither workflow deploys or publishes an image.

## Monitoring and Logs

### Viewing Logs

#### Docker Logs

```bash
# View recent logs
docker logs ministry-mapper

# Follow logs in real-time (useful for debugging)
docker logs -f ministry-mapper

# View last 100 lines
docker logs --tail 100 ministry-mapper
```

#### Application Logs

PocketBase stores its structured logs in the SQLite database **`pb_data/logs.db`** — not in a `pb_data/logs/` folder. Read them through the admin panel's **Logs** section, or query the file directly with the `sqlite3` CLI. They include:

- Request logs
- Error logs
- Custom application logs

When `SENTRY_DSN` is set, every log row is also mirrored to Sentry's structured logging, so errors show up there without a separate integration.

### Health Checks

```bash
# PocketBase's own liveness endpoint
curl http://localhost:8080/api/health

# Ministry Mapper's database integrity probe
curl http://localhost:8080/api/db-health
```

`GET /api/db-health` runs `PRAGMA quick_check` with a 10-second timeout and returns `200` when the database is healthy or `503` with the failure detail when it is not. It exists because a trivial `SELECT 1` succeeds even against a corrupted database — index corruption only surfaces in a structural scan. This is the endpoint to give your platform's healthcheck.

### What to Look For in Logs

- **Startup Messages**: Confirms successful initialization, including the build id from `SOURCE_COMMIT`
- **Migration Messages**: Database migrations being applied
- **Sentry Initialization**: Error tracking setup
- **Cron Jobs**: Background task execution (assignment cleanup, message processing, and so on)
- **Aggregate Hook Failures**: `aggregate hook failed` with a map id means a map's progress was not recalculated
- **Error Messages**: Problems with database, email, API calls, or LaunchDarkly
- **Authentication Events**: Login attempts and user activity
- **Performance Issues**: Slow queries or timeouts

## Troubleshooting

### Port Already in Use

**Problem**: "Port already in use" error

**Solution**:

```bash
# For Docker (port 8080)
lsof -i :8080

# For local development (port 8090)
lsof -i :8090

# Kill the process using the port
kill -9 <process_id>

# Or map to a different port
docker run -p 8081:8080 ...  # Maps host port 8081 to container port 8080
```

### Database Locked

**Problem**: "Database is locked" error

**Solutions**:
- Stop all containers/processes accessing the database
- Check if a backup process is running
- Ensure only one instance of the app is running
- SQLite doesn't support concurrent writes from multiple processes
- Restart the application

### Email Not Sending

**Problem**: Users don't receive emails

**Solutions**:
1. **Identify which mail path is failing**: digests, reports and lifecycle notices go through **MailerSend**; verification, password reset and OTP go through **SMTP**. They are configured separately.
2. **Verify MailerSend**: `MAILERSEND_API_KEY` and `MAILERSEND_FROM_EMAIL` must both be set, or every digest and report send fails
3. **Verify SMTP Settings**: Check all `PB_SMTP_*` variables — and remember they are migration-read, so an existing database keeps whatever was configured on its first run. Change them in the admin panel under Settings → Mail.
4. **Gmail Users**: Use an [App Password](https://support.google.com/accounts/answer/185833), not your regular password
5. **Check that the job is enabled**: a LaunchDarkly flag set to off means the digest never runs
6. **Check Spam Folder**: Emails might be filtered
7. **Review Logs**: Look in the admin panel's Logs section (`pb_data/logs.db`) or the Docker logs
8. **Port Issues**: Ensure port 587 (TLS) or 465 (SSL) is not blocked by firewall

### Out of Memory

**Problem**: Application crashes with memory errors

**Solutions**:
- Increase Docker memory limits: `docker run -m 1g ...`
- If it is the **build** that dies, the culprit is the linker's ~1.76 GB peak, not the running server — build on a larger host and pull the image
- Check logs for memory leaks
- Optimize large database queries
- Upgrade your hosting plan's RAM allocation

### Cannot Access Admin Panel

**Problem**: 404 error when accessing admin URL

**Solutions**:
- Ensure URL format: `http://your-url/_/` (with underscore and trailing slash)
- Check whether the admin UI controls were hidden by `PB_HIDE_CONTROLS=true`
- Verify the application is running: `docker ps` or check process
- Clear browser cache and cookies
- Check logs for startup errors

### Admin Credentials Rejected

**Problem**: `PB_ADMIN_EMAIL` and `PB_ADMIN_PASSWORD` don't work

**Solutions**:
- Remember that these are **migration-read**: they created the superuser on the first migration run and are ignored afterwards. Editing `.env` does not change the password.
- Try the compiled defaults if you never set your own before the first start: `testing_account@ministry-mapper.com` / `pb123456789` — and change the password immediately if they work
- Create a fresh superuser from the command line: `./main superuser create <email> <password>`

### Environment Variable Change Has No Effect

**Problem**: You edited `.env`, restarted, and nothing changed

**Solutions**:
- Check whether the variable is migration-read (app name/URL, admin credentials, SMTP, OAuth, OTP/MFA, hide-controls, rate limiting). Those apply only on the first migration run against a database — change the equivalent setting in the admin panel instead.
- For `PB_OTP_ENABLED` and `PB_MFA_ENABLED`, check the exact value: only the lowercase string `true` counts. `TRUE` and `1` mean disabled.
- Confirm the value actually reached the process: `docker exec ministry-mapper env | grep PB_`

## Performance Tips

### Small Congregations (< 100 users)
- Default settings work well
- 512MB RAM is sufficient to run (building the image needs more)
- SQLite handles this load easily
- No special configuration needed

### Medium Congregations (100-500 users)
- Recommended: 1GB RAM
- Enable rate limiting: `PB_ENABLE_RATE_LIMITING=true`
- Set up daily backups
- Monitor database size in `pb_data/data.db`

### Large Congregations (> 500 users)
- Recommended: 2GB+ RAM
- Enable rate limiting
- Consider dedicated hosting (not shared)
- Monitor logs for slow queries and the `/api/db-health` probe for integrity
- Run `VACUUM` occasionally with the server stopped to return freed pages to the filesystem
- Set up backup retention policy (keep last 30 days)

!!! note "Scaling Is Vertical"
    PocketBase runs on a single embedded SQLite database. There are no read replicas and no PostgreSQL option — scaling means a bigger machine. The application is designed for fewer than 1,000 concurrent users.

## API Endpoints

The backend adds **20 custom routes** on top of PocketBase's built-in API. Every one is `POST` except the health probe, which is `GET`. Authorization differs per group, so the groups below are the important part, not the paths.

### Public / Self-Authenticating

These four are **not** behind the authentication gate — they authorize themselves so publishers holding a map link can use them with no account and no login. The credential is the `link-id` header, whose value is an assignment record id validated against the map and its expiry on every request.

- `POST /link/map` — the publisher's whole payload in one request: expiry, publisher name, map, congregation policy and options, addresses, and whether pinned messages exist. **`link-id` header only** — a JWT alone is not accepted here.
- `POST /map/addresses` — addresses and address options for one map. Accepts a valid `link-id` **or** any congregation role.
- `POST /address/update` — update one address (status, notes, tries, coordinates, household options). Accepts a valid `link-id` or any congregation role; returns `204`. The actor recorded in `updated_by` is derived server-side and cannot be supplied by the client.
- `POST /address/add` — add an address to a map. Accepts a valid `link-id` or any congregation role; returns `201`.

!!! note "A Valid Link Beats a Role"
    When a non-empty `link-id` header is present, the link check decides the request — and if it fails, the request is refused even if the caller also presented a valid administrator token. It never falls back to the role check. A link is scoped to exactly one map.

### Map Management (Administrator Only)

All ten require authentication **and** the `administrator` role in the map's congregation:

- `POST /map/codes` — list the address codes in a map
- `POST /map/code/add` — add a new address code (unit column) to a map
- `POST /map/codes/update` — reorder address codes; the payload must be a **complete, collision-free** ordering
- `POST /map/code/delete` — delete an address code from a map
- `POST /map/floor/add` — add a floor to a map
- `POST /map/floor/remove` — remove a floor from a map
- `POST /map/reset` — reset a map's address statuses
- `POST /map/add` — create a new map
- `POST /map/territory/update` — move a map to another territory (a destination in another congregation is rejected)
- `POST /maps/sequence` — reorder the maps within a territory atomically

### Territory Management

- `POST /territory/reset` — reset every map in a territory. **Administrator or conductor.**
- `POST /territory/delete` — delete a territory and its contents. **Administrator or conductor.**
- `POST /territory/link` — mint a publisher "quicklink" assignment for a map. **Any role in the congregation** (a role is required — it is not open to any authenticated user).

### Options

- `POST /options/update` — update the congregation's household options. **Administrator.**

### Reports

- `POST /report/generate` — generate the territory activity report on demand and email it to the requesting user, covering a rolling 30 days. **Administrator** for that congregation; returns `202` and does the work in the background.

### Health

- `GET /api/db-health` — database integrity probe. **No authentication.** Intended as the platform healthcheck.

**Authentication**: every route above except the four public ones and the health probe requires a valid PocketBase authentication token, with the role check applied inside the handler.

**PocketBase Built-in APIs**: in addition to these custom endpoints, PocketBase provides automatic REST APIs for all collections at `/api/collections/{collection}/records`, plus `/api/realtime` for subscriptions. Those are governed by the collection API rules and by the authorization hooks — notably, `addresses` and `address_options` have all their write rules set to superuser-only, so every address mutation must go through the custom `/address/*` routes.

!!! note "Response Shape"
    `/link/map` and `/map/addresses` return raw bodies such as `{"error":"..."}` rather than PocketBase's standard `{code, message, data}` envelope, and those particular responses are also invisible to the Sentry middleware. Don't assume a uniform error envelope across the whole API.

## Background Jobs (Cron Scheduler)

The backend runs **8 cron jobs**, each gated by its own LaunchDarkly flag. **All schedules are UTC.**

### Scheduled Jobs

1. **Assignment Cleanup** (`1,6,11,...,56 * * * *` — every 5 minutes)
   - Deletes expired map assignments and writes an `expired` row to the assignment audit log
   - Deletion goes through the record API on purpose, so hooks and realtime delete events fire per record
   - Feature flag: `enable-assignments-cleanup`

2. **Message Processing** (`8,38 * * * *` — every 30 minutes)
   - Emails one digest per congregation of unread publisher messages, to that congregation's administrators
   - The 30-minute window only *discovers* which congregations have new messages; the whole unread backlog is then swept, and the batch is marked read once the email is sent
   - Optionally attaches an AI-generated overview
   - Feature flag: `enable-message-processing`

3. **Instruction Processing** (`18,48 * * * *` — every 30 minutes)
   - Emails a map's pinned administrator instructions to that congregation's non-administrators
   - Stays map-scoped, and nothing is marked read — pinned instructions are persistent
   - Optionally attaches an AI-generated overview
   - Feature flag: `enable-instruction-processing`

4. **Note Processing** (`28 * * * *` — hourly)
   - Emails administrators a digest of address notes updated in the last hour
   - Optionally attaches an AI-generated overview
   - Feature flag: `enable-note-processing`

5. **Monthly Report** (`0 18 1 * *` — 1st of each month at 18:00 UTC)
   - Generates the Excel territory activity report for the **previous full calendar month** and emails it to administrators
   - Optionally includes an AI-generated summary — see [AI Report Summaries](#ai-report-summaries)
   - Feature flag: `enable-monthly-report`

6. **Unprovisioned User Processing** (`0 18 * * *` — daily at 18:00 UTC)
   - Enforces the lifecycle for accounts holding no congregation role at all:
     an alert to the PocketBase superusers on first detection → warning email on day 3 → final warning on day 6 → **account disabled on day 7** → **permanently deleted on day 37** (a 30-day investigation window after disabling)
   - The clock restarts if the account's last role was revoked rather than never granted, so a long-tenured user gets a fresh grace period instead of being judged against their signup date
   - Feature flag: `enable-unprovisioned-user-processing`

7. **Inactive User Processing** (`30 18 * * *` — daily at 18:30 UTC)
   - Warns and disables accounts by inactivity, measured from `last_login` (falling back to the creation date for users who never logged in):
     warning at **91 days** → final warning at **152 days** → **disabled at 183 days**. **Inactive accounts are never automatically deleted.**
   - Aligned to NIST SP 800-53 AC-2(3). Any successful login clears both warning stamps, so a returning user restarts the sequence
   - Feature flag: `enable-inactive-user-processing`

8. **New Addresses Notification** (`0 19 * * *` — daily at 19:00 UTC)
   - Emails administrators a digest of addresses added from the app in the last 24 hours, grouped by map
   - Feature flag: `enable-new-addresses-notification`

!!! info "Why 18:00–19:00 UTC?"
    The daily and monthly jobs are deliberately staggered inside that hour: 18:00–19:00 UTC is 02:00–03:00 SGT, well clear of the 08:00–12:00 SGT field-service peak. Each job is also wrapped in panic recovery that reports failures to Sentry with a `job_name` tag, so one failing job cannot take the scheduler down.

!!! warning "Job Flags Default On, the AI Flag Defaults Off"
    With LaunchDarkly unconfigured, all 8 jobs above run. The ninth flag, `enable-report-ai-summary`, is **not** a job and behaves the opposite way — it evaluates to disabled when there is no LaunchDarkly client.

!!! note "There Is No Aggregate Cron"
    Map and territory progress used to be recalculated by a batch cron job. That job no longer exists: progress is now recalculated in real time by a database hook whenever an address's status or not-home tries change. See [Database Hooks](#database-hooks).

### AI Report Summaries

AI summaries are produced by **OpenAI**, using the model **`gpt-5.4-mini`** through Chat Completions: the system prompt is sent as a developer message, the response is requested in **JSON mode** (`response_format: json_object`), the temperature is **0.3**, and the call has a **90-second** timeout.

**Both of these are required:**

1. `OPENAI_API_KEY` must be set. Unset means no client is created and every AI section is omitted.
2. The LaunchDarkly flag `enable-report-ai-summary` must be on.

!!! warning "Unlike the Job Flags, This One Defaults to Off"
    If LaunchDarkly is not configured, `enable-report-ai-summary` evaluates to **disabled** — so **AI summaries stay off even when `OPENAI_API_KEY` is set**. Self-hosters who want them need a LaunchDarkly project with the flag turned on.

There are **four separate prompts**, covering four different things:

| What is summarized | Scope | Output |
|--------------------|-------|--------|
| Territory activity report (monthly job and on-demand `/report/generate`) | Congregation | `covered_activity`, `territory_analysis`, `conclusion` — written in the voice of the congregation's territory servant, for the service overseer and elders, from five analytics views |
| Publisher messages digest | Congregation | `overview` plus `key_themes`, including action items such as address-data corrections |
| Address notes digest | Congregation | `overview` — property notes: dogs, gate access, intercom, parking, renovations, vacancy |
| Pinned instructions digest | One map | `overview` — an administrator's pinned instructions, summarized for the publishers who receive them |

**Failure is always graceful.** A missing key, an API error or an unparseable response all produce an unavailable summary, and the email simply omits that section. Nothing else in the report or digest is affected.

!!! warning "What Leaves Your Server"
    When the feature is enabled, the **text of publisher messages and of address notes is sent to OpenAI**, along with the aggregate statistics behind the report. If that is not acceptable for your congregation, leave `OPENAI_API_KEY` unset — the flag alone cannot enable the feature.

## Database Hooks

A number of behaviours are implemented as PocketBase hooks rather than as endpoints or jobs, which is why they happen "by themselves":

- **Map and territory progress, recalculated in real time.** An after-update hook on `addresses` recalculates the map's aggregates whenever `status` or `not_home_tries` actually changed, then rolls the result up into the territory. **This hook replaced the old batch aggregate cron job.** Notes-only or actor-only edits do no work at all, and the recalculation runs in a tracked background routine so the request is not held up.
- **Bulk-operation suppression.** Bulk handlers (map reset, territory reset, and similar) set a `bulk_reset:<mapID>` flag in the application store before their transaction and clear it afterwards, so N address writes do not trigger N recalculations. One explicit recalculation runs at the end instead.
- **Address status log.** Another after-update hook on `addresses` writes a row to the address audit log with the old and new status and the acting user. It deliberately also logs a not-home *tries* increment that leaves the status string unchanged, because the aggregate bucket moves even though the status did not.
- **Notes timestamps.** A pre-save hook on `addresses` stamps a last-notes-updated timestamp and actor whenever `notes` changes. This is what the hourly notes digest reads.
- **Expiry-cache invalidation.** An after-update hook on `congregations` clears the cached `expiry_hours` value used to mint publisher links. It is bound to the *after-success* hook rather than the update request precisely so that edits made by a superuser in the admin panel invalidate the cache too. Without it, changing link expiry had no effect until the process restarted.
- **Assignment audit log.** Create and delete hooks on `assignments` log `assigned` and `unassigned` actions; the cleanup job logs `expired`. All of them run only after the operation succeeded, so a rejected request leaves no audit row.
- **Role audit log.** Create, update and delete hooks on `roles` log `granted`, `changed` and `revoked`, capturing the previous role before the change. A no-op change writes nothing. Superuser actions are recorded with an empty actor, because a superuser has no `users` record to reference.
- **Role deletion resets the grace period.** After a role is deleted, the user's remaining roles are counted; if none are left, the unprovisioned clock is started fresh and any previous warning stamps are cleared — so a long-standing member who loses their last role gets the full grace period rather than being judged against their signup date.
- **Login bookkeeping.** A successful authentication updates `last_login` and clears both inactivity warning stamps.
- **User creation.** Email addresses are trimmed and lower-cased on create.

The three audit-log collections (addresses, assignments, roles) have all their API rules set to superuser-only, so they are readable through the admin panel and not through the public API.

## Next Steps

- Set up the [Frontend Application](frontend-setup.md)
- Read the [Self-Hosting Guide](self-hosting.md) for end-to-end deployment instructions
- Review the [Architecture Overview](architecture.md) and [Data Models](data-models.md)
