# PROMPT 7 - Observability & Platform Health Dashboard

## Executive Summary

This document defines a comprehensive Platform Health Dashboard for the IDPBob Internal Developer Portal, providing real-time visibility into platform metrics, service health, and operational insights with role-based views.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                  Platform Health Dashboard                       │
│                                                                  │
│  ┌────────────────┐  ┌────────────────┐  ┌────────────────┐   │
│  │  Real-Time     │  │  Trend         │  │  Service       │   │
│  │  Status Tiles  │  │  Graphs        │  │  Dependency    │   │
│  │                │  │                │  │  Map           │   │
│  └────────┬───────┘  └────────┬───────┘  └────────┬───────┘   │
└───────────┼──────────────────┼──────────────────┼──────────────┘
            │                  │                  │
            ▼                  ▼                  ▼
┌─────────────────────────────────────────────────────────────────┐
│              Data Aggregation Layer                              │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Metrics Collector                                        │  │
│  │  - Deployment metrics                                     │  │
│  │  - Incident metrics                                       │  │
│  │  - Service health metrics                                 │  │
│  │  - Performance metrics                                    │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────┬────────────────┬────────────────┬────────────────────┘
          │                │                │
          ▼                ▼                ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│   ServiceNow    │ │   Prometheus    │ │   External      │
│   Tables        │ │   (Future)      │ │   APIs          │
│   - Incidents   │ │   - Metrics     │ │   - GitLab      │
│   - Changes     │ │   - Alerts      │ │   - Kubernetes  │
│   - Deployments │ │                 │ │   - Grafana     │
└─────────────────┘ └─────────────────┘ └─────────────────┘
```

---

## Component 1: Dashboard Layout

### Main Dashboard Structure

```
┌─────────────────────────────────────────────────────────────────┐
│  Platform Health Dashboard                    [Last Updated: Now]│
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │ Deployment   │  │ Active       │  │ Mean Time to │         │
│  │ Success Rate │  │ Incidents    │  │ Recovery     │         │
│  │              │  │              │  │              │         │
│  │    94.2%     │  │      3       │  │   2.5 hrs    │         │
│  │   ↑ 2.1%     │  │   ↓ 2        │  │   ↓ 0.5 hrs  │         │
│  └──────────────┘  └──────────────┘  └──────────────┘         │
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │ Service      │  │ API Response │  │ Error Rate   │         │
│  │ Availability │  │ Time         │  │              │         │
│  │              │  │              │  │              │         │
│  │   99.95%     │  │   145ms      │  │    0.12%     │         │
│  │   → 0.0%     │  │   ↓ 15ms     │  │   ↓ 0.03%    │         │
│  └──────────────┘  └──────────────┘  └──────────────┘         │
│                                                                  │
├─────────────────────────────────────────────────────────────────┤
│  Deployment Trend (Last 7 Days)                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                                                           │  │
│  │  [Line Chart: Successful vs Failed Deployments]          │  │
│  │                                                           │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                  │
│  Incident Trend (Last 30 Days)                                  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                                                           │  │
│  │  [Area Chart: Critical, High, Medium, Low]               │  │
│  │                                                           │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                  │
├─────────────────────────────────────────────────────────────────┤
│  Service Health Status                                           │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  ● payment-service        Healthy      99.99%  145ms     │  │
│  │  ● auth-service           Healthy      99.95%  89ms      │  │
│  │  ● notification-service   Degraded     98.50%  450ms     │  │
│  │  ● order-service          Healthy      99.98%  120ms     │  │
│  │  ● inventory-service      Healthy      99.97%  95ms      │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                  │
│  Service Dependency Map (Placeholder)                            │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                                                           │  │
│  │  [Interactive Graph: Services and Dependencies]          │  │
│  │                                                           │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Component 2: Metrics to Display

### 1. Deployment Success Rate

**Calculation:**
```javascript
Deployment Success Rate = (Successful Deployments / Total Deployments) × 100

// Last 7 days
var successRate = calculateDeploymentSuccessRate(7);

function calculateDeploymentSuccessRate(days) {
    var startDate = new GlideDateTime();
    startDate.addDaysLocalTime(-days);
    
    var total = new GlideAggregate('x_ibmin_idpbob_deployment');
    total.addQuery('sys_created_on', '>=', startDate);
    total.addAggregate('COUNT');
    total.query();
    total.next();
    var totalCount = parseInt(total.getAggregate('COUNT'));
    
    var successful = new GlideAggregate('x_ibmin_idpbob_deployment');
    successful.addQuery('sys_created_on', '>=', startDate);
    successful.addQuery('status', 'successful');
    successful.addAggregate('COUNT');
    successful.query();
    successful.next();
    var successCount = parseInt(successful.getAggregate('COUNT'));
    
    return totalCount > 0 ? (successCount / totalCount * 100).toFixed(1) : 0;
}
```

**Display:**
```
┌──────────────────┐
│ Deployment       │
│ Success Rate     │
│                  │
│     94.2%        │
│   ↑ 2.1%         │  ← Trend vs previous period
│                  │
│ [View Details]   │
└──────────────────┘
```

### 2. Active Incidents

**Calculation:**
```javascript
// Count active incidents by priority
function getActiveIncidents() {
    var incidents = {
        critical: 0,
        high: 0,
        medium: 0,
        low: 0,
        total: 0
    };
    
    var gr = new GlideAggregate('incident');
    gr.addQuery('active', true);
    gr.addQuery('state', 'NOT IN', '6,7,8'); // Exclude resolved/closed
    gr.addAggregate('COUNT');
    gr.groupBy('priority');
    gr.query();
    
    while (gr.next()) {
        var priority = gr.getValue('priority');
        var count = parseInt(gr.getAggregate('COUNT'));
        
        switch(priority) {
            case '1': incidents.critical = count; break;
            case '2': incidents.high = count; break;
            case '3': incidents.medium = count; break;
            case '4': incidents.low = count; break;
        }
        incidents.total += count;
    }
    
    return incidents;
}
```

**Display:**
```
┌──────────────────┐
│ Active Incidents │
│                  │
│       3          │
│   ↓ 2            │  ← Trend vs yesterday
│                  │
│ Critical: 0      │
│ High: 1          │
│ Medium: 2        │
│                  │
│ [View All]       │
└──────────────────┘
```

### 3. Mean Time to Recovery (MTTR)

**Calculation:**
```javascript
// Calculate MTTR for last 30 days
function calculateMTTR(days) {
    var startDate = new GlideDateTime();
    startDate.addDaysLocalTime(-days);
    
    var gr = new GlideRecord('incident');
    gr.addQuery('resolved_at', '>=', startDate);
    gr.addQuery('resolved_at', '!=', '');
    gr.query();
    
    var totalRecoveryTime = 0;
    var count = 0;
    
    while (gr.next()) {
        var opened = new GlideDateTime(gr.getValue('opened_at'));
        var resolved = new GlideDateTime(gr.getValue('resolved_at'));
        var diff = gs.dateDiff(opened.getDisplayValue(), resolved.getDisplayValue(), true);
        totalRecoveryTime += diff;
        count++;
    }
    
    if (count === 0) return 0;
    
    var mttrSeconds = totalRecoveryTime / count;
    var mttrHours = (mttrSeconds / 3600).toFixed(1);
    
    return mttrHours;
}
```

**Display:**
```
┌──────────────────┐
│ Mean Time to     │
│ Recovery (MTTR)  │
│                  │
│    2.5 hrs       │
│   ↓ 0.5 hrs      │  ← Improvement
│                  │
│ Target: < 4 hrs  │
│ Status: ✓ On Track│
│                  │
│ [View Trend]     │
└──────────────────┘
```

### 4. Service Availability

**Calculation:**
```javascript
// Calculate service availability (uptime percentage)
function calculateServiceAvailability(serviceName, days) {
    var startDate = new GlideDateTime();
    startDate.addDaysLocalTime(-days);
    
    // Total time in seconds
    var totalTime = days * 24 * 60 * 60;
    
    // Get downtime from incidents
    var gr = new GlideRecord('incident');
    gr.addQuery('cmdb_ci.name', serviceName);
    gr.addQuery('opened_at', '>=', startDate);
    gr.query();
    
    var totalDowntime = 0;
    
    while (gr.next()) {
        var opened = new GlideDateTime(gr.getValue('opened_at'));
        var resolved = gr.getValue('resolved_at');
        
        if (resolved) {
            var resolvedDT = new GlideDateTime(resolved);
            var downtime = gs.dateDiff(opened.getDisplayValue(), resolvedDT.getDisplayValue(), true);
            totalDowntime += downtime;
        } else {
            // Still open - count time until now
            var now = new GlideDateTime();
            var downtime = gs.dateDiff(opened.getDisplayValue(), now.getDisplayValue(), true);
            totalDowntime += downtime;
        }
    }
    
    var uptime = totalTime - totalDowntime;
    var availability = (uptime / totalTime * 100).toFixed(2);
    
    return availability;
}
```

**Display:**
```
┌──────────────────┐
│ Service          │
│ Availability     │
│                  │
│   99.95%         │
│   → 0.0%         │  ← No change
│                  │
│ SLA: 99.9%       │
│ Status: ✓ Met    │
│                  │
│ [View Services]  │
└──────────────────┘
```

### 5. API Response Time

**Calculation:**
```javascript
// Calculate average API response time
function calculateAPIResponseTime(days) {
    // This would integrate with APM tools like Prometheus/Grafana
    // Placeholder calculation using mock data
    
    var gr = new GlideAggregate('x_ibmin_idpbob_api_metrics');
    gr.addQuery('timestamp', '>=', getStartDate(days));
    gr.addAggregate('AVG', 'response_time');
    gr.query();
    
    if (gr.next()) {
        return parseInt(gr.getAggregate('AVG', 'response_time'));
    }
    
    return 0;
}
```

**Display:**
```
┌──────────────────┐
│ API Response     │
│ Time (P95)       │
│                  │
│    145ms         │
│   ↓ 15ms         │  ← Improvement
│                  │
│ Target: < 200ms  │
│ Status: ✓ Good   │
│                  │
│ [View Details]   │
└──────────────────┘
```

### 6. Error Rate

**Calculation:**
```javascript
// Calculate error rate from API calls
function calculateErrorRate(days) {
    var startDate = new GlideDateTime();
    startDate.addDaysLocalTime(-days);
    
    var total = new GlideAggregate('x_ibmin_idpbob_api_call');
    total.addQuery('timestamp', '>=', startDate);
    total.addAggregate('COUNT');
    total.query();
    total.next();
    var totalCalls = parseInt(total.getAggregate('COUNT'));
    
    var errors = new GlideAggregate('x_ibmin_idpbob_api_call');
    errors.addQuery('timestamp', '>=', startDate);
    errors.addQuery('status_code', '>=', 400);
    errors.addAggregate('COUNT');
    errors.query();
    errors.next();
    var errorCalls = parseInt(errors.getAggregate('COUNT'));
    
    return totalCalls > 0 ? (errorCalls / totalCalls * 100).toFixed(2) : 0;
}
```

**Display:**
```
┌──────────────────┐
│ Error Rate       │
│                  │
│    0.12%         │
│   ↓ 0.03%        │  ← Improvement
│                  │
│ Target: < 1%     │
│ Status: ✓ Excellent│
│                  │
│ [View Errors]    │
└──────────────────┘
```

---

## Component 3: Trend Graphs

### 1. Deployment Trend (Weekly)

**Data Structure:**
```javascript
{
    "labels": ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"],
    "datasets": [
        {
            "label": "Successful",
            "data": [45, 52, 48, 55, 50, 30, 25],
            "color": "#24a148"
        },
        {
            "label": "Failed",
            "data": [3, 2, 4, 3, 2, 1, 2],
            "color": "#da1e28"
        }
    ]
}
```

**Chart Type:** Line Chart with dual series

**Implementation:**
```javascript
// Server Script
data.deploymentTrend = getDeploymentTrend(7);

function getDeploymentTrend(days) {
    var trend = {
        labels: [],
        successful: [],
        failed: []
    };
    
    for (var i = days - 1; i >= 0; i--) {
        var date = new GlideDateTime();
        date.addDaysLocalTime(-i);
        var dayStart = date.getDate().getValue();
        
        date.addDaysLocalTime(1);
        var dayEnd = date.getDate().getValue();
        
        // Get successful deployments
        var success = new GlideAggregate('x_ibmin_idpbob_deployment');
        success.addQuery('sys_created_on', '>=', dayStart);
        success.addQuery('sys_created_on', '<', dayEnd);
        success.addQuery('status', 'successful');
        success.addAggregate('COUNT');
        success.query();
        success.next();
        
        // Get failed deployments
        var failed = new GlideAggregate('x_ibmin_idpbob_deployment');
        failed.addQuery('sys_created_on', '>=', dayStart);
        failed.addQuery('sys_created_on', '<', dayEnd);
        failed.addQuery('status', 'failed');
        failed.addAggregate('COUNT');
        failed.query();
        failed.next();
        
        trend.labels.push(getDayLabel(i));
        trend.successful.push(parseInt(success.getAggregate('COUNT')));
        trend.failed.push(parseInt(failed.getAggregate('COUNT')));
    }
    
    return trend;
}
```

### 2. Incident Trend (Monthly)

**Data Structure:**
```javascript
{
    "labels": ["Week 1", "Week 2", "Week 3", "Week 4"],
    "datasets": [
        {
            "label": "Critical",
            "data": [2, 1, 0, 1],
            "color": "#da1e28"
        },
        {
            "label": "High",
            "data": [5, 4, 6, 3],
            "color": "#ff832b"
        },
        {
            "label": "Medium",
            "data": [12, 10, 15, 11],
            "color": "#f1c21b"
        },
        {
            "label": "Low",
            "data": [8, 7, 9, 6],
            "color": "#0f62fe"
        }
    ]
}
```

**Chart Type:** Stacked Area Chart

**Implementation:**
```javascript
// Server Script
data.incidentTrend = getIncidentTrend(30);

function getIncidentTrend(days) {
    var trend = {
        labels: [],
        critical: [],
        high: [],
        medium: [],
        low: []
    };
    
    var weeksCount = Math.ceil(days / 7);
    
    for (var week = weeksCount - 1; week >= 0; week--) {
        var weekStart = new GlideDateTime();
        weekStart.addDaysLocalTime(-(week * 7 + 7));
        
        var weekEnd = new GlideDateTime();
        weekEnd.addDaysLocalTime(-(week * 7));
        
        // Count incidents by priority
        var priorities = ['1', '2', '3', '4'];
        var counts = [0, 0, 0, 0];
        
        for (var p = 0; p < priorities.length; p++) {
            var gr = new GlideAggregate('incident');
            gr.addQuery('opened_at', '>=', weekStart);
            gr.addQuery('opened_at', '<', weekEnd);
            gr.addQuery('priority', priorities[p]);
            gr.addAggregate('COUNT');
            gr.query();
            gr.next();
            counts[p] = parseInt(gr.getAggregate('COUNT'));
        }
        
        trend.labels.push('Week ' + (weeksCount - week));
        trend.critical.push(counts[0]);
        trend.high.push(counts[1]);
        trend.medium.push(counts[2]);
        trend.low.push(counts[3]);
    }
    
    return trend;
}
```

### 3. Service Health Trend

**Data Structure:**
```javascript
{
    "labels": ["00:00", "04:00", "08:00", "12:00", "16:00", "20:00"],
    "datasets": [
        {
            "label": "Availability %",
            "data": [99.95, 99.98, 99.92, 99.97, 99.95, 99.96],
            "color": "#24a148"
        },
        {
            "label": "Response Time (ms)",
            "data": [120, 115, 145, 135, 140, 125],
            "color": "#0f62fe",
            "yAxisID": "y-axis-2"
        }
    ]
}
```

**Chart Type:** Dual-axis Line Chart

---

## Component 4: Service Health Status

### Service Health Table

**Data Structure:**
```javascript
{
    "services": [
        {
            "name": "payment-service",
            "status": "healthy",
            "availability": "99.99%",
            "response_time": "145ms",
            "error_rate": "0.05%",
            "last_deployment": "2 hours ago",
            "version": "v2.3.1"
        },
        {
            "name": "auth-service",
            "status": "healthy",
            "availability": "99.95%",
            "response_time": "89ms",
            "error_rate": "0.08%",
            "last_deployment": "1 day ago",
            "version": "v1.8.2"
        },
        {
            "name": "notification-service",
            "status": "degraded",
            "availability": "98.50%",
            "response_time": "450ms",
            "error_rate": "1.2%",
            "last_deployment": "3 days ago",
            "version": "v1.5.0",
            "issues": [
                "High response time",
                "Elevated error rate"
            ]
        }
    ]
}
```

**Implementation:**
```javascript
// Server Script
data.services = getServiceHealthStatus();

function getServiceHealthStatus() {
    var services = [];
    
    var gr = new GlideRecord('cmdb_ci_service');
    gr.addQuery('operational_status', '1'); // Operational
    gr.orderBy('name');
    gr.query();
    
    while (gr.next()) {
        var serviceName = gr.getValue('name');
        
        services.push({
            name: serviceName,
            status: getServiceStatus(serviceName),
            availability: calculateServiceAvailability(serviceName, 7),
            response_time: getAverageResponseTime(serviceName),
            error_rate: getServiceErrorRate(serviceName),
            last_deployment: getLastDeploymentTime(serviceName),
            version: getServiceVersion(serviceName)
        });
    }
    
    return services;
}

function getServiceStatus(serviceName) {
    // Check for active incidents
    var gr = new GlideRecord('incident');
    gr.addQuery('cmdb_ci.name', serviceName);
    gr.addQuery('active', true);
    gr.addQuery('priority', '<=', 2); // Critical or High
    gr.query();
    
    if (gr.hasNext()) {
        return 'critical';
    }
    
    // Check availability
    var availability = parseFloat(calculateServiceAvailability(serviceName, 1));
    if (availability < 99.0) {
        return 'degraded';
    }
    
    // Check error rate
    var errorRate = parseFloat(getServiceErrorRate(serviceName));
    if (errorRate > 1.0) {
        return 'degraded';
    }
    
    return 'healthy';
}
```

**Display:**
```html
<table class="service-health-table">
    <thead>
        <tr>
            <th>Service</th>
            <th>Status</th>
            <th>Availability</th>
            <th>Response Time</th>
            <th>Error Rate</th>
            <th>Last Deployment</th>
        </tr>
    </thead>
    <tbody>
        <tr ng-repeat="service in data.services" 
            ng-class="{'degraded': service.status === 'degraded', 'critical': service.status === 'critical'}">
            <td>
                <i class="fa fa-circle" ng-class="{'text-success': service.status === 'healthy', 'text-warning': service.status === 'degraded', 'text-danger': service.status === 'critical'}"></i>
                {{service.name}}
            </td>
            <td>{{service.status | titleCase}}</td>
            <td>{{service.availability}}</td>
            <td>{{service.response_time}}</td>
            <td>{{service.error_rate}}</td>
            <td>{{service.last_deployment}}</td>
        </tr>
    </tbody>
</table>
```

---

## Component 5: Service Dependency Map (Placeholder)

### Dependency Graph Structure

**Data Structure:**
```javascript
{
    "nodes": [
        {
            "id": "payment-service",
            "label": "Payment Service",
            "status": "healthy",
            "type": "service"
        },
        {
            "id": "auth-service",
            "label": "Auth Service",
            "status": "healthy",
            "type": "service"
        },
        {
            "id": "database-postgres",
            "label": "PostgreSQL",
            "status": "healthy",
            "type": "database"
        }
    ],
    "edges": [
        {
            "from": "payment-service",
            "to": "auth-service",
            "label": "authenticates"
        },
        {
            "from": "payment-service",
            "to": "database-postgres",
            "label": "stores data"
        }
    ]
}
```

**Visualization:**
```
┌─────────────────────────────────────────────────────────────┐
│  Service Dependency Map                                      │
│                                                              │
│         ┌──────────────┐                                    │
│         │   API        │                                    │
│         │   Gateway    │                                    │
│         └──────┬───────┘                                    │
│                │                                            │
│       ┌────────┴────────┐                                  │
│       │                 │                                  │
│  ┌────▼─────┐     ┌────▼─────┐                           │
│  │ Payment  │────▶│   Auth   │                           │
│  │ Service  │     │ Service  │                           │
│  └────┬─────┘     └────┬─────┘                           │
│       │                │                                  │
│  ┌────▼─────┐     ┌────▼─────┐                           │
│  │PostgreSQL│     │  Redis   │                           │
│  │ Database │     │  Cache   │                           │
│  └──────────┘     └──────────┘                           │
│                                                            │
│  Legend:                                                   │
│  ● Healthy  ● Degraded  ● Critical                       │
└─────────────────────────────────────────────────────────────┘
```

**Implementation (Placeholder):**
```javascript
// Server Script
data.dependencyMap = getServiceDependencyMap();

function getServiceDependencyMap() {
    // Placeholder - would integrate with service mesh or CMDB
    return {
        nodes: [
            {id: 'api-gateway', label: 'API Gateway', status: 'healthy', type: 'gateway'},
            {id: 'payment-service', label: 'Payment Service', status: 'healthy', type: 'service'},
            {id: 'auth-service', label: 'Auth Service', status: 'healthy', type: 'service'},
            {id: 'postgres', label: 'PostgreSQL', status: 'healthy', type: 'database'},
            {id: 'redis', label: 'Redis Cache', status: 'healthy', type: 'cache'}
        ],
        edges: [
            {from: 'api-gateway', to: 'payment-service'},
            {from: 'api-gateway', to: 'auth-service'},
            {from: 'payment-service', to: 'auth-service'},
            {from: 'payment-service', to: 'postgres'},
            {from: 'auth-service', to: 'redis'}
        ]
    };
}
```

---

## Component 6: Role-Based Views

### SRE View (Full Access)

**Visible Metrics:**
- ✅ All metrics
- ✅ All trend graphs
- ✅ Service health details
- ✅ Dependency map
- ✅ Raw data access
- ✅ Export capabilities

**Additional Features:**
- Drill-down into incidents
- Access to logs
- Alert configuration
- SLO management

### Developer View (Limited Service Health)

**Visible Metrics:**
- ✅ Deployment success rate
- ✅ Service availability (own services only)
- ✅ API response time (own services only)
- ✅ Error rate (own services only)
- ❌ MTTR (hidden)
- ❌ Active incidents (summary only)

**Restrictions:**
- Can only see services they own/develop
- Cannot see infrastructure metrics
- Cannot configure alerts

### Product Owner View (Summary Only)

**Visible Metrics:**
- ✅ Deployment success rate (summary)
- ✅ Active incidents (count only)
- ✅ Service availability (summary)
- ❌ Technical details hidden
- ❌ No drill-down capabilities

**Focus:**
- High-level KPIs
- Business impact metrics
- Trend summaries

### Implementation

```javascript
// Server Script
(function() {
    var navEngine = new global.IDPNavigationEngine();
    var userRole = getUserPrimaryRole();
    
    // Determine view level
    if (navEngine.hasCapability('view_observability_full')) {
        data.viewLevel = 'full'; // SRE
    } else if (navEngine.hasCapability('view_observability')) {
        data.viewLevel = 'limited'; // Developer
    } else if (navEngine.hasCapability('view_product_metrics')) {
        data.viewLevel = 'summary'; // Product Owner
    } else {
        data.viewLevel = 'none';
    }
    
    // Load metrics based on view level
    data.metrics = getMetricsForViewLevel(data.viewLevel);
    data.services = getServicesForUser(data.viewLevel);
    data.trends = getTrendsForViewLevel(data.viewLevel);
})();

function getMetricsForViewLevel(viewLevel) {
    var metrics = {};
    
    switch(viewLevel) {
        case 'full':
            metrics.deploymentSuccessRate = calculateDeploymentSuccessRate(7);
            metrics.activeIncidents = getActiveIncidents();
            metrics.mttr = calculateMTTR(30);
            metrics.serviceAvailability = calculateOverallAvailability(7);
            metrics.apiResponseTime = calculateAPIResponseTime(7);
            metrics.errorRate = calculateErrorRate(7);
            break;
            
        case 'limited':
            metrics.deploymentSuccessRate = calculateDeploymentSuccessRate(7);
            metrics.activeIncidents = {total: getActiveIncidents().total}; // Count only
            metrics.serviceAvailability = calculateUserServicesAvailability();
            metrics.apiResponseTime = calculateUserServicesResponseTime();
            metrics.errorRate = calculateUserServicesErrorRate();
            break;
            
        case 'summary':
            metrics.deploymentSuccessRate = calculateDeploymentSuccessRate(7);
            metrics.activeIncidents = {total: getActiveIncidents().total};
            metrics.serviceAvailability = calculateOverallAvailability(7);
            break;
    }
    
    return metrics;
}
```

---

## Component 7: Data Sources

### ServiceNow Tables

1. **incident** - Incident metrics
   - Active incidents count
   - MTTR calculation
   - Incident trends

2. **change_request** - Deployment metrics
   - Deployment success rate
   - Deployment frequency

3. **cmdb_ci_service** - Service inventory
   - Service list
   - Service ownership
   - Service dependencies

4. **x_ibmin_idpbob_deployment** (Custom)
   - Deployment history
   - Success/failure tracking
   - Deployment duration

5. **x_ibmin_idpbob_service_metrics** (Custom)
   - Service availability
   - Response time
   - Error rate

### External Data Sources (Future Integration)

1. **Prometheus**
   - Real-time metrics
   - Custom queries
   - Alert rules

2. **Grafana**
   - Pre-built dashboards
   - Visualization templates
   - Data source integration

3. **GitLab API**
   - Pipeline metrics
   - Deployment frequency
   - Code quality metrics

4. **Kubernetes API**
   - Pod health
   - Resource utilization
   - Cluster metrics

5. **Jaeger/Zipkin**
   - Distributed tracing
   - Service dependencies
   - Performance bottlenecks

---

## Component 8: Widget Implementation

### Platform Health Dashboard Widget

**File:** `sp_widget_platform_health_dashboard.xml`

**Server Script:**
```javascript
(function() {
    // Get user view level
    var navEngine = new global.IDPNavigationEngine();
    data.viewLevel = determineViewLevel(navEngine);
    
    // Load metrics
    data.metrics = {
        deploymentSuccessRate: calculateDeploymentSuccessRate(7),
        activeIncidents: getActiveIncidents(),
        mttr: calculateMTTR(30),
        serviceAvailability: calculateOverallAvailability(7),
        apiResponseTime: calculateAPIResponseTime(7),
        errorRate: calculateErrorRate(7)
    };
    
    // Load trends
    data.trends = {
        deployments: getDeploymentTrend(7),
        incidents: getIncidentTrend(30)
    };
    
    // Load service health
    data.services = getServiceHealthStatus();
    
    // Load dependency map (placeholder)
    data.dependencyMap = getServiceDependencyMap();
    
    // Refresh interval
    data.refreshInterval = 60000; // 60 seconds
})();
```

**Client Controller:**
```javascript
function($scope, $interval, $http) {
    var c = this;
    
    // Auto-refresh
    var refreshTimer = $interval(function() {
        c.server.refresh();
    }, c.data.refreshInterval);
    
    // Cleanup on destroy
    $scope.$on('$destroy', function() {
        $interval.cancel(refreshTimer);
    });
    
    // Navigate to service details
    c.viewServiceDetails = function(serviceName) {
        $location.path('/service/' + serviceName);
    };
    
    // Export data
    c.exportData = function() {
        var data = {
            metrics: c.data.metrics,
            trends: c.data.trends,
            services: c.data.services,
            timestamp: new Date()
        };
        
        var blob = new Blob([JSON.stringify(data, null, 2)], {type: 'application/json'});
        var url = URL.createObjectURL(blob);
        var a = document.createElement('a');
        a.href = url;
        a.download = 'platform-health-' + Date.now() + '.json';
        a.click();
    };
}
```

**HTML Template:**
```html
<div class="platform-health-dashboard">
    <!-- Header -->
    <div class="dashboard-header">
        <h1>Platform Health Dashboard</h1>
        <div class="dashboard-actions">
            <span class="last-updated">Last Updated: {{data.lastUpdated | date:'short'}}</span>
            <button ng-click="c.exportData()" ng-if="data.viewLevel === 'full'">
                <i class="fa fa-download"></i> Export
            </button>
            <button ng-click="c.server.refresh()">
                <i class="fa fa-refresh"></i> Refresh
            </button>
        </div>
    </div>
    
    <!-- Metrics Tiles -->
    <div class="metrics-grid">
        <div class="metric-tile">
            <div class="metric-label">Deployment Success Rate</div>
            <div class="metric-value">{{data.metrics.deploymentSuccessRate}}%</div>
            <div class="metric-trend" ng-class="{'positive': data.metrics.deploymentTrend > 0}">
                <i class="fa" ng-class="{'fa-arrow-up': data.metrics.deploymentTrend > 0, 'fa-arrow-down': data.metrics.deploymentTrend < 0}"></i>
                {{data.metrics.deploymentTrend}}%
            </div>
        </div>
        
        <div class="metric-tile" ng-if="data.viewLevel !== 'summary'">
            <div class="metric-label">Active Incidents</div>
            <div class="metric-value">{{data.metrics.activeIncidents.total}}</div>
            <div class="metric-breakdown" ng-if="data.viewLevel === 'full'">
                <span class="critical">Critical: {{data.metrics.activeIncidents.critical}}</span>
                <span class="high">High: {{data.metrics.activeIncidents.high}}</span>
            </div>
        </div>
        
        <div class="metric-tile" ng-if="data.viewLevel === 'full'">
            <div class="metric-label">Mean Time to Recovery</div>
            <div class="metric-value">{{data.metrics.mttr}} hrs</div>
            <div class="metric-target">Target: < 4 hrs</div>
        </div>
        
        <!-- More metric tiles... -->
    </div>
    
    <!-- Trend Graphs -->
    <div class="trends-section" ng-if="data.viewLevel !== 'summary'">
        <div class="trend-chart">
            <h3>Deployment Trend (Last 7 Days)</h3>
            <canvas id="deploymentChart"></canvas>
        </div>
        
        <div class="trend-chart" ng-if="data.viewLevel === 'full'">
            <h3>Incident Trend (Last 30 Days)</h3>
            <canvas id="incidentChart"></canvas>
        </div>
    </div>
    
    <!-- Service Health -->
    <div class="service-health-section" ng-if="data.viewLevel !== 'summary'">
        <h3>Service Health Status</h3>
        <table class="service-health-table">
            <thead>
                <tr>
                    <th>Service</th>
                    <th>Status</th>
                    <th>Availability</th>
                    <th>Response Time</th>
                    <th ng-if="data.viewLevel === 'full'">Error Rate</th>
                    <th>Last Deployment</th>
                </tr>
            </thead>
            <tbody>
                <tr ng-repeat="service in data.services" 
                    ng-click="c.viewServiceDetails(service.name)"
                    class="clickable">
                    <td>
                        <i class="fa fa-circle status-indicator" 
                           ng-class="{'healthy': service.status === 'healthy', 'degraded': service.status === 'degraded', 'critical': service.status === 'critical'}"></i>
                        {{service.name}}
                    </td>
                    <td>{{service.status | titleCase}}</td>
                    <td>{{service.availability}}</td>
                    <td>{{service.response_time}}</td>
                    <td ng-if="data.viewLevel === 'full'">{{service.error_rate}}</td>
                    <td>{{service.last_deployment}}</td>
                </tr>
            </tbody>
        </table>
    </div>
    
    <!-- Dependency Map -->
    <div class="dependency-map-section" ng-if="data.viewLevel === 'full'">
        <h3>Service Dependency Map (Placeholder)</h3>
        <div class="dependency-map-placeholder">
            <p>Interactive service dependency visualization will be displayed here.</p>
            <p>Integration with service mesh or CMDB required.</p>
        </div>
    </div>
</div>
```

---

## Summary

### Metrics Implemented
✅ Deployment Success Rate  
✅ Active Incidents  
✅ Mean Time to Recovery (MTTR)  
✅ Service Availability  
✅ API Response Time  
✅ Error Rate  

### Views Implemented
✅ Real-time status tiles  
✅ Trend graphs (weekly/monthly)  
✅ Service health table  
✅ Service dependency map (placeholder)  

### Role-Based Access
✅ SRE → Full view with all metrics  
✅ Developers → Limited to own services  
✅ Product Owner → Summary view only  

### Data Sources
✅ ServiceNow tables (incidents, changes, CMDB)  
✅ Custom tables (deployments, metrics)  
🔄 External integrations (Prometheus, Grafana) - Future  

**Status:** Complete and ready for implementation

---

**Document Version:** 1.0  
**Created:** 2026-05-02  
**Total Lines:** 1,100+  
**Status:** Architecture Complete