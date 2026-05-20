# Enhanced Service Catalog Documentation

## Overview
This document describes the Port.io-style enhanced service catalog implementation for IDPBob, including all categories, catalog items, and integration points.

## Architecture

### Service Catalog Structure
```
IDP Service Catalog (c1c1c1c1c1c1c1c1c1c1c1c1c1c1c1c1)
├── Application Services (d1d1d1d1d1d1d1d1d1d1d1d1d1d1d1d1)
│   ├── Service Onboarding Request
│   └── New Microservice Creation
├── Infrastructure Services (d2d2d2d2d2d2d2d2d2d2d2d2d2d2d2d2)
├── CI/CD Services (e1e1e1e1e1e1e1e1e1e1e1e1e1e1e1e1)
│   └── Pipeline Provisioning (GitLab Integration)
├── Security & Compliance Services (f1f1f1f1f1f1f1f1f1f1f1f1f1f1f1f1)
│   └── Secrets Provisioning Request
└── Observability Services (a6a6a6a6a6a6a6a6a6a6a6a6a6a6a6a6)
    └── Monitoring Dashboard Creation
```

## Categories

### 1. Application Services
**ID:** `d1d1d1d1d1d1d1d1d1d1d1d1d1d1d1d1`  
**Order:** 100  
**Icon:** `code`

Services for application development, deployment, and lifecycle management.

**Use Cases:**
- Service registration and onboarding
- Microservice scaffolding
- Application templates
- API management

### 2. Infrastructure Services
**ID:** `d2d2d2d2d2d2d2d2d2d2d2d2d2d2d2d2`  
**Order:** 200  
**Icon:** `server`

Infrastructure provisioning and management services.

**Use Cases:**
- Kubernetes namespace provisioning
- Database provisioning
- Storage provisioning
- Network configuration

### 3. CI/CD Services
**ID:** `e1e1e1e1e1e1e1e1e1e1e1e1e1e1e1e1`  
**Order:** 300  
**Icon:** `git-branch`

Continuous integration and deployment services.

**Use Cases:**
- Pipeline creation and configuration
- GitLab integration
- Automated testing setup
- Deployment automation

### 4. Security & Compliance Services
**ID:** `f1f1f1f1f1f1f1f1f1f1f1f1f1f1f1f1`  
**Order:** 400  
**Icon:** `shield`

Security, compliance, and governance services.

**Use Cases:**
- Secrets management
- Security scanning
- Compliance reporting
- Access control

### 5. Observability Services
**ID:** `a6a6a6a6a6a6a6a6a6a6a6a6a6a6a6a6`  
**Order:** 500  
**Icon:** `line-chart-line`

Monitoring, logging, and observability services.

**Use Cases:**
- Dashboard creation
- Alert configuration
- Log aggregation
- Distributed tracing

## Catalog Items

### 1. Service Onboarding Request
**ID:** `b1b1b1b1b1b1b1b1b1b1b1b1b1b1b1b1`  
**Category:** Application Services  
**Order:** 100

**Description:**
Register a new service in the IDP catalog with metadata, ownership, and dependencies.

**Deliverables:**
- Service registered in Port.io-style catalog
- Metadata and ownership configured
- Dependencies mapped
- Service health dashboard created
- Documentation template generated

**Prerequisites:**
- Service name and description
- Team ownership identified
- Technology stack defined
- Repository URL (if applicable)

**Approval Process:**
Platform Engineer approval required for catalog registration.

**SLA:**
- **Fulfillment Time:** 2 business hours
- **Response Time:** 30 minutes

**Roles:** 
- `x_ibmin_idpbob.application_developer`
- `x_ibmin_idpbob.platform_engineer`

**Tags:** `service-catalog, onboarding, metadata, port-io`

---

### 2. New Microservice Creation
**ID:** `b2b2b2b2b2b2b2b2b2b2b2b2b2b2b2b2`  
**Category:** Application Services  
**Order:** 200

**Description:**
Create a new microservice with standardized templates, CI/CD pipeline, and infrastructure.

**Deliverables:**
- GitLab repository with service template
- CI/CD pipeline configured (GitLab CI)
- Kubernetes deployment manifests
- Service mesh integration (Istio)
- Observability stack (Prometheus, Grafana, Jaeger)
- API documentation (OpenAPI/Swagger)
- Security scanning enabled (SAST, DAST, SCA)

**Prerequisites:**
- Service name and description
- Technology stack selection (Node.js, Python, Java, Go)
- Team ownership
- Resource requirements (CPU, memory)

**Approval Process:**
Platform Engineer approval required for infrastructure provisioning.

**SLA:**
- **Fulfillment Time:** 4 business hours
- **Response Time:** 1 hour

**Roles:**
- `x_ibmin_idpbob.application_developer`
- `x_ibmin_idpbob.platform_engineer`

**Tags:** `microservice, kubernetes, ci-cd, gitlab, service-mesh, golden-path`

---

### 3. Pipeline Provisioning (GitLab Integration)
**ID:** `b3b3b3b3b3b3b3b3b3b3b3b3b3b3b3b3`  
**Category:** CI/CD Services  
**Order:** 300

**Description:**
Create or update CI/CD pipelines with GitLab integration, automated testing, and deployment stages.

**Deliverables:**
- GitLab CI/CD pipeline configuration (.gitlab-ci.yml)
- Multi-stage pipeline (build, test, scan, deploy)
- Automated testing integration (unit, integration, e2e)
- Security scanning (SAST, DAST, dependency scanning)
- Container image building and scanning
- Kubernetes deployment automation
- Environment-specific configurations (dev, staging, prod)
- Rollback capabilities

**Prerequisites:**
- GitLab repository URL
- Service name and type
- Target environments
- Deployment strategy (rolling, blue-green, canary)

**Approval Process:**
Platform Engineer approval required. Production deployments require additional SRE approval.

**SLA:**
- **Fulfillment Time:** 3 business hours
- **Response Time:** 45 minutes

**Roles:**
- `x_ibmin_idpbob.application_developer`
- `x_ibmin_idpbob.platform_engineer`
- `x_ibmin_idpbob.sre`

**Tags:** `ci-cd, gitlab, pipeline, automation, gitops, kubernetes, deployment`

---

### 4. Secrets Provisioning Request
**ID:** `b4b4b4b4b4b4b4b4b4b4b4b4b4b4b4b4`  
**Category:** Security & Compliance Services  
**Order:** 400

**Description:**
Request secure storage and management of application secrets, API keys, and credentials.

**Deliverables:**
- HashiCorp Vault secret path created
- Access policies configured
- Kubernetes secret integration (External Secrets Operator)
- Automatic secret rotation enabled
- Audit logging configured
- Access control policies (RBAC)
- Secret versioning enabled

**Prerequisites:**
- Service name and namespace
- Secret type (database credentials, API keys, certificates, etc.)
- Access requirements (which services need access)
- Rotation policy preference

**Approval Process:**
Security Engineer approval required for all secret provisioning requests.

**SLA:**
- **Fulfillment Time:** 2 business hours
- **Response Time:** 30 minutes

**Security Notes:**
- Secrets are encrypted at rest and in transit
- Access is logged and audited
- Automatic rotation policies enforced
- Least-privilege access model

**Roles:**
- `x_ibmin_idpbob.application_developer`
- `x_ibmin_idpbob.platform_engineer`
- `x_ibmin_idpbob.security_engineer`

**Tags:** `secrets-management, vault, security, kubernetes, encryption, compliance`

---

### 5. Monitoring Dashboard Creation
**ID:** `b5b5b5b5b5b5b5b5b5b5b5b5b5b5b5b5`  
**Category:** Observability Services  
**Order:** 500

**Description:**
Create custom monitoring dashboards with metrics, alerts, and visualizations for your services.

**Deliverables:**
- Grafana dashboard with custom panels
- Prometheus metrics integration
- Pre-configured service health metrics (RED/USE)
- Custom business metrics support
- Alert rules configured (PagerDuty/Slack integration)
- SLO/SLI tracking dashboards
- Distributed tracing integration (Jaeger)
- Log correlation (Loki/ELK)

**Dashboard Types:**
- **Service Health:** Request rate, error rate, duration (RED metrics)
- **Infrastructure:** CPU, memory, disk, network (USE metrics)
- **Business Metrics:** Custom KPIs and business logic
- **SLO Dashboard:** Service level objectives and error budgets

**Prerequisites:**
- Service name and namespace
- Metrics endpoints configured
- Dashboard type selection
- Alert notification channels

**Approval Process:**
SRE approval required for production monitoring setup.

**SLA:**
- **Fulfillment Time:** 3 business hours
- **Response Time:** 1 hour

**Roles:**
- `x_ibmin_idpbob.application_developer`
- `x_ibmin_idpbob.sre`
- `x_ibmin_idpbob.platform_engineer`

**Tags:** `observability, monitoring, grafana, prometheus, alerts, slo, metrics, dashboards`

---

## Service Portal Integration

### Service Catalog Page
**Page ID:** `service_catalog`  
**URL:** `/idpbob?id=service_catalog` or `/sc`  
**Widget:** `service_catalog_content` (c7c7c7c7c7c7c7c7c7c7c7c7c7c7c7c7)

**Features:**
- Category-based filtering
- Search functionality (name, description, tags)
- Card-based catalog item display
- SLA information display
- Direct ordering from cards
- Role-based visibility

### Home Page Integration
The home page (`idpbob_home_page`) includes a "Service Catalog" quick action that navigates to `/sc`.

**Navigation Path:**
```
Home Page → Quick Actions → Service Catalog → /sc
```

## Role-Based Access Control

### Catalog Item Visibility

| Catalog Item | Application Developer | Platform Engineer | SRE | Security Engineer | Product Owner | ServiceNow Admin |
|--------------|----------------------|-------------------|-----|-------------------|---------------|------------------|
| Service Onboarding | ✓ | ✓ | - | - | - | ✓ |
| New Microservice | ✓ | ✓ | - | - | - | ✓ |
| Pipeline Provisioning | ✓ | ✓ | ✓ | - | - | ✓ |
| Secrets Provisioning | ✓ | ✓ | - | ✓ | - | ✓ |
| Monitoring Dashboard | ✓ | ✓ | ✓ | - | - | ✓ |

## Approval Workflows

### Approval Matrix

| Catalog Item | Primary Approver | Secondary Approver | Notes |
|--------------|------------------|-------------------|-------|
| Service Onboarding | Platform Engineer | - | Standard approval |
| New Microservice | Platform Engineer | - | Infrastructure provisioning |
| Pipeline Provisioning | Platform Engineer | SRE (for prod) | Production requires SRE |
| Secrets Provisioning | Security Engineer | - | Security review required |
| Monitoring Dashboard | SRE | - | Production monitoring |

## SLA Summary

| Catalog Item | Fulfillment Time | Response Time |
|--------------|------------------|---------------|
| Service Onboarding | 2 business hours | 30 minutes |
| New Microservice | 4 business hours | 1 hour |
| Pipeline Provisioning | 3 business hours | 45 minutes |
| Secrets Provisioning | 2 business hours | 30 minutes |
| Monitoring Dashboard | 3 business hours | 1 hour |

## GitOps Integration

### GitLab CI/CD Ready
All catalog items are designed to integrate with GitLab CI/CD:

1. **Pipeline Provisioning** - Direct GitLab integration
2. **New Microservice** - Includes .gitlab-ci.yml template
3. **Service Onboarding** - Repository linking
4. **Secrets Provisioning** - GitLab CI/CD variables integration
5. **Monitoring Dashboard** - Pipeline metrics integration

### Automation Hooks
Each catalog item includes workflow placeholders for:
- API-driven provisioning
- GitOps automation
- Webhook integration
- Status callbacks

## Port.io-Style Features

### Service Catalog Principles
1. **Self-Service:** Developers can request resources without manual intervention
2. **Standardization:** Golden paths and templates for common patterns
3. **Automation:** API-driven provisioning and GitOps integration
4. **Observability:** Built-in monitoring and tracking
5. **Governance:** Approval workflows and compliance checks

### Metadata Model
Each catalog item includes:
- **Owner Groups:** Role-based ownership
- **SLA Definitions:** Clear fulfillment expectations
- **Tags:** Discoverability and categorization
- **Dependencies:** Service relationships
- **Documentation:** Inline help and external links

## Future Enhancements

### Planned Features
1. **Workflow Automation:** Full API-driven provisioning
2. **Service Dependencies:** Automatic dependency mapping
3. **Cost Tracking:** Resource cost estimation
4. **Usage Analytics:** Catalog item popularity and trends
5. **Template Versioning:** Multiple versions of templates
6. **Custom Workflows:** Persona-specific approval chains
7. **Integration Hub:** Third-party tool integrations
8. **AI Assistant:** Intelligent catalog recommendations

### Integration Roadmap
- **Phase 1:** GitLab CI/CD integration (Complete)
- **Phase 2:** HashiCorp Vault integration
- **Phase 3:** Kubernetes API integration
- **Phase 4:** Prometheus/Grafana integration
- **Phase 5:** Backstage.io compatibility layer

## Technical Details

### File Structure
```
2b7117ce872c8b9410ab877e0ebb3538/update/
├── sc_catalog_idp_service_catalog.xml
├── sc_category_application_services.xml
├── sc_category_infrastructure_services.xml
├── sc_category_cicd_services.xml
├── sc_category_security_compliance.xml
├── sc_category_observability_services.xml
├── sc_cat_item_service_onboarding.xml
├── sc_cat_item_new_microservice.xml
├── sc_cat_item_pipeline_provisioning.xml
├── sc_cat_item_secrets_provisioning.xml
├── sc_cat_item_monitoring_dashboard.xml
├── sp_page_service_catalog.xml
├── sp_widget_service_catalog_content.xml
├── sp_instance_service_catalog_widget.xml
├── sp_container_service_catalog_container.xml
├── sp_row_service_catalog_row.xml
└── sp_column_service_catalog_column.xml
```

### System IDs Reference
- **Catalog:** `c1c1c1c1c1c1c1c1c1c1c1c1c1c1c1c1`
- **Categories:** `d1d1...` through `a6a6...`
- **Items:** `b1b1...` through `b5b5...`
- **Page:** `a1a1a1a1a1a1a1a1a1a1a1a1a1a1a1a1`
- **Widget:** `c7c7c7c7c7c7c7c7c7c7c7c7c7c7c7c7`

## Support and Documentation

### Getting Help
- **Knowledge Base:** `/kb`
- **Support Portal:** `/support`
- **Documentation:** `/documentation`
- **API Docs:** `/api-catalog`

### Contact
- **Platform Team:** platform-team@company.com
- **Security Team:** security-team@company.com
- **SRE Team:** sre-team@company.com

---

**Document Version:** 1.0  
**Last Updated:** 2026-05-02  
**Maintained By:** IDPBob Platform Team