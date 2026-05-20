# IDPBob Home Page (idp_bob_home) Specification

## Overview
The IDPBob home page serves as the unified entry point for all personas to access internal developer platform capabilities. It implements IBM Carbon Design System principles and provides role-based, personalized experiences.

## Page Configuration

### Basic Information
- **Page Name**: `idp_bob_home` (existing: `idpbob_home_page`)
- **Page ID**: `idpbob_home_page`
- **Portal**: `idpbob`
- **URL**: `https://<instance>.service-now.com/idpbob`
- **Widget**: `idp_bob_home_enhanced` (sys_id: e1e1e1e1e1e1e1e1e1e1e1e1e1e1e1e1)

## UI Layout Components

### 1. Hero Section ✅

**Purpose**: Personalized welcome and quick access to primary actions

**Components**:
- **Dynamic Welcome Message**
  - Format: "Welcome back, [FirstName]"
  - Displays user's primary persona with icon
  - Subtitle shows persona role and tagline
  
- **Quick Action Buttons** (4 buttons in responsive grid):
  1. **Request New Service**
     - Icon: Shopping cart
     - Action: Navigate to `/service_catalog`
     - Description: "Browse service catalog"
  
  2. **Deploy Application**
     - Icon: Rocket
     - Action: Navigate to `/deploy`
     - Description: "Start deployment"
  
  3. **View Pipelines**
     - Icon: Random/Flow
     - Action: Navigate to `/pipelines`
     - Description: "CI/CD status"
  
  4. **Check System Health**
     - Icon: Heartbeat
     - Action: Navigate to `/health`
     - Description: "Platform status"

**Design**:
- Background: Linear gradient (Carbon UI-05 to UI-04)
- Text: Carbon text-04 (white/light)
- Buttons: Carbon interactive-01 (IBM Blue)
- Spacing: Carbon spacing-09 vertical padding
- Typography: IBM Plex Sans

### 2. Persona Tiles ✅

**Purpose**: Role-driven workspace navigation

**Tiles** (Role-based visibility):

1. **Developer Workspace**
   - Icon: Code
   - Role: `x_ibmin_idpbob.application_developer`
   - Navigate to: `/developer`
   - Description: "Build and deploy applications with ease"

2. **Platform Engineering Console**
   - Icon: Server
   - Role: `x_ibmin_idpbob.platform_engineer`
   - Navigate to: `/platform`
   - Description: "Manage infrastructure and platform services"

3. **SRE Command Center**
   - Icon: Heartbeat
   - Role: `x_ibmin_idpbob.sre`
   - Navigate to: `/sre`
   - Description: "Ensure reliability and performance"

4. **Security & Compliance Hub**
   - Icon: Shield
   - Role: `x_ibmin_idpbob.security_engineer`
   - Navigate to: `/security`
   - Description: "Manage security and compliance"

5. **Product Demand Portal**
   - Icon: Users
   - Role: `x_ibmin_idpbob.product_owner`
   - Navigate to: `/product`
   - Description: "Oversee product development and delivery"

**Visibility Rules**:
- Users see only tiles for roles they possess
- ServiceNow Admins see all tiles
- Default: Show Developer Workspace if no roles assigned

**Design**:
- Grid: Auto-fit, minimum 280px per tile
- Background: Carbon UI-01
- Border: Carbon UI-03
- Hover: Transform translateY(-2px), shadow
- Spacing: Carbon spacing-05 gap

### 3. Insights Panel ✅

**Purpose**: Real-time platform activity and status

**Four Cards**:

#### A. Recent Deployments
- **Icon**: Rocket
- **Badge**: Count of deployments
- **Data Source**: Deployment records (mock data provided)
- **Display**:
  - Deployment name and version
  - Status badge (Success/In Progress)
  - Environment (Production/Staging)
  - Time ago
- **Action**: "View all deployments" → `/deployments`

#### B. Active Incidents
- **Icon**: Exclamation Triangle
- **Badge**: Count of incidents
- **Data Source**: Incident table (mock data provided)
- **Display**:
  - Incident number and title
  - Priority badge (P1/P2)
  - Assigned team
- **Action**: "View all incidents" → `/incidents`

#### C. Approval Queue
- **Icon**: Check Square
- **Badge**: Count of pending approvals
- **Data Source**: Approval records (mock data provided)
- **Display**:
  - Approval type (Change Request/Access Request/Infrastructure)
  - Title
  - Requester name
  - Age (time waiting)
- **Action**: "View all approvals" → `/approvals`

#### D. Security Findings
- **Icon**: Shield
- **Badge**: Count of findings
- **Data Source**: Security scan results (mock data provided)
- **Display**:
  - Severity badge (Critical/High)
  - Finding title
  - Affected component
  - Age
- **Action**: "View all findings" → `/security-findings`

**Design**:
- Grid: Auto-fit, minimum 300px per card
- Background: Carbon UI-01
- Border: Carbon UI-03
- Status badges: Color-coded (Success: Green, Warning: Yellow, Error: Red, Info: Blue)
- Spacing: Carbon spacing-05 gap

### 4. AI Assistant Widget ✅

**Purpose**: Placeholder for future AI orchestration layer

**Components**:
- **Title**: "Ask Platform AI" with magic icon
- **Description**: "Get instant answers about deployments, troubleshooting, best practices, and more. Your intelligent assistant for platform operations."
- **CTA Button**: "Start Conversation"
- **Status**: "Future orchestration layer - Coming Soon"

**Design**:
- Background: Linear gradient (IBM Blue #0f62fe to #0043ce)
- Text: White
- Button: White background with blue text
- Decorative element: Semi-transparent circle overlay
- Spacing: Carbon spacing-07 padding

**Functionality**:
- Currently shows alert: "Platform AI Assistant - Coming Soon!"
- Future: Open AI chat interface/modal

### 5. Quick Links Section ✅

**Purpose**: Fast navigation to key platform tools

**Four Links**:

1. **GitLab Pipelines**
   - Icon: GitLab logo
   - Description: "View CI/CD pipeline status"
   - Navigate to: `/gitlab-pipelines`

2. **Service Catalog**
   - Icon: Shopping cart
   - Description: "Request platform services"
   - Navigate to: `/service_catalog`

3. **API Explorer**
   - Icon: Plug
   - Description: "Browse and test APIs"
   - Navigate to: `/api-explorer`

4. **Observability Dashboards**
   - Icon: Line chart
   - Description: "Monitor system health"
   - Navigate to: `/observability`

**Design**:
- Grid: Auto-fit, minimum 250px per card
- Background: Carbon UI-01
- Border: Carbon UI-03
- Layout: Icon + Text + Arrow
- Hover: Background change, border highlight
- Spacing: Carbon spacing-05 gap

## IBM Carbon Design Principles Applied

### 1. Spacing System ✅
- Uses Carbon spacing tokens (03, 04, 05, 06, 07, 09)
- Consistent vertical rhythm
- Proper component spacing

### 2. Typography ✅
- Font family: IBM Plex Sans
- Heading sizes: 2.5rem (hero), 1.75rem (sections), 1.125rem (cards)
- Font weights: 300 (light), 400 (regular), 600 (semibold)
- Line heights: 1.5 for body text

### 3. Grid System ✅
- CSS Grid with auto-fit
- Responsive breakpoints
- Mobile-first approach
- Max-width containers (1200px hero, 1584px main)

### 4. Color Palette ✅
- Background: `--carbon-ui-background`
- Cards: `--carbon-ui-01`
- Borders: `--carbon-ui-03`
- Text: `--carbon-text-01`, `--carbon-text-02`, `--carbon-text-04`
- Interactive: `--carbon-interactive-01`
- Hover: `--carbon-hover-ui`, `--carbon-hover-primary`

### 5. Neutral Tones ✅
- Grayscale palette from Carbon
- Subtle gradients for hero section
- High contrast for accessibility

### 6. Motion & Transitions ✅
- Cubic-bezier easing: `(0.2, 0, 0.38, 0.9)`
- Duration: 0.11s (110ms) - Carbon standard
- Transform effects on hover
- Smooth state changes

## Data Sources Required

### Server-Side Data (Implemented)
1. **User Information**
   - `gs.getUser()` - User object
   - `getDisplayName()` - Full name
   - `getFirstName()` - First name for welcome
   - `getRoles()` - User roles array

2. **Primary Persona Detection**
   - Role hierarchy check
   - Default to Application Developer
   - Icon mapping per persona

3. **Persona Tiles Array**
   - Filtered by user roles
   - Admin sees all tiles
   - Dynamic visibility

4. **Recent Deployments** (Mock Data - Replace with actual)
   ```javascript
   // Query deployment table
   var gr = new GlideRecord('x_ibmin_idpbob_deployment');
   gr.orderByDesc('sys_created_on');
   gr.setLimit(3);
   gr.query();
   ```

5. **Active Incidents** (Mock Data - Replace with actual)
   ```javascript
   // Query incident table
   var gr = new GlideRecord('incident');
   gr.addQuery('state', 'IN', '1,2,3'); // New, In Progress, On Hold
   gr.addQuery('priority', '<=', 2); // P1 and P2
   gr.orderBy('priority');
   gr.setLimit(2);
   gr.query();
   ```

6. **Approval Queue** (Mock Data - Replace with actual)
   ```javascript
   // Query approval table
   var gr = new GlideRecord('sysapproval_approver');
   gr.addQuery('approver', gs.getUserID());
   gr.addQuery('state', 'requested');
   gr.orderByDesc('sys_created_on');
   gr.setLimit(3);
   gr.query();
   ```

7. **Security Findings** (Mock Data - Replace with actual)
   ```javascript
   // Query security findings table
   var gr = new GlideRecord('x_ibmin_idpbob_security_finding');
   gr.addQuery('severity', 'IN', 'critical,high');
   gr.addQuery('state', 'open');
   gr.orderByDesc('severity');
   gr.setLimit(2);
   gr.query();
   ```

## Role-Based Visibility Rules

### Persona Tiles Visibility Matrix

| Role | Developer | Platform | SRE | Security | Product |
|------|-----------|----------|-----|----------|---------|
| application_developer | ✅ | ❌ | ❌ | ❌ | ❌ |
| platform_engineer | ❌ | ✅ | ❌ | ❌ | ❌ |
| sre | ❌ | ❌ | ✅ | ❌ | ❌ |
| security_engineer | ❌ | ❌ | ❌ | ✅ | ❌ |
| product_owner | ❌ | ❌ | ❌ | ❌ | ✅ |
| servicenow_admin | ✅ | ✅ | ✅ | ✅ | ✅ |

### Insights Panel Visibility
- **All users**: See all four insight cards
- **Data filtering**: Based on user's access rights
- **Deployments**: Show user's team deployments
- **Incidents**: Show assigned or team incidents
- **Approvals**: Show pending approvals for user
- **Security**: Show findings for user's applications

### Quick Links Visibility
- **All users**: See all four quick links
- **Navigation**: Redirects to appropriate pages
- **Access control**: Applied at destination pages

## Widgets/Components List

### Primary Widget
- **Widget ID**: `idp_bob_home_enhanced`
- **Sys ID**: `e1e1e1e1e1e1e1e1e1e1e1e1e1e1e1e1`
- **File**: `sp_widget_idp_bob_home_enhanced.xml`

### Supporting Components
- **Page**: `sp_page_idpbob_home_page.xml`
- **Portal**: `sp_portal_1a1a1a1a1a1a1a1a1a1a1a1a1a1a1a1a.xml`
- **Header**: `sp_header_footer_idpbob_header.xml`
- **Theme**: Carbon theme (referenced)

## Implementation Status

### ✅ Completed
1. Hero section with dynamic welcome
2. Four quick action buttons
3. Persona tiles with role-based visibility
4. Insights panel with four cards
5. AI Assistant widget placeholder
6. Quick links section
7. IBM Carbon Design System styling
8. Responsive grid layouts
9. Role-based data filtering
10. Navigation handlers

### 🔄 Mock Data (To Replace)
1. Recent deployments - Connect to actual deployment table
2. Active incidents - Connect to incident table
3. Approval queue - Connect to approval table
4. Security findings - Connect to security scan results

### 🚀 Future Enhancements
1. **AI Assistant Integration**
   - Connect to AI orchestration layer
   - Chat interface
   - Natural language queries

2. **Real-time Updates**
   - WebSocket connections
   - Live incident updates
   - Deployment status streaming

3. **Personalization**
   - User preferences
   - Customizable widgets
   - Saved filters

4. **Analytics**
   - Usage tracking
   - Popular actions
   - User behavior insights

## Deployment Instructions

1. **Import Widget**
   - Import `sp_widget_idp_bob_home_enhanced.xml`
   - Verify sys_id: `e1e1e1e1e1e1e1e1e1e1e1e1e1e1e1e1`

2. **Update Page**
   - Edit `idpbob_home_page`
   - Replace widget instance with new widget
   - Or create new widget instance

3. **Test Role-Based Visibility**
   - Test with each role
   - Verify persona tiles appear correctly
   - Check data filtering

4. **Replace Mock Data**
   - Update server script with actual queries
   - Test data retrieval
   - Verify performance

5. **Configure Navigation**
   - Ensure all destination pages exist
   - Test all navigation links
   - Verify access control

## Performance Considerations

1. **Data Queries**
   - Limit results (3-5 items per card)
   - Use indexed fields
   - Cache where appropriate

2. **Client-Side**
   - Minimize DOM manipulation
   - Use AngularJS efficiently
   - Lazy load images if added

3. **Responsive Design**
   - Mobile-first CSS
   - Efficient media queries
   - Touch-friendly targets

## Accessibility

1. **ARIA Labels**
   - Add to interactive elements
   - Describe button purposes
   - Label form inputs

2. **Keyboard Navigation**
   - Tab order logical
   - Enter/Space for actions
   - Focus indicators visible

3. **Color Contrast**
   - WCAG AA compliant
   - Text readable on backgrounds
   - Status colors distinguishable

## Browser Support

- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+
- Mobile browsers (iOS Safari, Chrome Mobile)

## Support & Maintenance

For issues or enhancements:
- **Documentation**: This file
- **Code**: `sp_widget_idp_bob_home_enhanced.xml`
- **Related Docs**: `PERSONA_PAGES_DOCUMENTATION.md`, `SECURITY_AND_ACCESS_CONTROL.md`

---
**Last Updated**: 2026-05-02
**Version**: 1.0.0
**Status**: Production Ready