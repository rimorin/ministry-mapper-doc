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

Currently supported languages:

- English
- Japanese (日本語)
- Korean (한국語)
- Chinese (中文)
- Indonesian (Bahasa Indonesia)
- Malay (Bahasa Melayu)
- Tamil (தமிழ்)

The interface automatically detects your browser's language. You can also manually switch languages in the app.

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

### Can Ministry Mapper work offline?

No, an internet connection is required for the app to function properly. This is because:

- It needs to connect to the PocketBase backend
- Real-time updates between users require connectivity

The app does work well on mobile data if WiFi isn't available.

## Privacy and Legal

### What privacy laws should I consider?

**Important**: Ministry Mapper tracks residential addresses, which may be subject to data privacy laws. These laws vary significantly between countries and regions:

- **Europe**: GDPR (General Data Protection Regulation)
- **California**: CCPA (California Consumer Privacy Act)
- **Brazil**: LGPD (Lei Geral de Proteção de Dados)
- **Many Others**: Various local regulations

**Please thoroughly review your local regulations and ensure compliance before using Ministry Mapper.** Consult a legal professional for proper legal advice specific to your area.

**Hosted Service**: The hosted service at [ministry-mapper.com](https://ministry-mapper.com) may provide compliance assistance, but you remain responsible for following your local laws.

**Self-Hosting**: You are fully responsible for all privacy law compliance.

### What data does Ministry Mapper store?

**User Data (managed by PocketBase):**

- Email address
- Name
- Password (encrypted)
- User roles and permissions

**Territory Data:**

- Territory boundaries and names
- Addresses
- Status information
- Notes about households
- Assignment history
- Update timestamps

**Technical Data (if Sentry is enabled):**

- Error logs
- Performance metrics

All data storage complies with security best practices. For hosted service data policies, check [ministry-mapper.com](https://ministry-mapper.com).

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

- Node.js >= 22.0.0
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

```
VITE_SYSTEM_ENVIRONMENT=local
VITE_VERSION=$npm_package_version
VITE_GOOGLE_MAPS_API_KEY=your_google_maps_key
VITE_POCKETBASE_URL=your_pocketbase_backend_url
VITE_SENTRY_DSN=your_sentry_dsn (optional)
VITE_PRIVACY_URL=your_privacy_policy_url
VITE_TERMS_URL=your_terms_url
VITE_ABOUT_URL=your_about_page_url
```

**For production:** Same variables as above, but with `VITE_SYSTEM_ENVIRONMENT=production`

See the [Frontend Setup Guide](frontend-setup.md) for complete details.

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
   - `VITE_SYSTEM_ENVIRONMENT`: Set to "production" for production (affects tracing sample rate)
   - `VITE_VERSION`: Used for release tracking in Sentry

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

Tests are written using Vitest and Testing Library. Test files are located alongside the source files with `.test.ts` extension.

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

- **Frontend Framework**: React 19 with TypeScript
- **Build Tool**: Vite
- **UI Library**: React Bootstrap (Bootstrap 5)
- **Maps**: Leaflet with OpenStreetMap
- **State Management**: React Context + custom hooks
- **Routing**: Wouter
- **Styling**: SCSS
- **Backend**: PocketBase (separate repository)
- **Monitoring**: Sentry
- **Testing**: Vitest + Testing Library

See CLAUDE.md for detailed architecture information.

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

See CLAUDE.md for architecture details and coding conventions.

### Can I translate the app to a new language?

Yes! The app uses internationalization (i18n) for translations:

1. Check `src/i18n/` directory in the [frontend repository](https://github.com/rimorin/ministry-mapper-v2) for existing translations
2. Create a new language file following the existing structure
3. Add your translations
4. Update the language selector component
5. Submit a pull request

### How do I upgrade to a new version?

**For Hosted Service Users**: Updates are automatic. You don't need to do anything!

**For Self-Hosting Users**:

**Frontend:**

```bash
git pull origin main
npm install
npm run build
# Deploy new build to your hosting service
```

**Backend:**
Refer to the [Ministry Mapper BE repository](https://github.com/rimorin/ministry-mapper-be) for backend upgrade instructions.

**Important:**

- Always backup your data first
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

- Knowledge of React 19, TypeScript, and PocketBase
- Understanding of the codebase (see CLAUDE.md in the repository)
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
