# Privacy Policy

Effective date: August 2026  
Last updated: August 2026

## Introduction

This privacy policy will help you understand what information we, as the creators of Ministry Mapper, collect, how Ministry Mapper uses it, and what choices you have.
We built the Ministry Mapper app as a free app. This SERVICE is provided at no cost and is intended for use as is.
If you choose to use this Service, then you agree to the collection and use of information in relation with this policy. The Personal Information that we collect is used for providing and improving the Service. We will not use or share your information with anyone except as described in this Privacy Policy.  
The terms used in this Privacy Policy have the same meanings as in our [Terms of Service](terms-of-service.md), which is accessible via this documentation, unless otherwise defined in this Privacy Policy.

## Information Collection and Use

In order to use our Service, we may require you to provide us with your name and email address. The information that we request will be retained by us and used as described in this privacy policy. The app does use third party services that may collect information used to identify you.

The information held by the Service falls into the following categories:

- Account information: your name, your email address, your password stored only as a bcrypt hash, whether your email address has been verified, whether the account has been disabled, and the date and time of your last sign-in.
- Congregation roles: which congregation or congregations you belong to and the role you hold in each.
- Territory and address data: the territories, maps and addresses your congregation records, including household status, the number of not-home attempts, free-text notes about a property, and geographic coordinates. Coordinates may be captured from your device's location when you choose to save a property's position on the map.
- Audit logs: three separate logs record changes to address status, the granting, revoking and expiry of map assignments, and the granting, changing and revoking of congregation roles. Each entry records which account made the change.
- Analytics views: five read-only database views aggregate territory and map progress, daily status changes, not-home follow-up state, and user activity and inactivity, including the number of days since an account last signed in. These views are restricted to the system administrator.
- Server logs: requests to the Service are logged, including IP addresses and authentication attempts.

Notes and coordinates recorded in the Service describe properties, not the people living in them. As set out in our Terms of Service, we ask that you do not record personal or sensitive details about householders.

## Browser Storage

The Service does not rely on cookies to keep you signed in. Instead, it stores information in your own browser, on your own device:

- Local storage holds your sign-in session, in the form of the authentication token kept by our PocketBase client, together with your preferences such as theme and colour theme, language, map list sort order and last scroll position, and a cached copy of the map behind an assignment link.
- IndexedDB holds a cached copy of the addresses in a map and the queue of edits you make while offline, so that your changes can be sent to the server once you are back online. Where a browser blocks IndexedDB, for example Safari Private Browsing, the app writes directly to the server instead and the offline queue is unavailable.

This data stays on your device and is not readable by other websites. You can clear it through your browser settings, but doing so will sign you out, discard any edits still waiting to be sent, and reset your preferences.

## Location Information

Some of our services utilize location information transmitted from users' devices. We use this information specifically for territory locating purposes within the scope of the designated service: to show nearby maps, to calculate a walking or driving route to a map, and, when you choose to save a property's position, to record that position against the address. Your device asks for your permission before any of this happens, and the features that need location simply remain unavailable if you decline.

## Device Information

We may collect certain information from your device. This information is used for troubleshooting, debugging, and performance monitoring purposes to improve our service and ensure a smooth user experience. Error reports contain technical details such as the browser, the page or request involved, and the error itself; when the error happens while you are signed in, the report also carries your account identifier so that we can trace what went wrong.

## Third-Party Services

We use the following third-party services to run Ministry Mapper. Each one is listed with what it does and what it receives.

- OpenAI: generates the written summaries included in our email reports and digests. When this feature is switched on, the text of publisher feedback messages and of the notes recorded against properties is sent to OpenAI to be summarised. The feature requires both an OpenAI API key and a feature flag to be enabled, and it stays off by default when the feature flag service is not configured. While it is off, no message or note text is sent to OpenAI.
- MailerSend: delivers our transactional email, including activity digests, the monthly report and reports generated on demand. It receives recipient email addresses and the contents of those messages.
- Sentry: collects error reports from the app and from our servers so that faults can be diagnosed. Errors whose origin is a browser extension are discarded rather than reported, and we do not use session replay, so no recording of your screen, typing or clicks is ever captured.
- LaunchDarkly: decides which features are switched on. It receives an identifier for the application and, once you are signed in, an identifier for your account. Your email address is configured as a private attribute and is not retained by LaunchDarkly. Before you sign in, everyone shares a single anonymous context.
- Umami: measures usage of the app, such as page views and a small number of named events like changing the language or the theme. Analytics is only active where it has been configured.
- Google: provides optional sign-in with a Google account using OAuth2. If you choose it, Google tells us the name and email address on that account; we never receive your Google password. Signing in with an email address and password remains available instead.
- Geoapify: supplies our map tiles, address search and route calculation. Requests sent to Geoapify include the map area you are viewing, the address text you type when searching, and the start and end points of a route, which may be your current location.
- Coolify and Cloudflare: host the Service and deliver it through a content delivery network, so all traffic to the Service passes through them.

We want to inform users of this Service that these third parties may have access to the information described above. The reason is to perform tasks assigned to them on our behalf. However, they are obligated not to disclose or use the information for any other purpose. Each of them publishes its own privacy policy, and we encourage you to read the policies of the providers that matter to you.

## Data Retention

Your account and the data associated with it are kept for as long as the account is in use. Two automated processes act on accounts that are not:

- Accounts with no congregation role. If an account holds no role in any congregation, we email a warning on day 3 and a final warning on day 6, disable the account on day 7, and permanently delete it on day 37. The 30 days between disabling and deletion exist so that an account disabled in error can be investigated and restored.
- Accounts that have not signed in. Counting from the last sign-in, we email a warning at 91 days and a final warning at 152 days, and disable the account at 183 days. These accounts are never deleted automatically. Signing in successfully at any point clears the pending inactivity warnings.

Territory, address and audit-log data belongs to the congregation and is kept until an administrator deletes it. Copies of deleted content may remain in backups for a period after deletion. Server request logs are retained for a limited period and then discarded.

## Security

We value your trust in providing us your Personal Information, thus we are striving to use commercially acceptable means of protecting it. But remember that no method of transmission over the internet, or method of electronic storage is 100% secure and reliable, and we cannot guarantee its absolute security.

## Changes to This Privacy Policy

We may update our Privacy Policy from time to time. Thus, you are advised to review this page periodically for any changes. We will notify you of any changes by posting the new Privacy Policy on this page and updating the date at the top of it. These changes are effective immediately, after they are posted on this page.

## Contact Us

If you have any questions or suggestions about our Privacy Policy, do not hesitate to contact us.  
Contact Information:  
Email: rimorin@gmail.com
