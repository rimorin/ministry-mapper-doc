# User Guide

## Introduction

This guide explains how to use Ministry Mapper for managing field service territories. Ministry Mapper is a web-based application that helps congregations organize territory assignments, track visits, and coordinate field service activities.

## Getting Started

### Creating Your Account

1. **Visit the Website**

   - Go to your congregation's Ministry Mapper URL
   - Click "Sign Up"

2. **Fill in Your Information**

   - Enter your name
   - Enter your email address (use a valid email you check regularly)
   - Create a strong password (at least 6 characters with numbers, capital letters)
   - Confirm your password
   - Review and agree to the privacy policy and terms of service

3. **Verify Your Email**

   - Check your email for a verification message
   - Click the verification link to activate your account
   - Your account is now verified

4. **First Login**
   - Return to the login page
   - Enter your email and password
   - If your organization uses One-Time Password (OTP), enter the code sent to your email
   - After logging in, wait for an administrator to grant you access to your congregation

### Understanding User Roles

Ministry Mapper has four access levels:

**Publisher**

- View territories assigned to you via links
- Update address status (done, not home, do not call)
- Add notes to addresses
- View territory maps
- Track visit attempts

**Read-Only**

- View all territories
- See address information
- Cannot make changes to territories or addresses

**Conductor**

- Everything Read-Only can do, plus:
- Create and manage territory assignments
- View all territories and their progress
- Access congregation messages
- Manage congregation options (status types)

**Administrator**

- Full access to everything
- Create, edit, and delete territories
- Manage addresses and buildings
- Assign user roles and permissions
- Configure congregation settings
- Manage users and invitations

## Main Features

### Main Interface

After logging in, the interface varies based on your role:

**Publishers** access territories through:

- Assignment links sent by administrators
- Links expire after a set time (default 24 hours)
- No dashboard - direct access to assigned territories

**Conductors and Administrators** see:

- **Territory Selector**: Dropdown to choose which territory to manage
- **Territory View**: Selected territory with addresses and map
- **Action Buttons**: Access to assignments, messages, and settings
- **Progress Tracking**: Visual progress bars showing territory completion

### Viewing Territories

#### For Publishers

1. Click the assignment link provided by your administrator
2. You'll see the territory map and address list
3. Territory information includes:
   - Territory code and description
   - Interactive Google Map with address markers
   - List of addresses/units to visit
   - Progress percentage

#### For Conductors and Administrators

1. Select a territory from the dropdown menu at the top
2. The territory displays with:
   - Territory code and description
   - Progress bar showing completion percentage
   - Map view with all addresses
   - Detailed address/unit list
   - Management options (edit, delete, reset)

### Working With Addresses

#### Viewing Address Information

Each address/unit shows:

- **Address/Unit Number**: Location identifier
- **Floor**: Building floor level (for multi-story properties)
- **Type**: Household type based on congregation options (e.g., Chinese, English, Tamil)
- **Status**: Current status (default: not done, done, not home, do not call, invalid)
- **Not Home Count**: Number of times nobody answered
- **Do Not Call Date**: When DNC was marked (if applicable)
- **Notes**: Important information about the householder
- **Last Updated**: Timestamp of last update
- **Updated By**: User who made the last update
- **Sequence**: Order for visiting addresses

#### Updating Address Status

1. **Find the Address**

   - Scroll through the address list in the territory view
   - Addresses are grouped by floor for multi-story buildings

2. **Click to Edit**

   - Click on an address/unit card
   - An edit modal will open

3. **Change Status**
   - Select the appropriate status:
     - **Not Done**: Not yet visited (default)
     - **Done**: Householder was contacted
     - **Not Home**: Nobody answered
     - **Do Not Call**: Householder requested no further visits
     - **Invalid**: Address doesn't exist or is inaccessible
4. **Update Type (if available)**

   - Select household type(s) based on congregation options
   - Multiple types can be selected if configured by administrator

5. **Add Notes**

   - Type relevant information:
     - Best times to visit
     - Language spoken
     - Special circumstances
     - Follow-up needed
   - Keep notes respectful and factual

6. **Save Changes**
   - Click "Save"
   - Changes save immediately to the database
   - Other users will see updates in real-time

#### Understanding Status Colors

Addresses are color-coded for quick identification:

- **Light background**: Not started or in progress
- **Highlighted**: Nearly complete territory (approaching 90% completion)
- **Grayed out**: Completed units or non-countable addresses

#### Using the Map View

1. **Access Map**

   - The Google Maps view is displayed at the top of the territory
   - Shows territory location with address markers

2. **Navigate**

   - Zoom in/out with +/- buttons or pinch gesture
   - Drag to move around the map
   - Click address markers to see details

3. **Get Directions**

   - Click the "Directions" button above the map
   - Opens Google Maps in a new tab with directions from your congregation location
   - Helps you plan your route

4. **Change Location (Administrators)**
   - Click the geolocation button to set a custom starting point for directions
   - Useful for territories far from the congregation

### Real-Time Data Synchronization

Ministry Mapper uses PocketBase realtime subscriptions:

- **Instant Updates**: Changes to addresses appear immediately for all users
- **Live Collaboration**: Multiple users can work on different territories simultaneously
- **Connection Status**: The app automatically reconnects if internet connection is lost
- **Background Sync**: Updates sync automatically when you're viewing a territory

**Note**: Real-time updates only work while you have the territory open. Closing the browser or navigating away will stop the subscription.

### Territory Assignment (For Administrators and Conductors)

Ministry Mapper doesn't have a "request territory" system for publishers. Instead, administrators and conductors create assignment links.

#### Creating Assignment Links

1. **Access Assignments**

   - Click the "Assignments" button in the top navigation
   - View all active assignment links

2. **Create New Assignment**

   - Click "Create New Assignment"
   - Select the territory from dropdown
   - Choose assignment type:
     - **Normal Assignment**: Regular territory assignment
     - **Personal Slip**: For personal territory work
   - Select publisher (optional - links can be shared with anyone)
   - Set expiry time (default: 24 hours)
   - Click "Create"

3. **Share the Link**
   - Copy the generated link
   - Send to publishers via email, WhatsApp, or other messaging
   - Publisher clicks the link to access the territory
   - Link expires automatically after the set time

#### Managing Assignments

- **View Active Links**: See all current assignment links and their expiry times
- **Delete Links**: Remove links before they expire if needed
- **Monitor Usage**: Check which territories are currently assigned

### Messages and Instructions (For Administrators and Conductors)

#### Viewing Messages

1. Click the "Messages" button in the top navigation
2. See congregation-wide messages and territory-specific instructions
3. Messages show:
   - Message content
   - Type (feedback or instruction)
   - Creator and creation date
   - Read status

#### Creating Instructions

1. Select a territory
2. Click "New Instruction"
3. Type your message for publishers working this territory
4. Instructions appear when publishers access the territory link

#### Managing Messages

- **Pin Messages**: Keep important messages at the top
- **Mark as Read**: Track which messages you've reviewed
- **Delete Messages**: Remove outdated messages

### User Management (For Administrators)

#### Inviting Users

1. Click on your profile icon
2. Select "User Management"
3. Click "Invite User"
4. Enter the user's email address
5. Select their congregation role:
   - Publisher
   - Read-Only
   - Conductor
   - Administrator
6. Send invitation
7. User receives email with link to create account

#### Managing Existing Users

1. Go to "User Management"
2. View list of all users in your congregation
3. For each user you can:
   - **Change Role**: Update access level
   - **Remove Access**: Set to "Delete Access" to revoke permissions
   - **View Details**: See email and verification status

**Important**: You need "Administrator" role to manage users. Conductors cannot change user permissions.

### Managing Territories (For Administrators)

#### Creating Territories

1. Select "Create New" from the territory dropdown
2. Enter territory code (e.g., M01, W12)
3. Enter territory description
4. Click "Create"
5. Start adding addresses to the new territory

#### Managing Territory Settings

- **Change Territory Name**: Update the description
- **Change Territory Code**: Modify the territory identifier
- **Reset Territory**: Clear all address statuses back to "not done"
- **Delete Territory**: Remove territory and all its addresses (cannot be undone)

### Managing Multiple Territories

Territory selection works via dropdown:

1. **Switch Between Territories**

   - Use the territory selector dropdown at the top
   - Select any territory to view and manage it

2. **Track Progress**
   - Progress bars show completion percentage
   - Based on countable addresses vs. completed addresses
   - Updates in real-time as addresses are marked

## Advanced Features

### Congregation Options (For Administrators)

Customize household types for your congregation:

1. Click "Congregation Options" in settings
2. Add, edit, or remove household types
3. Set which types are countable
4. Set default type for new addresses
5. Reorder types by dragging

Examples: Chinese, English, Tamil, Malay, etc.

### Congregation Settings (For Administrators)

Configure how territories work:

1. **Max Tries**: Set how many "not home" attempts before considering complete
2. **Origin Location**: Set default location for map directions
3. **Default Expiry Hours**: Set how long assignment links last before expiring
4. **Multiple Options**: Allow addresses to have multiple types

### Using Filters

The application doesn't have built-in filters, but addresses are organized by:

- **Status**: Visual color coding helps identify address states
- **Floor**: Multi-story buildings group units by floor
- **Sequence**: Addresses are ordered by sequence number for logical visiting order

### Address Management (For Administrators)

#### Adding Public Addresses (Multi-story buildings)

1. Click "New Public Address"
2. Enter postal code
3. Enter address name
4. Set number of floors
5. Enter starting floor number
6. Enter number of units per floor
7. Set unit numbering format
8. Click "Create"
9. Units are automatically generated for all floors

#### Adding Private Addresses (Single-story properties)

1. Click "New Private Address"
2. Enter postal code
3. Enter property name/address
4. Click "Create"
5. Manually add individual houses/units as needed

#### Editing Addresses

Administrators can:

- **Change Address Name**: Update the building/property name
- **Change Postal Code**: Update the postal code
- **Add/Delete Floors**: Modify floor structure for public addresses
- **Add/Delete Units**: Manage individual units
- **Change Unit Sequence**: Reorder units for visiting
- **Reset Address**: Clear all unit statuses
- **Delete Address**: Remove entire address with all units

### Quick Links (For Administrators)

Create temporary access links:

1. Click "Get Quick Link"
2. Link is generated for current territory
3. Share with anyone who needs temporary access
4. Link expires based on congregation settings
5. No login required for recipients

## Mobile Usage

### Using on Your Phone

Ministry Mapper is fully responsive and works on mobile devices:

**Access on Mobile:**

1. Open your browser (Safari, Chrome, etc.)
2. Navigate to your Ministry Mapper URL
3. Log in or click assignment link
4. The interface automatically adapts to your screen size

**Progressive Web App (PWA):**

- Ministry Mapper can be installed as a PWA
- Provides app-like experience
- Faster loading with cached resources
- Works on iOS and Android

**Installation Steps:**

1. Open Ministry Mapper in your mobile browser
2. For iOS Safari: Tap Share → "Add to Home Screen"
3. For Android Chrome: Tap menu (⋮) → "Install app" or "Add to Home Screen"
4. The app icon appears on your home screen

**Mobile Features:**

- Touch-friendly buttons and interface
- Google Maps integration for navigation
- Responsive layout adapts to screen size
- All desktop features available

### Offline Usage

Ministry Mapper requires internet connection:

**Internet Required:**

- PocketBase backend connection needed for all data operations
- Real-time synchronization requires active connection
- Google Maps requires internet for display
- Updates cannot be made offline

**Service Worker Caching:**

- Static assets (CSS, JS, fonts) are cached for faster loading
- External assets cached for 7 days
- Fonts cached for 30 days
- App shell loads faster on repeat visits

**Best Practice:**

- Ensure stable internet before starting fieldwork
- Check connection if updates aren't saving
- Consider downloading directions ahead of time

## Settings

### Personal Profile

Access your profile settings:

1. Click on your profile name/icon
2. Select "Profile"
3. Available options:
   - Change password
   - View account information
   - Access user management (Administrators only)

### Changing Your Password

1. Go to your profile
2. Enter your current password
3. Enter new password (minimum 6 characters, must include numbers and capital letters)
4. Confirm new password
5. Click "Change Password"

### Password Recovery

If you forgot your password:

1. Click "Forgotten your password?" on login page
2. Enter your email address
3. Click "Continue"
4. Check your email for password reset link
5. Click the link and create a new password

### Language Selection

Ministry Mapper supports multiple languages:

**Available Languages:**

- English (en)
- Japanese (ja / 日本語)
- Korean (ko / 한국어)
- Chinese (zh / 中文)
- Indonesian (id / Bahasa Indonesia)
- Malay (ms / Bahasa Melayu)
- Tamil (ta / தமிழ்)

**Changing Language:**

- Language is automatically detected from your browser settings
- To change: Update your browser's language preferences
- The app will automatically use the matching translation
- Default view (list/map)

## Tips for Effective Territory Work

### Organization

- **Start with a Plan**: Review the entire territory before going out
- **Work Systematically**: Complete one section/building at a time
- **Update as You Go**: Don't wait until you're home to update
- **Use Notes Wisely**: Add helpful info for next publisher

### Communication

- **Be Clear**: Write notes others can understand
- **Be Respectful**: Keep notes professional and kind
- **Be Accurate**: Double-check address and status
- **Be Timely**: Update immediately after visiting

### Time Management

- **Set Goals**: Aim for a certain number of addresses per session
- **Check Often**: Review your territories regularly
- **Return Promptly**: Don't keep territories too long
- **Ask for Help**: If territory is too large, communicate with servant

### Quality Work

- **Be Thorough**: Cover all addresses
- **Be Persistent**: Try different times/days for "not homes"
- **Be Flexible**: Adapt to what the territory needs
- **Be Prayerful**: Remember the purpose of the work

## Troubleshooting

### Can't Log In

**Forgot Password:**

1. Click "Forgot Password"
2. Enter your email
3. Check email for reset link
4. Create new password

**Account Locked:**

- Contact territory servant or administrator
- May need to verify your email
- Could be due to inactivity

### Changes Not Saving

**Possible Causes:**

- Poor internet connection
- Browser issue
- Someone else editing same record

**Solutions:**

- Check internet connection
- Refresh page and try again
- Wait a moment and retry
- Contact support if persists

### Map Not Loading

**Quick Fixes:**

- Refresh the page (Google Maps may timeout or fail to load initially)
- Check internet connection
- Verify VITE_GOOGLE_MAPS_API_KEY is correctly configured
- Check browser console for specific error messages
- Ensure Google Maps APIs are enabled in Google Cloud Console

### Connection or Sync Issues

**Problem**: Changes not saving or "Connection lost" message

**Solutions:**

- Check your internet connection
- Refresh the browser page
- The app will automatically reconnect when connection is restored
- Check if PocketBase backend is running
- Look for error messages in browser console

### Link Expired

**Problem**: Assignment link shows "Link has expired"

**Solutions:**

- Contact administrator for a new link
- Assignment links expire after set time (default 24 hours)
- Administrator needs to create a new assignment

### Permission Issues

**Problem**: "You don't have permission to access this" message

**Solutions:**

- Contact your administrator to grant appropriate role
- Verify your email is verified
- Check that you've been added to the correct congregation
- Log out and log back in to refresh permissions

## Getting Help

### Support Resources

1. **Administrator/Conductor**: Your congregation's Ministry Mapper admin
2. **GitHub Wiki**: https://github.com/rimorin/ministry-mapper/wiki
3. **Documentation**: Check the setup and security guides
4. **GitHub Issues**: Report bugs at the repository

### Reporting Problems

When reporting issues, include:

- What you were trying to do
- What happened instead
- Any error messages (exact text or screenshot)
- Browser and version (Chrome, Safari, Firefox, etc.)
- Device type (desktop, mobile, tablet)
- Operating system
- Whether you're using an assignment link or logged in

## Best Practices Summary

✓ Update address status immediately after visiting
✓ Write clear, respectful notes about visits
✓ Complete territories in reasonable time
✓ Communicate with administrators about issues
✓ Keep login credentials secure
✓ Respect householder privacy
✓ Use the mobile web app while in field service
✓ Ensure stable internet connection before starting
✓ Log out on shared devices
✓ Ask administrators if you need help

## Keyboard Shortcuts

Ministry Mapper uses standard browser shortcuts:

- `Escape`: Close open modals/dialogs
- `Tab`: Navigate between form fields
- `Enter`: Submit forms or confirm actions
- `Ctrl/Cmd + R`: Refresh page
- Standard text editing shortcuts work in note fields

## Privacy and Security

### Protecting Information

- **Never Share Login**: Your account credentials are personal
- **Log Out on Shared Devices**: Always log out when using public/shared computers
- **Use Strong Password**: Minimum 6 characters with numbers and capital letters
- **Be Careful With Notes**: Only record necessary, respectful information
- **Follow Privacy Laws**: Comply with GDPR, CCPA, and local data protection regulations
- **No Sensitive Data**: Avoid recording personal details beyond what's needed

### What Information is Stored

- Your account details (name, email, verified status)
- Your congregation role assignment
- Territory assignment links you've created or accessed
- Address updates you've made (status, notes, timestamp)
- Messages and instructions

### Data Storage

- **Backend**: PocketBase database (configured by administrator)
- **Hosting**: Depends on congregation's deployment
- **Real-time Sync**: Through PocketBase realtime subscriptions
- **Cached Data**: Service worker caches static assets for performance

## Conclusion

Ministry Mapper is a web-based tool that helps congregations manage field service territories efficiently. It eliminates paper waste, provides real-time collaboration, and works on any device with internet access.

**Key Features:**

- Real-time updates through PocketBase
- Google Maps integration for navigation
- Mobile-responsive design
- Multi-language support
- Role-based permissions
- Flexible congregation settings

**Getting Help:**

- Administrators: Check the setup guides
- Users: Contact your congregation administrator
- Technical Issues: Visit the GitHub wiki at https://github.com/rimorin/ministry-mapper/wiki

For backend setup information, see the ministry-mapper-be repository.
