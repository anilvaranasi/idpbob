# IDPBob Persona Pages Documentation

## Overview
This document describes the persona-specific pages and widgets created for the IDPBob Internal Developer Portal. Each persona has a dedicated page with role-based access control and tailored content.

## Created Pages

### 1. Service Catalog Page
- **Page ID**: `service_catalog`
- **Sys ID**: `a1a1a1a1a1a1a1a1a1a1a1a1a1a1a1a1`
- **URL**: `/service_catalog`
- **Access**: All authenticated users
- **Purpose**: Display available platform services and catalog items
- **Widget**: Service Catalog Content (`c1c1c1c1c1c1c1c1c1c1c1c1c1c1c1c2`)

### 2. Application Developer Page
- **Page ID**: `developer`
- **Sys ID**: `b1b1b1b1b1b1b1b1b1b1b1b1b1b1b1b1`
- **URL**: `/developer`
- **Access**: `x_ibmin_idpbob.application_developer` role
- **Purpose**: Developer-focused dashboard with quick actions and resources
- **Widget**: Developer Dashboard (`d1d1d1d1d1d1d1d1d1d1d1d1d1d1d1d1`)

### 3. Platform Engineer Page
- **Page ID**: `platform`
- **Sys ID**: `b2b2b2b2b2b2b2b2b2b2b2b2b2b2b2b2`
- **URL**: `/platform`
- **Access**: `x_ibmin_idpbob.platform_engineer` role
- **Purpose**: Infrastructure and platform management dashboard
- **Widget**: To be created (Platform Dashboard)

### 4. SRE / Operations Page
- **Page ID**: `sre`
- **Sys ID**: `b3b3b3b3b3b3b3b3b3b3b3b3b3b3b3b3`
- **URL**: `/sre`
- **Access**: `x_ibmin_idpbob.sre` role
- **Purpose**: Site reliability and operations monitoring dashboard
- **Widget**: To be created (SRE Dashboard)

### 5. Security Engineer Page
- **Page ID**: `security`
- **Sys ID**: `b4b4b4b4b4b4b4b4b4b4b4b4b4b4b4b4`
- **URL**: `/security`
- **Access**: `x_ibmin_idpbob.security_engineer` role
- **Purpose**: Security and compliance management dashboard
- **Widget**: To be created (Security Dashboard)

### 6. Product Owner Page
- **Page ID**: `product`
- **Sys ID**: `b5b5b5b5b5b5b5b5b5b5b5b5b5b5b5b5`
- **URL**: `/product`
- **Access**: `x_ibmin_idpbob.product_owner` role
- **Purpose**: Product management and team metrics dashboard
- **Widget**: To be created (Product Dashboard)

### 7. ServiceNow Admin Page
- **Page ID**: `admin`
- **Sys ID**: `b6b6b6b6b6b6b6b6b6b6b6b6b6b6b6b6`
- **URL**: `/admin`
- **Access**: `x_ibmin_idpbob.servicenow_admin` role
- **Purpose**: Platform administration and configuration dashboard
- **Widget**: To be created (Admin Dashboard)

## Created Widgets

### 1. Service Catalog Content Widget
- **Widget ID**: `service_catalog_content`
- **Sys ID**: `c1c1c1c1c1c1c1c1c1c1c1c1c1c1c1c2`
- **Purpose**: Displays catalog items grouped by category
- **Features**:
  - Fetches items from IDP Service Catalog
  - Groups items by category
  - Click-to-order functionality
  - Displays delivery time and descriptions
  - Responsive grid layout

### 2. Developer Dashboard Widget
- **Widget ID**: `developer_dashboard`
- **Sys ID**: `d1d1d1d1d1d1d1d1d1d1d1d1d1d1d1d1`
- **Purpose**: Application developer-focused dashboard
- **Features**:
  - Quick actions for common tasks
  - Available services list
  - Developer resources links
  - Recent requests history
  - Getting started guide
  - Role-based access control

### 3. IDP Home Content Widget (Updated)
- **Widget ID**: `idp_home_content`
- **Sys ID**: `6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f`
- **Purpose**: Homepage with persona navigation
- **Updates**:
  - Fixed navigation to use actual page IDs
  - Service catalog link points to `/service_catalog`
  - Persona cards navigate to respective pages
  - All quick actions properly routed

## Navigation Structure

### Home Page Links
From the home page (`/home`), users can navigate to:
- **Persona Pages**: Click persona cards to go to role-specific dashboards
- **Service Catalog**: Quick action button or direct link
- **Other Services**: API Catalog, Templates, Documentation, etc.

### Persona Page Navigation
The home page persona cards now correctly navigate to:
- Developer → `/developer`
- Platform Engineer → `/platform`
- SRE → `/sre`
- Security Engineer → `/security`
- Product Owner → `/product`
- ServiceNow Admin → `/admin`

### Service Catalog Integration
- Home page "Service Catalog" quick action → `/service_catalog`
- Service catalog page displays all catalog items
- Click any item to order → `/sc_cat_item?sys_id={item_id}`

## Implementation Details

### Role-Based Access Control
Each persona page has the appropriate role assigned in the `<roles>` field:
```xml
<roles>x_ibmin_idpbob.application_developer</roles>
```

This ensures only users with the correct role can access the page.

### Widget Integration
Widgets are integrated into pages through:
1. Widget instances (sp_instance)
2. Containers (sp_container)
3. Rows (sp_row)
4. Columns (sp_column)

### Client-Side Navigation
All navigation uses AngularJS `$location` service:
```javascript
c.navigateToPersona = function(personaId) {
    $location.path('/' + personaId);
};
```

### Server-Side Data
Widgets fetch data server-side using GlideRecord:
```javascript
var gr = new GlideRecord('sc_cat_item');
gr.addQuery('sc_catalogs', 'c1c1c1c1c1c1c1c1c1c1c1c1c1c1c1c1');
gr.query();
```

## File Structure

```
2b7117ce872c8b9410ab877e0ebb3538/update/
├── sp_page_service_catalog.xml          # Service Catalog page
├── sp_page_developer.xml                # Application Developer page
├── sp_page_platform.xml                 # Platform Engineer page
├── sp_page_sre.xml                      # SRE page
├── sp_page_security.xml                 # Security Engineer page
├── sp_page_product.xml                  # Product Owner page
├── sp_page_admin.xml                    # ServiceNow Admin page
├── sp_widget_service_catalog_content.xml # Service Catalog widget
├── sp_widget_developer_dashboard.xml    # Developer Dashboard widget
└── sp_widget_idp_home_content.xml       # Updated Home widget
```

## Next Steps

### Remaining Widgets to Create
1. **Platform Dashboard Widget** - For Platform Engineers
2. **SRE Dashboard Widget** - For Site Reliability Engineers
3. **Security Dashboard Widget** - For Security Engineers
4. **Product Dashboard Widget** - For Product Owners
5. **Admin Dashboard Widget** - For ServiceNow Admins

### Widget Features to Implement
Each dashboard widget should include:
- Role-specific quick actions
- Relevant metrics and KPIs
- Recent activity feed
- Resource links
- Getting started guide
- Integration with ServiceNow tables

### Additional Pages Needed
- `/requests` - My Requests page
- `/kb` - Knowledge Base page
- `/documentation` - Documentation page
- `/api-catalog` - API Catalog page
- `/templates` - Code Templates page
- `/tools` - Developer Tools page
- `/support` - Support page
- `/search` - Search results page

## Testing Checklist

- [ ] Home page loads correctly
- [ ] Persona cards navigate to correct pages
- [ ] Service catalog link works from home page
- [ ] Service catalog displays all items
- [ ] Developer dashboard loads for users with developer role
- [ ] Role-based access control works (users without role cannot access)
- [ ] All quick actions navigate correctly
- [ ] Catalog item ordering works
- [ ] Recent requests display correctly
- [ ] All links are functional (no broken links)

## Deployment Instructions

1. **Import Update Set**
   - Navigate to System Update Sets > Retrieved Update Sets
   - Import the IDPBob update set
   - Preview and commit

2. **Verify Pages**
   - Navigate to Service Portal > Pages
   - Confirm all 7 pages exist
   - Check role assignments

3. **Verify Widgets**
   - Navigate to Service Portal > Widgets
   - Confirm all widgets exist
   - Test widget preview

4. **Test Navigation**
   - Access portal at `/idpbob`
   - Click each persona card
   - Verify navigation works
   - Test service catalog link

5. **Test Role-Based Access**
   - Create test users with different roles
   - Verify each can access their persona page
   - Verify users cannot access pages without proper role

## Support

For issues or questions:
- **Documentation**: See this file and related docs
- **Support Portal**: Submit ticket through portal
- **Email**: platform-team@company.com

---
**Last Updated**: 2026-05-02
**Version**: 1.0.0