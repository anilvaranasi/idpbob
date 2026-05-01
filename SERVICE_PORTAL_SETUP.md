# IDPBob Service Portal Setup

## Overview
This document describes the Service Portal configuration for the IDPBob Internal Developer Portal.

## Portal Configuration

### Portal Details
- **Name**: IDPBob Portal
- **URL Suffix**: idpbob
- **Homepage**: IDP AG Home (idp_ag_home)
- **Theme**: IDPBob Carbon Theme (idpbob_carbon_theme)
- **Header**: IDPBob Header

### Access URL
Access the portal at: `https://<instance>.service-now.com/idpbob`

## Components

### 1. Theme (sp_theme_idpbob_carbon_theme.xml)
- Implements IBM Carbon Design System
- Includes Carbon color palette, spacing, and typography
- Uses IBM Plex Sans font family
- Provides consistent styling for all portal components

### 2. Homepage (sp_page_idpbob_home_page.xml)
- **Page ID**: idp_ag_home
- **Title**: Internal Developer Portal
- Contains custom CSS for hero section, persona cards, quick actions, and resources
- Responsive design with mobile breakpoints

### 3. Homepage Widget (sp_widget_idp_home_content.xml)
- **Widget ID**: idp_home_content
- **Name**: IDP Home Content
- Features:
  - Hero section with search bar
  - Platform statistics dashboard
  - Persona-based navigation cards (6 personas)
  - Quick action buttons (8 actions)
  - Resource cards (3 categories)
- Server-side script fetches user data and roles
- Client-side navigation handlers

### 4. Page Layout Structure
The homepage uses the following layout hierarchy:
- **Container**: IDP Home Container (sp_container_idp_home_container.xml)
  - **Row**: IDP Home Row (sp_row_idp_home_row.xml)
    - **Column**: 12-column full-width (sp_column_idp_home_column.xml)
      - **Widget Instance**: IDP Home Widget Instance (sp_instance_idp_home_widget_instance.xml)

### 5. Header (sp_header_footer_idpbob_header.xml)
- **Name**: IDPBob Header
- Navigation links: Home, Catalog, Knowledge, My Requests
- User avatar with initials
- Carbon Design System styling

## Personas Supported

The portal supports 6 distinct personas:
1. **Application Developer** - Build and deploy applications
2. **Platform Engineer** - Manage infrastructure and platform services
3. **SRE / Operations** - Ensure reliability and performance
4. **Security Engineer** - Manage security and compliance
5. **Product Owner** - Oversee product development
6. **ServiceNow Admin** - Configure and manage the platform

## Quick Actions

The homepage provides 8 quick action buttons:
1. Service Catalog
2. API Catalog
3. Templates
4. Documentation
5. Developer Tools
6. My Requests
7. Knowledge Base
8. Get Support

## Technical Details

### File Structure
```
2b7117ce872c8b9410ab877e0ebb3538/update/
├── sp_portal_idpbob_portal.xml          # Portal configuration
├── sp_theme_idpbob_carbon_theme.xml     # Carbon theme
├── sp_page_idpbob_home_page.xml         # Homepage definition
├── sp_widget_idp_home_content.xml       # Homepage widget
├── sp_instance_idp_home_widget_instance.xml  # Widget instance
├── sp_container_idp_home_container.xml  # Page container
├── sp_row_idp_home_row.xml              # Page row
├── sp_column_idp_home_column.xml        # Page column
└── sp_header_footer_idpbob_header.xml   # Portal header
```

### System IDs
- Portal: 1a1a1a1a1a1a1a1a1a1a1a1a1a1a1a1a
- Theme: 2b2b2b2b2b2b2b2b2b2b2b2b2b2b2b2b
- Page: 3c3c3c3c3c3c3c3c3c3c3c3c3c3c3c3c
- Header: 4d4d4d4d4d4d4d4d4d4d4d4d4d4d4d4d
- Widget: 6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f
- Column: 7f7f7f7f7f7f7f7f7f7f7f7f7f7f7f7f
- Widget Instance: 8f8f8f8f8f8f8f8f8f8f8f8f8f8f8f8f
- Container: 9f9f9f9f9f9f9f9f9f9f9f9f9f9f9f9f
- Row: af9f9f9f9f9f9f9f9f9f9f9f9f9f9f9f

## Deployment

To deploy this portal to a ServiceNow instance:

1. Ensure all XML files are in the update set
2. Import the update set to the target instance
3. Preview and commit the update set
4. Navigate to `https://<instance>.service-now.com/idpbob`

## Known Issues

See Bob Findings panel for identified issues that should be addressed:
1. Mock statistics data needs to be replaced with actual queries
2. Search validation needs improvement
3. User roles handling needs null checks
4. Persona ID naming inconsistency
5. Placeholder resource links need implementation
6. Inline styles should be moved to CSS

## Future Enhancements

- Implement actual statistics queries
- Create persona-specific landing pages
- Implement search functionality
- Add actual resource links
- Create API catalog integration
- Add template library
- Implement developer tools section