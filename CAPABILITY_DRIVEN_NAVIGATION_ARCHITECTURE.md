# Capability-Driven Navigation Engine Architecture

## Executive Summary

This document describes the implementation of a **4-layer abstraction model** for dynamic navigation in the IDPBob Internal Developer Portal, replacing static role-to-menu mapping with a scalable, capability-driven architecture.

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    USER AUTHENTICATION                       │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│  LAYER 1: ROLES (Who the user is)                           │
│  - Application Developer                                     │
│  - Platform Engineer                                         │
│  - SRE                                                       │
│  - Security Engineer                                         │
│  - Product Owner                                             │
│  - ServiceNow Admin                                          │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│  LAYER 2: CAPABILITIES (What the user can do)               │
│  - deploy_service                                            │
│  - view_incidents                                            │
│  - manage_infrastructure                                     │
│  - configure_pipelines                                       │
│  - manage_secrets                                            │
│  - view_observability                                        │
│  - create_service_catalog_item                               │
│  - manage_compliance                                         │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│  LAYER 3: TAXONOMY (How features are grouped)               │
│  - Developer Tools                                           │
│  - Platform Operations                                       │
│  - Reliability & Monitoring                                  │
│  - Security & Compliance                                     │
│  - Product & Demand Management                               │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│  LAYER 4: UI NAVIGATION (Dynamic rendering)                 │
│  - Service Catalog                                           │
│  - API Explorer                                              │
│  - CI/CD Pipelines                                           │
│  - Monitoring Dashboards                                     │
│  - Security Dashboard                                        │
│  - Compliance Reports                                        │
└─────────────────────────────────────────────────────────────┘
```

## Data Model

### Table 1: u_idp_capability
**Purpose:** Define all capabilities in the system

| Field | Type | Description |
|-------|------|-------------|
| capability_id | String (100) | Unique identifier (e.g., "deploy_service") |
| name | String (255) | Display name |
| description | String (1000) | Detailed description |
| active | Boolean | Enable/disable capability |
| icon | String (50) | Icon for UI rendering |
| api_endpoint | String (255) | Associated API endpoint (optional) |

**Example Records:**
```javascript
{
  capability_id: "deploy_service",
  name: "Deploy Service",
  description: "Ability to deploy services to Kubernetes",
  active: true,
  icon: "rocket"
}
```

### Table 2: u_role_capability_map
**Purpose:** Map roles to capabilities (many-to-many)

| Field | Type | Description |
|-------|------|-------------|
| role | Reference (sys_user_role) | ServiceNow role |
| capability | Reference (u_idp_capability) | Capability |
| active | Boolean | Enable/disable mapping |

**Example Mappings:**
```
Application Developer → deploy_service
Application Developer → view_incidents
Application Developer → create_service_catalog_item
Platform Engineer → deploy_service
Platform Engineer → manage_infrastructure
Platform Engineer → configure_pipelines
SRE → view_incidents
SRE → view_observability
SRE → manage_infrastructure
Security Engineer → manage_secrets
Security Engineer → manage_compliance
```

### Table 3: u_capability_taxonomy_map
**Purpose:** Map capabilities to taxonomy nodes (many-to-many)

| Field | Type | Description |
|-------|------|-------------|
| capability | Reference (u_idp_capability) | Capability |
| taxonomy_node | Choice | Taxonomy category |
| order | Integer | Display order |
| active | Boolean | Enable/disable mapping |

**Taxonomy Node Choices:**
- Developer Tools
- Platform Operations
- Reliability & Monitoring
- Security & Compliance
- Product & Demand Management

**Example Mappings:**
```
deploy_service → Developer Tools
deploy_service → Platform Operations
configure_pipelines → Developer Tools
view_observability → Reliability & Monitoring
manage_secrets → Security & Compliance
```

### Table 4: u_navigation_item (Extended)
**Purpose:** Define navigation items with capability requirements

| Field | Type | Description |
|-------|------|-------------|
| name | String | Navigation item name |
| url | String | Target URL |
| icon | String | Icon |
| taxonomy_node | Choice | Taxonomy category |
| required_capabilities | List (u_idp_capability) | Required capabilities |
| order | Integer | Display order |
| active | Boolean | Enable/disable |

## Core Business Logic: IDPNavigationEngine

### Script Include: IDPNavigationEngine

```javascript
var IDPNavigationEngine = Class.create();
IDPNavigationEngine.prototype = {
    initialize: function() {
        this.userId = gs.getUserID();
        this.userRoles = [];
        this.capabilities = [];
        this.taxonomyNodes = [];
        this.navigationTree = {};
    },

    /**
     * Main entry point - Get navigation for current user
     * @returns {Object} Navigation tree grouped by taxonomy
     */
    getNavigation: function() {
        this._fetchUserRoles();
        this._resolveCapabilities();
        this._resolveTaxonomy();
        this._buildNavigationTree();
        return this.navigationTree;
    },

    /**
     * Get user roles
     * @private
     */
    _fetchUserRoles: function() {
        var gr = new GlideRecord('sys_user_has_role');
        gr.addQuery('user', this.userId);
        gr.query();
        
        while (gr.next()) {
            this.userRoles.push(gr.role.toString());
        }
    },

    /**
     * Resolve capabilities from roles
     * @private
     */
    _resolveCapabilities: function() {
        if (this.userRoles.length === 0) return;
        
        var gr = new GlideRecord('x_ibmin_idpbob_u_role_capability_map');
        gr.addQuery('role', 'IN', this.userRoles.join(','));
        gr.addQuery('active', true);
        gr.query();
        
        var capabilityIds = [];
        while (gr.next()) {
            var capId = gr.capability.toString();
            if (capabilityIds.indexOf(capId) === -1) {
                capabilityIds.push(capId);
            }
        }
        
        // Get capability details
        if (capabilityIds.length > 0) {
            var grCap = new GlideRecord('x_ibmin_idpbob_u_idp_capability');
            grCap.addQuery('sys_id', 'IN', capabilityIds.join(','));
            grCap.addQuery('active', true);
            grCap.query();
            
            while (grCap.next()) {
                this.capabilities.push({
                    id: grCap.sys_id.toString(),
                    capability_id: grCap.capability_id.toString(),
                    name: grCap.name.toString(),
                    icon: grCap.icon.toString()
                });
            }
        }
    },

    /**
     * Resolve taxonomy nodes from capabilities
     * @private
     */
    _resolveTaxonomy: function() {
        if (this.capabilities.length === 0) return;
        
        var capIds = this.capabilities.map(function(c) { return c.id; });
        
        var gr = new GlideRecord('x_ibmin_idpbob_u_capability_taxonomy_map');
        gr.addQuery('capability', 'IN', capIds.join(','));
        gr.addQuery('active', true);
        gr.orderBy('order');
        gr.query();
        
        while (gr.next()) {
            var node = gr.taxonomy_node.toString();
            if (this.taxonomyNodes.indexOf(node) === -1) {
                this.taxonomyNodes.push(node);
            }
        }
    },

    /**
     * Build navigation tree grouped by taxonomy
     * @private
     */
    _buildNavigationTree: function() {
        if (this.taxonomyNodes.length === 0) return;
        
        // Initialize taxonomy nodes
        for (var i = 0; i < this.taxonomyNodes.length; i++) {
            this.navigationTree[this.taxonomyNodes[i]] = [];
        }
        
        // Get navigation items
        var capIds = this.capabilities.map(function(c) { return c.id; });
        
        var gr = new GlideRecord('x_ibmin_idpbob_u_navigation_item');
        gr.addQuery('active', true);
        gr.orderBy('order');
        gr.query();
        
        while (gr.next()) {
            var requiredCaps = gr.required_capabilities.toString().split(',');
            var hasAccess = this._checkCapabilityAccess(requiredCaps, capIds);
            
            if (hasAccess) {
                var taxonomyNode = gr.taxonomy_node.toString();
                if (this.navigationTree[taxonomyNode]) {
                    this.navigationTree[taxonomyNode].push({
                        name: gr.name.toString(),
                        url: gr.url.toString(),
                        icon: gr.icon.toString(),
                        description: gr.description.toString()
                    });
                }
            }
        }
        
        // Remove empty taxonomy nodes
        for (var node in this.navigationTree) {
            if (this.navigationTree[node].length === 0) {
                delete this.navigationTree[node];
            }
        }
    },

    /**
     * Check if user has required capabilities
     * @private
     */
    _checkCapabilityAccess: function(requiredCaps, userCaps) {
        if (!requiredCaps || requiredCaps.length === 0) return true;
        
        for (var i = 0; i < requiredCaps.length; i++) {
            if (requiredCaps[i] && userCaps.indexOf(requiredCaps[i]) === -1) {
                return false;
            }
        }
        return true;
    },

    /**
     * Check if user has specific capability
     * @param {String} capabilityId - Capability ID to check
     * @returns {Boolean}
     */
    hasCapability: function(capabilityId) {
        for (var i = 0; i < this.capabilities.length; i++) {
            if (this.capabilities[i].capability_id === capabilityId) {
                return true;
            }
        }
        return false;
    },

    /**
     * Get all user capabilities
     * @returns {Array}
     */
    getUserCapabilities: function() {
        if (this.capabilities.length === 0) {
            this._fetchUserRoles();
            this._resolveCapabilities();
        }
        return this.capabilities;
    },

    type: 'IDPNavigationEngine'
};
```

## Service Portal Integration

### Updated Home Widget (sp_widget_idp_home_content)

**Server Script:**
```javascript
(function() {
    // Use navigation engine
    var navEngine = new global.IDPNavigationEngine();
    data.navigation = navEngine.getNavigation();
    data.capabilities = navEngine.getUserCapabilities();
    
    // User info
    data.user = gs.getUser();
    data.userName = data.user.getDisplayName();
    
    // Statistics (capability-filtered)
    data.stats = {
        activeServices: 127,
        apiEndpoints: 342,
        deployments: 89,
        incidents: 3
    };
})();
```

**Client Controller:**
```javascript
function($scope, $location) {
    var c = this;
    
    // Navigate based on taxonomy
    c.navigateToTaxonomy = function(taxonomyNode) {
        var items = c.data.navigation[taxonomyNode];
        if (items && items.length > 0) {
            $location.path(items[0].url);
        }
    };
    
    // Navigate to specific item
    c.navigateTo = function(url) {
        $location.path(url);
    };
    
    // Check capability
    c.hasCapability = function(capabilityId) {
        return c.data.capabilities.some(function(cap) {
            return cap.capability_id === capabilityId;
        });
    };
}
```

**HTML Template:**
```html
<div class="idp-home-container">
    <!-- Dynamic Navigation by Taxonomy -->
    <div class="taxonomy-sections">
        <div ng-repeat="(taxonomy, items) in data.navigation" class="taxonomy-section">
            <h2>{{taxonomy}}</h2>
            <div class="navigation-grid">
                <div ng-repeat="item in items" 
                     class="nav-card" 
                     ng-click="c.navigateTo(item.url)">
                    <i class="fa fa-{{item.icon}}"></i>
                    <h3>{{item.name}}</h3>
                    <p>{{item.description}}</p>
                </div>
            </div>
        </div>
    </div>
</div>
```

## Security Enforcement

### ACL Strategy

**Table-Level ACLs:**
```javascript
// u_idp_capability - Read
// Condition: gs.hasRole('x_ibmin_idpbob.servicenow_admin')

// u_role_capability_map - Read
// Condition: gs.hasRole('x_ibmin_idpbob.servicenow_admin')

// u_capability_taxonomy_map - Read
// Condition: gs.hasRole('x_ibmin_idpbob.servicenow_admin')
```

**Capability-Based ACLs:**
```javascript
// Example: Deploy Service ACL
var navEngine = new global.IDPNavigationEngine();
answer = navEngine.hasCapability('deploy_service');
```

### API Enforcement

**REST API Example:**
```javascript
(function process(/*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
    var navEngine = new global.IDPNavigationEngine();
    
    if (!navEngine.hasCapability('deploy_service')) {
        response.setStatus(403);
        response.setBody({error: 'Insufficient capabilities'});
        return;
    }
    
    // Process request
})(request, response);
```

## Capability Definitions

### Core Capabilities

| Capability ID | Name | Description | Taxonomy Nodes |
|---------------|------|-------------|----------------|
| deploy_service | Deploy Service | Deploy services to Kubernetes | Developer Tools, Platform Operations |
| view_incidents | View Incidents | View and manage incidents | Reliability & Monitoring |
| manage_infrastructure | Manage Infrastructure | Provision and manage infrastructure | Platform Operations |
| configure_pipelines | Configure Pipelines | Create and manage CI/CD pipelines | Developer Tools |
| manage_secrets | Manage Secrets | Manage secrets and credentials | Security & Compliance |
| view_observability | View Observability | Access monitoring dashboards | Reliability & Monitoring |
| create_service_catalog_item | Create Catalog Item | Request services from catalog | Developer Tools |
| manage_compliance | Manage Compliance | View compliance reports | Security & Compliance |
| manage_risk | Manage Risk | Assess and manage risks | Security & Compliance |
| view_product_metrics | View Product Metrics | Access product analytics | Product & Demand Management |
| manage_platform_config | Manage Platform Config | Configure platform settings | Platform Operations |
| view_api_catalog | View API Catalog | Browse API documentation | Developer Tools |

## Role-to-Capability Mapping

### Application Developer
- deploy_service
- view_incidents
- create_service_catalog_item
- configure_pipelines
- view_api_catalog

### Platform Engineer
- deploy_service
- manage_infrastructure
- configure_pipelines
- manage_platform_config
- view_observability

### SRE
- view_incidents
- view_observability
- manage_infrastructure
- deploy_service

### Security Engineer
- manage_secrets
- manage_compliance
- manage_risk
- view_observability

### Product Owner
- view_product_metrics
- view_incidents
- create_service_catalog_item

### ServiceNow Admin
- ALL CAPABILITIES

## Extensibility

### AI-Driven Navigation (Future)
```javascript
// Recommendation engine based on usage patterns
var AINavigationEngine = Class.create();
AINavigationEngine.prototype = Object.extendsObject(IDPNavigationEngine, {
    getRecommendations: function() {
        // Analyze user behavior
        // Suggest relevant capabilities
        // Return personalized navigation
    }
});
```

### GitLab CI/CD Integration
```javascript
// Trigger pipelines based on capabilities
if (navEngine.hasCapability('configure_pipelines')) {
    // Allow pipeline configuration
    gitlabAPI.createPipeline(config);
}
```

### Port.io-Style Service Discovery
```javascript
// Dynamic service catalog based on capabilities
var services = serviceDiscovery.getServices({
    capabilities: navEngine.getUserCapabilities(),
    taxonomy: 'Developer Tools'
});
```

## Testing Strategy

### Unit Tests
```javascript
// Test capability resolution
var navEngine = new IDPNavigationEngine();
navEngine.userId = 'test_user_id';
var capabilities = navEngine.getUserCapabilities();
assertEqual(capabilities.length > 0, true);
```

### Integration Tests
```javascript
// Test navigation tree generation
var navEngine = new IDPNavigationEngine();
var navigation = navEngine.getNavigation();
assertNotNull(navigation['Developer Tools']);
```

## Migration Path

### Phase 1: Parallel Run
- Keep existing static navigation
- Run capability engine in parallel
- Compare results

### Phase 2: Gradual Rollout
- Enable for ServiceNow Admins
- Enable for Platform Engineers
- Enable for all users

### Phase 3: Full Cutover
- Remove static navigation
- Capability engine becomes primary

## Performance Considerations

### Caching Strategy
```javascript
// Cache navigation for session
var cacheKey = 'nav_' + gs.getUserID();
var cached = gs.getSession().getClientData(cacheKey);
if (cached) {
    return JSON.parse(cached);
}
// Generate and cache
gs.getSession().putClientData(cacheKey, JSON.stringify(navigation));
```

### Query Optimization
- Index on capability_id
- Index on role field in mapping tables
- Use GlideAggregate for counts

## Monitoring & Analytics

### Metrics to Track
- Navigation generation time
- Capability resolution time
- Most used capabilities
- Unused capabilities
- Navigation patterns by role

### Dashboards
- Capability usage heatmap
- Navigation performance metrics
- Role-capability coverage matrix

---

**Document Version:** 1.0  
**Last Updated:** 2026-05-02  
**Architecture Owner:** IDPBob Platform Team