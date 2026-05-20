# IDPBob Service Catalog Documentation

## Overview
The IDPBob Service Catalog provides a self-service portal for developers to request platform resources, tools, and services. It follows ServiceNow best practices with reusable variable sets, organized categories, and automated fulfillment workflows.

## Catalog Structure

### Service Catalog
- **Name**: IDP Service Catalog
- **Sys ID**: c1c1c1c1c1c1c1c1c1c1c1c1c1c1c1c1
- **Description**: Internal Developer Portal Service Catalog - Request development resources, tools, and platform services
- **Portal Integration**: Linked to IDPBob Portal

### Categories

#### Developer Services
- **Sys ID**: e1e1e1e1e1e1e1e1e1e1e1e1e1e1e1e1
- **Description**: Request development resources, environments, and platform services
- **Icon**: tree-view-2-line
- **Order**: 100

## Variable Sets

### Project Details Variable Set
**Sys ID**: d1d1d1d1d1d1d1d1d1d1d1d1d1d1d1d1

Reusable variable set containing common project information fields:

#### Variables:
1. **Project Name** (d2d2d2d2d2d2d2d2d2d2d2d2d2d2d2d2)
   - Type: Single Line Text
   - Mandatory: Yes
   - Regex Validation: `^[a-z0-9-]+$`
   - Help Text: Enter a unique name for your project (lowercase, alphanumeric, hyphens allowed)
   - Example: my-awesome-app
   - Order: 100

2. **Project Description** (d3d3d3d3d3d3d3d3d3d3d3d3d3d3d3d3)
   - Type: Multi-Line Text
   - Mandatory: Yes
   - Help Text: Provide a clear description of what this project does
   - Order: 200

## Catalog Items

### 1. New Application Project
**Sys ID**: f1f1f1f1f1f1f1f1f1f1f1f1f1f1f1f1

Request a new application project with automated infrastructure setup.

#### Features:
- Git repository creation
- CI/CD pipeline configuration
- Development, staging, and production environments
- Monitoring and logging setup
- Security scanning integration

#### Variables:
- **Variable Set**: Project Details (includes Project Name and Description)
- **Technology Stack** (f3f3f3f3f3f3f3f3f3f3f3f3f3f3f3f3)
  - Type: Select Box
  - Mandatory: Yes
  - Choices:
    - Node.js (nodejs) - Order 100
    - Python (Django/Flask) (python) - Order 200
    - Java (Spring Boot) (java) - Order 300
    - .NET Core (dotnet) - Order 400
    - Go (golang) - Order 500
    - Ruby (Rails) (ruby) - Order 600
  - Order: 300

#### Delivery Time: 1 business day
#### Category: Developer Services
#### Order: 100

---

### 2. Request API Access
**Sys ID**: f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6

Request access to internal or external APIs.

#### Features:
- API key generation
- OAuth client credentials
- Rate limit configuration
- API documentation access
- Sandbox environment for testing

#### Variables:
- API Name (text field)
- Access Type (API Key / OAuth)
- Environment (Sandbox / Production)
- Rate Limit Requirements (text)
- Business Justification (multi-line text)

#### Delivery Time: 2 hours (business hours)
#### Category: Developer Services
#### Order: 200

---

### 3. Provision Database
**Sys ID**: f7f7f7f7f7f7f7f7f7f7f7f7f7f7f7f7

Request a new database instance for your application.

#### Features:
- Choice of database engine (PostgreSQL, MySQL, MongoDB, Redis)
- Automated backups and point-in-time recovery
- High availability configuration
- Monitoring and alerting
- Connection credentials and endpoints
- Security group configuration

#### Variables:
- **Variable Set**: Project Details
- Database Engine (select box)
- Database Size (Small / Medium / Large)
- High Availability Required (yes/no)
- Backup Retention Days (number)
- Environment (Development / Staging / Production)

#### Delivery Time: 4 hours
#### Category: Developer Services
#### Order: 300

## Best Practices Implemented

### 1. Variable Sets
- **Reusability**: Common fields like Project Name and Description are in a reusable variable set
- **Consistency**: Same fields appear identically across multiple catalog items
- **Maintainability**: Update once, applies to all items using the variable set

### 2. Validation
- **Regex Patterns**: Project names validated with `^[a-z0-9-]+$` pattern
- **Mandatory Fields**: Critical information marked as required
- **Help Text**: Clear guidance for users on each field

### 3. Organization
- **Categories**: Logical grouping of related services
- **Ordering**: Consistent ordering (100, 200, 300) allows easy insertion of new items
- **Icons**: Visual identification using Remix Icon library

### 4. User Experience
- **Clear Descriptions**: HTML-formatted descriptions with bullet points
- **Delivery Times**: Set expectations for fulfillment
- **Short Descriptions**: Concise summaries for catalog browsing

### 5. Integration
- **Portal Linking**: Catalog linked to IDPBob Portal via sc_catalog field
- **Package Scoping**: All items scoped to IDPBob package for easy deployment

## System IDs Reference

| Component | Sys ID |
|-----------|--------|
| Service Catalog | c1c1c1c1c1c1c1c1c1c1c1c1c1c1c1c1 |
| Developer Services Category | e1e1e1e1e1e1e1e1e1e1e1e1e1e1e1e1 |
| Project Details Variable Set | d1d1d1d1d1d1d1d1d1d1d1d1d1d1d1d1 |
| Project Name Variable | d2d2d2d2d2d2d2d2d2d2d2d2d2d2d2d2 |
| Project Description Variable | d3d3d3d3d3d3d3d3d3d3d3d3d3d3d3d3 |
| New Application Item | f1f1f1f1f1f1f1f1f1f1f1f1f1f1f1f1 |
| Variable Set Link | f2f2f2f2f2f2f2f2f2f2f2f2f2f2f2f2 |
| Technology Stack Variable | f3f3f3f3f3f3f3f3f3f3f3f3f3f3f3f3 |
| Node.js Choice | f4f4f4f4f4f4f4f4f4f4f4f4f4f4f4f4 |
| Python Choice | f5f5f5f5f5f5f5f5f5f5f5f5f5f5f5f5 |
| API Access Item | f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6 |
| Database Provisioning Item | f7f7f7f7f7f7f7f7f7f7f7f7f7f7f7f7 |

## File Structure

```
2b7117ce872c8b9410ab877e0ebb3538/update/
├── sc_catalog_idp_service_catalog.xml                    # Main catalog
├── sc_category_developer_services.xml                    # Category
├── item_option_new_set_project_details.xml              # Variable set
├── item_option_new_project_name.xml                     # Variable
├── item_option_new_project_description.xml              # Variable
├── sc_cat_item_new_application.xml                      # Catalog item
├── io_set_item_new_application_project_details.xml      # Variable set link
├── item_option_new_technology_stack.xml                 # Variable
├── question_choice_nodejs.xml                           # Choice
├── question_choice_python.xml                           # Choice
├── sc_cat_item_api_access.xml                          # Catalog item
└── sc_cat_item_database_provisioning.xml               # Catalog item
```

## Deployment Instructions

1. **Import Update Set**
   - Navigate to System Update Sets > Retrieved Update Sets
   - Import the IDPBob update set
   - Preview and resolve any conflicts
   - Commit the update set

2. **Verify Catalog**
   - Navigate to Service Catalog > Catalogs
   - Confirm "IDP Service Catalog" exists
   - Verify it's linked to IDPBob Portal

3. **Test Catalog Items**
   - Access portal at `https://<instance>.service-now.com/idpbob`
   - Navigate to Service Catalog
   - Test each catalog item:
     - Verify all variables display correctly
     - Check validation rules work
     - Confirm variable sets are properly linked

4. **Configure Fulfillment**
   - Assign fulfillment groups to catalog items
   - Configure workflows for automated provisioning
   - Set up approval processes if required

## Future Enhancements

### Additional Catalog Items
- **Container Registry Access**: Request access to container registries
- **CI/CD Pipeline Setup**: Configure custom pipelines
- **Monitoring Dashboard**: Request custom monitoring dashboards
- **SSL Certificate**: Request SSL/TLS certificates
- **Cloud Resources**: Provision cloud infrastructure (VMs, storage, etc.)
- **Development Tools**: Request IDE licenses, tool access
- **Training Resources**: Access to learning materials

### Variable Set Enhancements
- **Environment Details**: Reusable set for environment configuration
- **Security Requirements**: Common security and compliance fields
- **Cost Center**: Budget and cost allocation fields
- **Team Information**: Team and ownership details

### Workflow Integration
- **Automated Provisioning**: Connect to infrastructure APIs
- **Approval Workflows**: Multi-level approval for sensitive resources
- **Notifications**: Email and Slack notifications
- **Status Updates**: Real-time provisioning status

### Reporting
- **Usage Analytics**: Track most requested services
- **Fulfillment Metrics**: Time to provision, success rates
- **User Satisfaction**: Feedback and ratings
- **Cost Tracking**: Resource utilization and costs

## Support

For issues or questions about the service catalog:
- **Documentation**: See this file and SERVICE_PORTAL_SETUP.md
- **Support Portal**: Submit a ticket through the portal
- **Email**: platform-team@company.com
- **Slack**: #idp-support channel