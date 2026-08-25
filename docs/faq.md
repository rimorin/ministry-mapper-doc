# Frequently Asked Questions (FAQ)

## General Questions

### What is Ministry Mapper?

Ministry Mapper is a modern web application that helps congregations manage field service territories digitally. Instead of using paper territory slips, Ministry Mapper lets you organize and track everything digitally from any device with an internet connection. It uses React for the frontend, PocketBase as the backend, and Leaflet for interactive mapping.

### How do I access Ministry Mapper?

**Recommended**: Use our hosted service at **[ministry-mapper.com](https://ministry-mapper.com)**

Simply:
1. Visit [ministry-mapper.com](https://ministry-mapper.com)
2. Create an account using the sign-up page
3. Verify your email address
4. Contact your congregation's administrator for proper permissions

**Alternative**: Self-hosting (not recommended) - see the [Self-Hosting Guide](self-hosting.md) for details. Self-hosting requires significant technical expertise and ongoing maintenance.

### Is Ministry Mapper free to use?

**Hosted Service**: Visit [ministry-mapper.com](https://ministry-mapper.com) for pricing and plans.

**Self-Hosting**: The source code is free and open-source, but you'll need to provide your own:
- Hosting for the frontend (e.g., Vercel, Netlify, AWS)
- PocketBase backend deployment
- Optional: Sentry account for error monitoring

Note: Self-hosting costs (server, API fees, maintenance time) often exceed hosted service fees.

### What languages does Ministry Mapper support?

**For All Users**: Ministry Mapper is available in 8 languages:

- English
- Español (Spanish)
- 中文 (Chinese)
- தமிழ் (Tamil)
- Bahasa Indonesia (Indonesian)
- B. Melayu (Malay)
- 日本語 (Japanese)
- 한국어 (Korean)

On your first visit the app follows your browser's language if it matches one of these. You can change it at any time with the Language button in the admin sidebar footer, on the sign-in screen, or in the bottom bar of a publisher map. There is no need to change any browser setting, and your choice is remembered on that device.

### Do I need technical knowledge to use Ministry Mapper?

**As a Publisher/User**: No technical knowledge needed! If you can use a smartphone or computer, you can use Ministry Mapper through the hosted service at [ministry-mapper.com](https://ministry-mapper.com).

**As a Self-Hosting Administrator**: Yes, significant technical knowledge is required:

- Experience with Linux server administration
- Understanding of Docker and containerization
- Knowledge of environment variables and configuration
- Familiarity with SSL/TLS certificates
- Web server configuration (Nginx/Apache)
- DNS and domain configuration

**We strongly recommend using the hosted service instead of self-hosting.** See the [Self-Hosting Guide](self-hosting.md) for complete details if you still want to self-host.

### What can each user role do?

**For All Users**: There are three congregation roles, each granted to a user account by an administrator. One person can hold a different role in each congregation they belong to.

- **Read-Only**: View territories, maps, progress, messages, and directions, and switch between list and map views. Nothing can be changed.
- **Conductor**: Everything Read-Only can do, plus assign maps to publishers, share quick links, create and update addresses, and reset or delete territories.
- **Administrator**: Everything a Conductor can do, plus create territories and maps, move a map to another territory, add or remove floors and units, manage users and their roles, generate reports, and change congregation settings.

"Publisher" is not one of these roles. A publisher holds an assignment link instead of an account, which is a separate way in described in the next answer.

### How do assignment links work?

**For All Users**: An assignment link is a web address that opens exactly one map. It carries its own credential, so a publisher needs no account and never signs in.

- A conductor or administrator creates the link, optionally naming the publisher it is for, and shares it.
- Opening the link shows that one map only. Publishers cannot browse other maps or territories from it.
- The link expires after a period set by the congregation. Once it expires, opening it shows "This link has expired" and the map's cached copy is discarded.
- An administrator can delete a link at any time, and can copy an existing link again from the assignments list.

**Important**: An assignment link bypasses login, so anyone holding it has access to that map. Never post one to a public status or story, such as WhatsApp Status. If a link is shared publicly by mistake, remove the post immediately and tell a conductor or administrator so the link can be deleted and reissued. Administrators can shorten the link expiry to reduce the risk window.

### How do real-time updates work?

**For All Users**: When someone records a call, everyone else looking at the same map sees it within moments, with no refresh needed.

Under the hood the app keeps a Server-Sent Events (SSE) stream open to the backend and receives updates on it. There are no WebSockets involved, and updates only flow for the maps you are authorized to see. If the stream drops, the app reconnects on its own and re-reads the map to catch anything missed while it was disconnected.

**For Self-Hosting Only**: If you put a reverse proxy in front of the backend, disable response buffering for it (`proxy_buffering off;` in Nginx). Buffered responses hold the event stream back and real-time updates appear to stop working.

### Can Ministry Mapper work offline?

**For All Users**: Partly. Ministry Mapper is an online app, but publishers get real tolerance for patchy coverage:

- **Reading works from a cache**: a map you have already opened is kept locally, so it still loads when the network is down, with any of your own pending edits applied on top.
- **Publisher address updates are queued**: an update saved while offline is stored on the device and sent automatically when the connection returns. A counter shows how many updates are still in flight, and each unsynced address is marked with a dot.
- **Administrator actions need a live connection**: creating territories or maps, assigning links, managing users, changing settings, and generating reports all require connectivity.
- **The queue needs browser storage**: if IndexedDB is unavailable, for example in Safari Private Browsing, the app detects that and writes straight to the server instead. Everything still works, but there is no offline queue in that mode.

Real-time updates between users always require connectivity, and the app works well on mobile data if WiFi isn't available.

### Can I change the app's colours or theme?

**For All Users**: Yes. Open Theme Settings from the palette icon, which is in the admin sidebar footer, in the sign-in screen footer, and in the bottom bar of a publisher map.

- **Appearance**: Light, Dark, or System (follows your device setting).
- **Colour**: Classic, Tangerine, Perpetuity, Cosmic Night, or Mocha Mousse.

Changes apply instantly, there is no Save button, and the choice is remembered on that device. Status symbols keep the same colours in every theme on purpose, so a done or do-not-call marking always reads the same way.

### What is the "X to go" button on a map?

**For All Users**: It is a shortcut for finishing a map. Tapping it jumps to the next address that still needs a call, highlights it, and wraps back to the start after the last one. The highlight clears once that address is done.

It appears only once the map is at least 90 percent complete, and disappears at 100 percent. Earlier in a map there are too many addresses left for jumping between them to help. On multi-floor maps a badge next to each floor number shows how many addresses on that floor still need a call, under the same 90 percent rule; single-floor maps never show those badges.

### How do I save a house's location?

**For Publishers and Conductors**: On landed-house (single-story) maps, the address form has a location field with two options:

- **Use my location**: captures your device's current position in one tap, which is the quickest way to pin a house while standing at it.
- **On map**: opens a map so you can search for a place or tap to position the pin manually.

Addresses with no pin show "No pin set" and are hidden from Map View entirely, so a note offers to jump straight to the first address that still needs a pin. Pinning works offline; the position is saved locally and synced when the connection returns.

### What do the markers in Map View mean?

**For All Users**: Each marker is a small dial showing a map's completion percentage in the centre, with:

- **A blue ring**: how much of the map is done. The remaining part of the ring is grey.
- **A green dot at the top**: the map is currently assigned to a publisher.
- **An orange dot at the bottom**: the map has a personal link out.
- **No dots**: no active links for that map.
- **A grey outline**: the marker you have selected. Selection is grey, not orange, because orange now means a personal link and nothing else.

A Marker Guide in the top-right corner of the map repeats this key.

### Can I install Ministry Mapper as an app?

**For All Users**: Yes. Ministry Mapper is a Progressive Web App, so it can be installed to your home screen or desktop from the browser: on iOS Safari use Share then "Add to Home Screen", and on Android Chrome use the menu then "Install app".

An installed copy checks for new versions in several ways and shows an "Update Available" prompt with a Reload button when one is found. Using Ministry Mapper in the browser is still the recommended primary experience, since the browser always loads the current version.

## Privacy and Legal

### What privacy laws should I consider?

**Important**: Ministry Mapper tracks residential addresses, which may be subject to data privacy laws. These laws vary significantly between countries and regions:

- **Europe**: GDPR (General Data Protection Regulation)
- **California**: CCPA (California Consumer Privacy Act)
- **Many Others**: Most countries now have comparable data protection legislation, and congregations should check what applies locally

**Please thoroughly review your local regulations and ensure compliance before using Ministry Mapper.** Consult a legal professional for proper legal advice specific to your area.

**Hosted Service**: The hosted service at [ministry-mapper.com](https://ministry-mapper.com) may provide compliance assistance, but you remain responsible for following your local laws.

**Self-Hosting**: You are fully responsible for all privacy law compliance.

### What data does Ministry Mapper store?

**User Data (managed by PocketBase):**

- Email address
- Name
- Password (hashed, never stored in readable form)
- User roles and permissions, one per congregation
- Last login time, which is also used to identify dormant accounts

**Territory Data:**

- Territory and map names, codes, and descriptions
- Addresses, including unit or house numbers and household types
- Geographic coordinates for territories, maps, and individual addresses, recorded when someone pins a location or captures it from their device
- Status information for each address (done, not done, not home, do not call, invalid) and the number of not-home attempts
- Notes about households
- Messages between publishers, conductors, and administrators
- Assignment history, including who a link was issued to and when it expires
- Update timestamps and the name of the person who made each update

**Audit Logs (administrator-only, not visible in the app):**

- Address change history: every status change, with who made it and when
- Assignment history: links granted, revoked, and expired
- Role history: roles granted, changed, and revoked, with who made the change

**Reporting Data:**

- Read-only analytics views over the data above, used to build the monthly congregation report and its per-territory statistics

**Technical Data (if Sentry is enabled):**

- Error logs, including IP address
- Performance metrics

**Data Sent to Third Parties:**

- If AI report summaries are enabled, the text of publisher messages and property notes is sent to OpenAI to generate the summary. This feature is off unless a self-hosting administrator turns it on.
- Transactional email (digests and reports) is delivered through MailerSend.
- Map tiles, address search, and directions are served by Geoapify.

All data storage complies with security best practices. For hosted service data policies, see the [Privacy Policy](privacy-policy.md) and check [ministry-mapper.com](https://ministry-mapper.com).

## Technical Setup (Self-Hosting)

!!! warning "Self-Hosting Not Recommended"
    The following sections are for advanced users who want to self-host Ministry Mapper. **We strongly recommend using the hosted service at [ministry-mapper.com](https://ministry-mapper.com) instead.** Self-hosting requires significant technical expertise, ongoing maintenance, and security responsibilities.
    
    For complete self-hosting instructions, see the [Self-Hosting Guide](self-hosting.md).

### What are the system requirements?

**For Using the Hosted Service:**
- Modern web browser (Chrome, Firefox, Safari, Edge)
- Internet connection
- Email address
- Mobile device or computer

**For Self-Hosting Development:**

- Node.js >= 24.0.0
- npm package manager
- Git
- Docker (for backend)

**For Self-Hosting Production:**

- Cloud hosting service (Railway, Render, DigitalOcean, AWS, etc.)
- Domain name
- SSL/TLS certificate
- PocketBase backend (separate deployment)
- (Optional) Sentry account for monitoring
- (Optional) Email service for notifications

### What environment variables are required?

**For Self-Hosting Only** - This section applies only if you're self-hosting. The hosted service handles all configuration automatically.

**Frontend (.env file):**

```bash
# Required
VITE_POCKETBASE_URL=your_pocketbase_backend_url
VITE_SYSTEM_ENVIRONMENT=local
VITE_GEOAPIFY_API_KEY=your_geoapify_key
VITE_PRIVACY_URL=your_privacy_policy_url
VITE_TERMS_URL=your_terms_url

# Optional
VITE_ABOUT_URL=your_about_page_url
VITE_SENTRY_DSN=your_sentry_dsn
VITE_LAUNCHDARKLY_CLIENT_ID=your_launchdarkly_client_id
VITE_MAINTENANCE_MODE=false
VITE_UMAMI_SRC_URL=your_umami_script_url
VITE_UMAMI_WEBSITE_ID=your_umami_website_id
VITE_UMAMI_DOMAINS=your_allowed_domains
```

`VITE_GEOAPIFY_API_KEY` covers map tiles, address search, and directions - all three come from Geoapify. Do not set an app version variable yourself: the build injects it from `package.json`.

**For production:** Same variables as above, but with `VITE_SYSTEM_ENVIRONMENT=production`

**Backend (.env file):**

```bash
# Set once, on the first run against a new database
PB_ADMIN_EMAIL=your_superuser_email
PB_ADMIN_PASSWORD=your_superuser_password
PB_APP_NAME=Ministry Mapper
PB_SMTP_HOST=your_smtp_host
PB_SMTP_USERNAME=your_smtp_username
PB_SMTP_PASSWORD=your_smtp_password
GOOGLE_CLIENT_ID=your_google_oauth_client_id
GOOGLE_CLIENT_SECRET=your_google_oauth_client_secret

# Read on every start
PB_APP_URL=https://your-frontend-domain
PB_ALLOW_ORIGINS=https://your-frontend-domain
MAILERSEND_API_KEY=your_mailersend_key
MAILERSEND_FROM_EMAIL=noreply@your-domain
SENTRY_DSN=your_sentry_dsn
LAUNCHDARKLY_SDK_KEY=your_launchdarkly_server_key
OPENAI_API_KEY=your_openai_key
```

Three things are worth knowing about the backend variables:

- **Nothing is strictly required.** Every variable has a fallback or simply switches its feature off. In practice MailerSend is the one to set, because without it none of the email notifications can be delivered.
- **The first group is read only by database migrations**, so it takes effect only on the first run against a new database. Changing those values later has no effect; adjust them in the PocketBase admin UI instead.
- **The shipped superuser defaults must be changed.** Set `PB_ADMIN_EMAIL` and `PB_ADMIN_PASSWORD` before the first start, and note that the Google sign-in variables are not included in the sample env file even though both are needed for it to work.

See the [Frontend Setup Guide](frontend-setup.md) and [Backend Setup Guide](backend-setup.md) for complete details.

### How do I deploy the frontend?

**For Self-Hosting Only** - Skip this if you're using the hosted service.

1. **Build the application:**

   ```bash
   npm run build
   ```

2. **Configure environment variables** as specified above

3. **Deploy the `build/` folder** to your hosting provider:

   - Vercel: Connect your GitHub repo and configure environment variables
   - Netlify: Drag and drop the build folder or connect via Git
   - AWS S3: Upload build folder and configure static website hosting

4. **Ensure the PocketBase backend is deployed** and accessible at the URL specified in `VITE_POCKETBASE_URL`

See the [Frontend Setup Guide](frontend-setup.md) and [Self-Hosting Guide](self-hosting.md) for detailed instructions.

### How do I set up the PocketBase backend?

**For Self-Hosting Only** - Skip this if you're using the hosted service.

The backend is managed in a separate repository: [ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)

**Quick Overview:**

1. Clone the backend repository
2. Configure environment variables (see [Backend Setup Guide](backend-setup.md))
3. Deploy using Docker or Railway/Render
4. Initialize the database with the schema
5. Configure SMTP for email notifications (optional)

**Detailed instructions:** See the [Backend Setup Guide](backend-setup.md) and [Self-Hosting Guide](self-hosting.md).

**Important**: The backend must be deployed and accessible before deploying the frontend.

### How do I set up Sentry monitoring?

**For Self-Hosting Only** - Skip this if you're using the hosted service.

1. Create a [Sentry](https://sentry.io/) account
2. Create a React project
3. Go to settings and retrieve the DSN key
4. Configure the following environment variables:
   - `VITE_SENTRY_DSN`: Your Sentry project DSN
   - `VITE_SYSTEM_ENVIRONMENT`: Set to "production" for production (gates whether errors are captured)

Release tracking uses the app version injected from `package.json` at build time, so there is nothing to configure for it. To upload source maps from your own builds, set the build-time variables `SENTRY_AUTH_TOKEN`, `SENTRY_ORG`, and `SENTRY_PROJECT`; these are not prefixed with `VITE_` and are never shipped to the browser.

Note: Sentry is optional but recommended for production deployments.

## Development

### How do I run the app locally?

**For Developers Only** - This section is for developers contributing to Ministry Mapper.

1. **Clone the repository:**

   ```bash
   git clone https://github.com/rimorin/ministry-mapper-v2.git
   cd ministry-mapper-v2
   ```

2. **Install dependencies:**

   ```bash
   npm install
   ```

3. **Configure .env file** with required environment variables (see `.env.example`)

4. **Start development server:**
   ```bash
   npm start
   ```

The app will run on `http://localhost:3000`

Note: You need a running PocketBase backend for the app to function. See the [Backend Setup Guide](backend-setup.md).

### How do I run tests?

```bash
npm test
```

Tests are written using Vitest and Testing Library. Test files are located alongside the source files they cover, with a `.test.ts` or `.test.tsx` extension - there is no separate test directory.

### How do I format code?

**Check formatting:**

```bash
npm run prettier
```

**Apply formatting fixes:**

```bash
npm run prettier:fix
```

The project uses Prettier for code formatting and has pre-commit hooks configured via Husky.

### What's the tech stack?

- **Frontend Framework**: React 19 with TypeScript, built with the React Compiler
- **Build Tool**: Vite 8 (Rolldown-based)
- **Styling**: Tailwind CSS 4, configured in CSS with no config file
- **UI Library**: shadcn/ui components built on Base UI
- **Maps**: Leaflet with OpenStreetMap tiles served by Geoapify
- **State Management**: React Context + custom hooks
- **Routing**: wouter
- **Backend**: PocketBase (separate repository)
- **Monitoring**: Sentry
- **Testing**: Vitest + Testing Library

Bootstrap and SCSS were removed in the 2.0.0 interface rebuild; anything still referring to them is out of date. See the [Architecture Overview](architecture.md) and [Frontend Setup Guide](frontend-setup.md) for detail, and the repository README for contributor conventions.

## Troubleshooting

### The app shows "Cannot connect to backend"

**For Hosted Service Users**: If you see this error, the service may be experiencing issues. Check the status page or contact support.

**For Self-Hosting Users**:

**Possible causes:**

1. PocketBase backend is not running or not accessible
2. `VITE_POCKETBASE_URL` environment variable is incorrect
3. CORS issues (backend not allowing frontend domain)

**Solutions:**

- Verify the backend is running and accessible
- Check the URL in your environment variables
- Configure CORS settings in PocketBase to allow your frontend domain

### Changes are not reflected after rebuilding

**For Self-Hosting Only**:

**Possible causes:**

1. Browser cache
2. CDN cache (if using one)
3. Build artifacts not properly deployed

**Solutions:**

- Clear browser cache or use incognito mode
- Invalidate CDN cache if applicable
- Verify the build folder was completely replaced on your hosting service
- Check browser console for errors

### User authentication not working

**For Hosted Service Users**: Contact support if you're having authentication issues.

**For Self-Hosting Users**:

**Possible causes:**

1. PocketBase backend user management not configured
2. Network connectivity issues
3. Incorrect PocketBase URL
4. CORS configuration issues

**Solutions:**

- Verify PocketBase backend is properly configured
- Check browser console for error messages
- Test backend API endpoints directly
- Check CORS settings in PocketBase

### The app is slow or unresponsive

**Possible causes:**

1. Large territory datasets
2. Network latency
3. Insufficient server resources (self-hosting)
4. Browser performance issues

**Solutions:**

- Check your internet connection
- Try using a different browser
- For self-hosting: Optimize territory data (reduce unnecessary fields)
- For self-hosting: Use a closer hosting region
- For self-hosting: Upgrade server resources if needed
- Clear browser cache and data
- Check browser console for performance warnings

## Contributing

### How do I report a bug?

1. Check if the bug is already reported in [GitHub Issues](https://github.com/rimorin/ministry-mapper-v2/issues)
2. If not, create a new issue with:
   - Clear description of the problem
   - Steps to reproduce
   - Expected vs actual behavior
   - Browser and device information
   - Screenshots or error messages
   - Your environment configuration (without sensitive data)

### Can I request features?

Yes! Create a feature request on GitHub with:

- Clear description of the feature
- Use case and benefits
- How it would work
- Who would benefit

Note: This is a volunteer-maintained project, so implementation depends on available time and resources.

### Can I contribute code?

Absolutely! Contributions are welcome:

**How to contribute:**

1. Fork the repository
2. Create a feature branch
3. Make your changes following the existing code style
4. Write tests for new functionality
5. Run `npm test` and `npm run prettier` to ensure quality
6. Submit a pull request with a clear description

**Contribution guidelines:**

- Follow TypeScript and React best practices
- Use existing patterns in the codebase
- Update documentation if needed
- Keep changes focused and minimal
- Test thoroughly before submitting

See the [Architecture Overview](architecture.md) for how the system fits together, and the repository README for coding conventions.

### Can I translate the app to a new language?

Yes! The app uses internationalization (i18n) for translations:

1. Check `src/i18n/locales/` in the [frontend repository](https://github.com/rimorin/ministry-mapper-v2) for the existing translations
2. Create a folder for your language code with a `translation.json` following the existing structure
3. Add your translations, keeping exactly the same keys as the English file - every language carries an identical key set
4. Add the language code and its label to the language options used by the language selector
5. Submit a pull request

### How do I upgrade to a new version?

**For Hosted Service Users**: Updates are automatic. You don't need to do anything!

**For Self-Hosting Users**:

**Frontend:**

```bash
git pull origin master
npm install
npm run build
# Deploy new build to your hosting service
```

**Backend:**
Refer to the [Ministry Mapper BE repository](https://github.com/rimorin/ministry-mapper-be) for backend upgrade instructions.

**Important:**

- Always back up your data first - PocketBase has a built-in backup feature under Settings in its admin UI, and `pb_data` holds all of your data
- Read CHANGELOG.md for breaking changes
- Test in a staging environment if possible
- Update environment variables if needed

## Support

### Where can I get help?

**For Hosted Service Users**: Contact support through the [ministry-mapper.com](https://ministry-mapper.com) website.

**For All Users**:

1. **Documentation**: Check this documentation site, including [Getting Started](getting-started.md), [User Guide](user-guide.md), and other guides
2. **GitHub Issues**: Search for existing issues or create a new one at the [ministry-mapper-v2 repository](https://github.com/rimorin/ministry-mapper-v2/issues)
3. **GitHub Discussions**: Ask questions and share ideas
4. **Code Review**: Read the code and comments for implementation details (developers)

### Is there commercial support?

**Hosted Service**: Professional support is available through [ministry-mapper.com](https://ministry-mapper.com).

**Self-Hosting**: No official commercial support is available for self-hosted instances. This is a volunteer-maintained open-source project. However:

- Community support via GitHub
- Comprehensive documentation
- You may hire independent developers for customization

### How often is the app updated?

**Hosted Service**: Updates are applied automatically as they become available.

**Self-Hosting**: Updates depend on volunteer availability. Check:

- **CHANGELOG.md** for version history
- **GitHub Releases** for release notes
- **Commit history** for recent changes

### Can I customize Ministry Mapper for my needs?

Yes! It's open source, so you can:

- Modify the UI (colors, layout, branding)
- Add custom features
- Change workflows
- Integrate with other systems

**Requirements:**

- Knowledge of React 19, TypeScript, Tailwind CSS, and PocketBase
- Understanding of the codebase (see the [Architecture Overview](architecture.md) and the repository README)
- Proper testing of your changes
- Consider contributing improvements back to the project

**Note**: Customizations require self-hosting. The hosted service uses the standard version.

---

## Still Have Questions?

1. **Read the Documentation**: 
   - [Getting Started Guide](getting-started.md)
   - [User Guide](user-guide.md)
   - [Self-Hosting Guide](self-hosting.md) (if self-hosting)
   - [Backend Setup Guide](backend-setup.md) (if self-hosting)
   - [Frontend Setup Guide](frontend-setup.md) (if self-hosting)
2. **Search** [GitHub Issues](https://github.com/rimorin/ministry-mapper-v2/issues)
3. **Ask in** GitHub Discussions
4. **Contact Support** (for hosted service users)
5. **Open a new issue** with your question

Remember: Ministry Mapper is open-source and maintained by volunteers. Be patient and respectful when asking for help!
