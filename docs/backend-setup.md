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
- Real-time data synchronization
- Custom API endpoints for territory management
- Background jobs for automated tasks
- Email notifications via SMTP

**This guide assumes you have**:
- Experience with server administration
- Understanding of Docker and containerization
- Familiarity with environment variables
- Knowledge of security best practices

For complete self-hosting instructions, see the [Self-Hosting Guide](self-hosting.md).

## Quick Reference

This page provides a quick reference for backend configuration. **For complete setup instructions, see the [Self-Hosting Guide](self-hosting.md).**

### Technology Stack

- **Go**: Fast, efficient programming language
- **PocketBase**: Backend-as-a-service with built-in admin panel
- **SQLite**: Lightweight database in `pb_data` folder
- **Docker**: Alpine Linux-based container

### Key Features

- Data storage and management
- User authentication
- Real-time subscriptions (Server-Sent Events)
- Custom API endpoints
- Scheduled background jobs
- Email notifications (SMTP)
- Error tracking (Sentry)
- Feature flags (LaunchDarkly)

---

!!! note "Complete Setup Instructions"
    The sections below provide a technical reference for backend configuration. For step-by-step setup instructions with context and explanations, please see the **[Self-Hosting Guide](self-hosting.md)**.

---

## Environment Variables Explained

### Service Integration Keys (Optional)

```bash
# MailerSend API key - for sending emails via MailerSend service
# Leave empty if using SMTP instead
MAILERSEND_API_KEY=

# Email address for MailerSend sender
MAILERSEND_FROM_EMAIL=

# LaunchDarkly SDK key - for feature flags to control background jobs
# Jobs run by default if not configured
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=
```

### Application Settings

```bash
# Name shown in emails and admin panel
PB_APP_NAME=Ministry Mapper

# URL where your frontend is hosted
PB_APP_URL=http://localhost:3000
```

### Default Admin Account

```bash
# Initial admin account created during first setup
# Change password immediately after first login!
PB_ADMIN_EMAIL=testing_account@ministry-mapper.com
PB_ADMIN_PASSWORD=pb123456789
```

### Email Configuration (SMTP)

```bash
# SMTP server address (e.g., smtp.gmail.com, smtp.sendgrid.net)
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

### Security & Access Control

```bash
# Comma-separated list of allowed origins for CORS
# Use * for development, specific domains for production
# Example: https://app.example.com,https://app2.example.com
PB_ALLOW_ORIGINS=*

# Hide the admin UI from public access (true/false)
# Set to true in production for security
PB_HIDE_CONTROLS=false
```

### Authentication Settings

```bash
# Enable one-time password login (true/false)
PB_OTP_ENABLED=false

# Enable multi-factor authentication (true/false)
PB_MFA_ENABLED=false
```

### Rate Limiting

```bash
# Enable rate limiting to prevent API abuse (true/false)
PB_ENABLE_RATE_LIMITING=false
```

### Error Tracking (Not in .env.sample - add manually if needed)

```bash
# Sentry DSN for error monitoring and tracking
SENTRY_DSN=

# Environment name for Sentry (development, staging, production)
SENTRY_ENV=production

# Git commit hash - automatically set by hosting platforms like Coolify
SOURCE_COMMIT=
```

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
- `-v /path/to/pb_data:/app/pb_data` saves your database permanently (replace `/path/to/pb_data` with a real path on your system)
- `--env-file .env` loads your environment variables from the `.env` file
- `-d` runs the container in the background (detached mode)

### Option 2: Local Development

For development and testing on your computer:

#### 1. Install Go Dependencies

```bash
./scripts/install.sh
```

This script runs `go get ./...` and `go mod tidy` to install all dependencies.

#### 2. Set Up Environment Variables

```bash
cp .env.sample .env
# Edit .env with your settings
```

#### 3. Run the Application

```bash
./scripts/start.sh
```

This script exports environment variables from `.env` and runs `go run main.go serve`.

The backend will start on **http://localhost:8090** by default (PocketBase default port).

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

**Security Note**: Set `PB_HIDE_CONTROLS=true` in production to hide the admin UI from public access.

## Database Management

### Backing Up Your Data

Your data lives in the `pb_data` folder. Regular backups are crucial.

#### Manual Backup

```bash
# Create a backup of the entire pb_data folder
tar -czf ministry-mapper-backup-$(date +%Y%m%d).tar.gz /path/to/pb_data/
```

#### Automated Daily Backup

Set up a cron job to back up automatically:

```bash
# Add to crontab (run: crontab -e)
0 2 * * * tar -czf /backups/ministry-mapper-backup-$(date +\%Y\%m\%d).tar.gz /path/to/pb_data/
```

This runs at 2 AM daily and creates timestamped backups.

#### Restoring from Backup

```bash
# Stop the application first
# Then extract the backup
tar -xzf ministry-mapper-backup-20240101.tar.gz -C /path/to/
```

## Security Checklist

Before going to production:

- [ ] Change the default admin password immediately after first login
- [ ] Set `PB_ALLOW_ORIGINS` to your actual domain(s), not `*`
- [ ] Enable HTTPS (never use HTTP in production)
- [ ] Set `PB_HIDE_CONTROLS=true` to hide admin UI from unauthorized access
- [ ] Consider enabling rate limiting with `PB_ENABLE_RATE_LIMITING=true`
- [ ] Configure Sentry (`SENTRY_DSN` and `SENTRY_ENV`) for error monitoring
- [ ] Set up automated daily backups
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

**Note**: Your data in `pb_data` is preserved during updates.

## Database Migrations

The backend automatically runs database migrations when starting in development mode (when using `go run`). You'll see messages in the logs like:

```
Running migrations...
Migration 1759244334_collections_snapshot completed
```

**How it Works:**

- Migrations are stored in the `migrations/` folder
- PocketBase detects if you're running via `go run` and auto-migrates
- In production (compiled binary), you need to run migrations manually using: `./main migrate`

**Important:** Never delete or modify migration files manually. This can break your database.

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

PocketBase stores logs in the `pb_data/logs/` folder. These include:

- Request logs
- Error logs
- Custom application logs

### What to Look For in Logs

- **Startup Messages**: Confirms successful initialization (e.g., "Starting Ministry Mapper build...")
- **Migration Messages**: Database migrations being applied
- **Sentry Initialization**: Error tracking setup
- **Cron Jobs**: Background task execution (territory aggregates, message processing, etc.)
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

```

### Database Locked

**Problem**: "Database is locked" error

**Solution**:
- Stop all containers/processes accessing the database
- Check if a backup process is running
- Ensure only one instance of the app is running
- SQLite doesn't support concurrent writes from multiple processes
- Restart the application

### Email Not Sending

**Problem**: Users don't receive emails

**Solutions**:
1. **Verify SMTP Settings**: Check all `PB_SMTP_*` variables in `.env`
2. **Gmail Users**: Use an [App Password](https://support.google.com/accounts/answer/185833), not your regular password
3. **Check Spam Folder**: Emails might be filtered
4. **Test SMTP Connection**: Use a tool like `telnet` or an email client to verify SMTP credentials
5. **Review Logs**: Look for error messages in `pb_data/logs/` or Docker logs
6. **Port Issues**: Ensure port 587 (TLS) or 465 (SSL) is not blocked by firewall

### Out of Memory

**Problem**: Application crashes with memory errors

**Solutions**:
- Increase Docker memory limits: `docker run -m 1g ...`
- Check logs for memory leaks
- Optimize large database queries
- Upgrade your hosting plan's RAM allocation
- Consider using SQLite PRAGMA optimizations for large datasets

### Cannot Access Admin Panel

**Problem**: 404 error when accessing admin URL

**Solutions**:
- Ensure URL format: `http://your-url/_/` (with underscore and trailing slash)
- Check if `PB_HIDE_CONTROLS=true` (prevents admin UI access)
- Verify the application is running: `docker ps` or check process
- Clear browser cache and cookies
- Check logs for startup errors

## Performance Tips

### Small Congregations (< 100 users)
- Default settings work well
- 512MB RAM is sufficient
- SQLite handles this load easily
- No special configuration needed

### Medium Congregations (100-500 users)
- Recommended: 1GB RAM
- Enable rate limiting: `PB_ENABLE_RATE_LIMITING=true`
- Set up daily automated backups
- Monitor database size in `pb_data/data.db`

### Large Congregations (> 500 users)
- Recommended: 2GB+ RAM
- Enable rate limiting
- Consider dedicated hosting (not shared)
- Monitor logs for slow queries
- Set up backup retention policy (keep last 30 days)
- Consider LaunchDarkly feature flags to control background job frequency

## API Endpoints

The backend provides these custom authenticated API endpoints (all require user authentication):

### Map Management
- `POST /map/codes` - Get all map codes for a specific map
- `POST /map/code/add` - Add a new address code to a map
- `POST /map/codes/update` - Update the sequence/order of map codes
- `POST /map/code/delete` - Delete an address code from a map
- `POST /map/add` - Create a new map
- `POST /map/territory/update` - Update which territory a map belongs to
- `POST /map/reset` - Reset a map (clear all assignments and data)

### Floor Management
- `POST /map/floor/add` - Add a floor level to a map
- `POST /map/floor/remove` - Remove a floor from a map

### Territory Management
- `POST /territory/reset` - Reset a territory (clear assignments)
- `POST /territory/link` - Create a quick access link for a territory

### Configuration
- `POST /options/update` - Update congregation options/settings

**Authentication**: All endpoints require a valid PocketBase authentication token in the request header.

**PocketBase Built-in APIs**: In addition to custom endpoints, PocketBase provides automatic REST APIs for all collections (CRUD operations) at `/api/collections/{collection}/records`.

## Background Jobs (Cron Scheduler)

The backend runs automated background tasks using a cron scheduler. These jobs are controlled by LaunchDarkly feature flags:

### Scheduled Jobs

1. **Territory Aggregates** (`*/10 * * * *` - every 10 minutes)
   - Updates territory statistics and aggregated data
   - Feature flag: `enable-territory-aggregations`

2. **Assignment Cleanup** (`*/5 * * * *` - every 5 minutes)
   - Removes expired or invalid territory assignments
   - Feature flag: `enable-assignments-cleanup`

3. **Message Processing** (`*/30 * * * *` - every 30 minutes)
   - Processes pending messages in the queue
   - Feature flag: `enable-message-processing`

4. **Instruction Processing** (`*/30 * * * *` - every 30 minutes)
   - Handles pending territory assignment instructions
   - Feature flag: `enable-instruction-processing`

5. **Note Processing** (`0 * * * *` - hourly)
   - Processes updated notes for congregations
   - Feature flag: `enable-note-processing`

6. **Monthly Report** (`0 0 1 * *` - 1st of each month at midnight)
   - Generates Excel report of congregation data
   - Feature flag: `enable-monthly-report`

**Note**: If LaunchDarkly is not configured, all jobs run by default (enabled: true).

## Database Hooks

The application automatically handles certain events:

- **Address Updates**: When address notes change, timestamps and user info are recorded
- **Address Updates**: Triggers map aggregate recalculation
- **User Authentication**: Updates last login timestamp
- **User Creation**: Normalizes and lowercase email addresses

## Next Steps

- Set up the [Frontend Application](frontend-setup.md)
- Learn about [User Management](user-management.md)
- Configure [Security Settings](security.md)
```
