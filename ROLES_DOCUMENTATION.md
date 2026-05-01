# IDPBob Roles Documentation

## Overview
This document describes the roles created for the IDPBob ServiceNow scoped application. These roles are designed to provide comprehensive role-based access control (RBAC) for different personas in a DevOps/Platform Engineering environment.

## Scope
**Application Scope:** `x_ibmin_idpbob`  
**Application Name:** IDPBob  
**Scope ID:** `2b7117ce872c8b9410ab877e0ebb3538`

## Created Roles

### 1. Application Developer
- **Role Name:** `x_ibmin_idpbob.application_developer`
- **Suffix:** `application_developer`
- **Description:** Role for Application Developers who build and maintain applications on the platform
- **Use Case:** Assign to developers who need to create, modify, and deploy applications

### 2. Platform Engineer
- **Role Name:** `x_ibmin_idpbob.platform_engineer`
- **Suffix:** `platform_engineer`
- **Description:** Role for Platform Engineers who manage and maintain the platform infrastructure
- **Use Case:** Assign to engineers responsible for platform infrastructure, tooling, and automation

### 3. Site Reliability Engineer (SRE)
- **Role Name:** `x_ibmin_idpbob.sre`
- **Suffix:** `sre`
- **Description:** Role for Site Reliability Engineers (SRE) / Operations Engineers who ensure system reliability and performance
- **Use Case:** Assign to SREs and operations engineers who monitor, maintain, and improve system reliability

### 4. Security Engineer
- **Role Name:** `x_ibmin_idpbob.security_engineer`
- **Suffix:** `security_engineer`
- **Description:** Role for Security Engineers / Risk & Compliance Owners who manage security and compliance requirements
- **Use Case:** Assign to security professionals responsible for security policies, compliance, and risk management

### 5. Product Owner
- **Role Name:** `x_ibmin_idpbob.product_owner`
- **Suffix:** `product_owner`
- **Description:** Role for Product Owners / Engineering Managers who oversee product development and team management
- **Use Case:** Assign to product owners, engineering managers, and team leads

### 6. ServiceNow Platform Administrator
- **Role Name:** `x_ibmin_idpbob.servicenow_admin`
- **Suffix:** `servicenow_admin`
- **Description:** Role for ServiceNow Platform Administrators who manage the ServiceNow platform and configurations
- **Use Case:** Assign to administrators who manage ServiceNow platform configurations and settings

## Role Properties

All roles have the following properties:
- **Can Delegate:** Yes (true)
- **Grantable:** Yes (true)
- **Elevated Privilege:** No (false)
- **Scoped Admin:** No (false)

## Accessing Roles via Other Applications

These roles are accessible to other applications in the following ways:

### 1. ServiceNow UI
- Navigate to **User Administration > Roles**
- Search for roles with prefix `x_ibmin_idpbob`
- Assign roles to users or groups

### 2. ServiceNow REST API
Roles can be queried and managed via ServiceNow's Table API:

**Query Roles:**
```
GET /api/now/table/sys_user_role?sysparm_query=name STARTSWITH x_ibmin_idpbob
```

**Get Specific Role:**
```
GET /api/now/table/sys_user_role/{sys_id}
```

**Assign Role to User:**
```
POST /api/now/table/sys_user_has_role
{
  "user": "{user_sys_id}",
  "role": "{role_sys_id}"
}
```

### 3. ServiceNow Scripting
Roles can be accessed in server-side scripts:

```javascript
// Check if user has a role
var hasRole = gs.hasRole('x_ibmin_idpbob.application_developer');

// Get role by name
var gr = new GlideRecord('sys_user_role');
gr.addQuery('name', 'x_ibmin_idpbob.application_developer');
gr.query();
if (gr.next()) {
    gs.info('Role found: ' + gr.sys_id);
}
```

### 4. Integration with External Applications
External applications can integrate with these roles using:
- **ServiceNow REST API** with OAuth 2.0 or Basic Authentication
- **ServiceNow SOAP API** for legacy integrations
- **ServiceNow Scripted REST APIs** for custom endpoints

### 5. Access Control Lists (ACLs)
Create ACLs to control access to tables, fields, and operations based on these roles:

```javascript
// Example ACL condition
answer = gs.hasRole('x_ibmin_idpbob.application_developer') || 
         gs.hasRole('x_ibmin_idpbob.platform_engineer');
```

## Role Hierarchy Recommendations

Consider creating role hierarchies for better access management:

```
x_ibmin_idpbob.servicenow_admin (highest privilege)
  └── x_ibmin_idpbob.platform_engineer
      ├── x_ibmin_idpbob.sre
      ├── x_ibmin_idpbob.application_developer
      └── x_ibmin_idpbob.security_engineer
  └── x_ibmin_idpbob.product_owner
```

To implement role hierarchy, edit roles and add parent roles in the "Contains Roles" field.

## Next Steps

1. **Define Access Controls:** Create ACLs to control access to tables and fields based on these roles
2. **Assign Roles:** Assign appropriate roles to users and groups
3. **Create Assignment Groups:** Create assignment groups for each role for workflow routing
4. **Configure UI Actions:** Control visibility of UI actions based on roles
5. **Set up Business Rules:** Implement business logic based on user roles
6. **Create Reports:** Build reports to track role assignments and usage

## File Structure

All role definitions are stored in:
```
2b7117ce872c8b9410ab877e0ebb3538/update/
├── sys_user_role_application_developer.xml
├── sys_user_role_platform_engineer.xml
├── sys_user_role_sre.xml
├── sys_user_role_security_engineer.xml
├── sys_user_role_product_owner.xml
├── sys_user_role_servicenow_admin.xml
├── sys_embedded_help_role_application_developer.xml
├── sys_embedded_help_role_platform_engineer.xml
├── sys_embedded_help_role_sre.xml
├── sys_embedded_help_role_security_engineer.xml
├── sys_embedded_help_role_product_owner.xml
└── sys_embedded_help_role_servicenow_admin.xml
```

## Support

For questions or issues related to these roles, contact the IDPBob development team.

---
**Last Updated:** 2026-05-01  
**Version:** 1.0.0