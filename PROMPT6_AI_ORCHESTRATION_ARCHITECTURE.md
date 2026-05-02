# PROMPT 6 - AI Orchestration Layer (Future Ready)

## Executive Summary

This document defines a comprehensive AI Orchestration Layer for the IDPBob Internal Developer Portal, designed to integrate with IBM Bob AI, ServiceNow Now Assist, and external LLMs. The architecture is **implementation-ready** with API contracts, event-driven integration models, and placeholder components.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                    IDP Portal (User Interface)                   │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │ Chat Widget  │  │ Suggestions  │  │ Auto-Actions │          │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘          │
└─────────┼──────────────────┼──────────────────┼──────────────────┘
          │                  │                  │
          ▼                  ▼                  ▼
┌─────────────────────────────────────────────────────────────────┐
│              AI Orchestration Layer (Middleware)                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  AI Request Router & Context Manager                      │  │
│  │  - Intent classification                                  │  │
│  │  - Context enrichment (user, role, history)              │  │
│  │  │  - Request validation & rate limiting                  │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  API Abstraction Layer                                    │  │
│  │  - /ai/ask        - General Q&A                          │  │
│  │  - /ai/recommend  - Recommendations                       │  │
│  │  - /ai/summarize  - Content summarization                │  │
│  │  - /ai/generate   - Code/config generation               │  │
│  │  - /ai/analyze    - Root cause analysis                  │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Event-Driven Integration                                 │  │
│  │  - Business Rules (triggers)                              │  │
│  │  - Event Queue (async processing)                         │  │
│  │  - Webhook handlers                                       │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────┬────────────────┬────────────────┬────────────────────┘
          │                │                │
          ▼                ▼                ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│   IBM Bob AI    │ │ ServiceNow Now  │ │  External LLMs  │
│   Integration   │ │ Assist / GenAI  │ │ (OpenAI, etc.)  │
└─────────────────┘ └─────────────────┘ └─────────────────┘
```

---

## Component 1: Platform AI Assistant Widget

### Widget Specification

**File:** `sp_widget_platform_ai_assistant.xml`

**Features:**
- Chat interface with conversation history
- Quick action buttons (Generate, Summarize, Recommend)
- Context-aware suggestions
- Code snippet rendering
- Markdown support

**Widget Structure:**
```javascript
// Server Script
(function() {
    data.aiEnabled = gs.getProperty('x_ibmin_idpbob.ai_enabled', 'false') === 'true';
    data.aiProvider = gs.getProperty('x_ibmin_idpbob.ai_provider', 'placeholder');
    
    data.quickActions = [
        {
            id: 'generate_request',
            label: 'Generate Service Request',
            icon: 'magic',
            prompt: 'Help me create a service request for...'
        },
        {
            id: 'suggest_pipeline',
            label: 'Suggest CI/CD Pipeline',
            icon: 'git-branch',
            prompt: 'Suggest a CI/CD pipeline configuration for...'
        },
        {
            id: 'summarize_incident',
            label: 'Summarize Incidents',
            icon: 'list',
            prompt: 'Summarize recent incidents related to...'
        },
        {
            id: 'recommend_fix',
            label: 'Recommend Security Fix',
            icon: 'shield',
            prompt: 'Recommend security fixes for...'
        }
    ];
    
    // Get conversation history
    data.conversationHistory = [];
    var gr = new GlideRecord('x_ibmin_idpbob_ai_conversation');
    gr.addQuery('user', gs.getUserID());
    gr.orderByDesc('sys_created_on');
    gr.setLimit(10);
    gr.query();
    
    while (gr.next()) {
        data.conversationHistory.push({
            id: gr.getUniqueValue(),
            question: gr.getValue('question'),
            answer: gr.getValue('answer'),
            timestamp: gr.getValue('sys_created_on')
        });
    }
})();

// Client Controller
function($scope, $http, spModal) {
    var c = this;
    
    c.messages = [];
    c.userInput = '';
    c.isProcessing = false;
    
    // Send message to AI
    c.sendMessage = function() {
        if (!c.userInput || c.isProcessing) return;
        
        var userMessage = c.userInput;
        c.messages.push({
            type: 'user',
            content: userMessage,
            timestamp: new Date()
        });
        
        c.userInput = '';
        c.isProcessing = true;
        
        // Call AI API
        $http.post('/api/x_ibmin_idpbob/ai/ask', {
            question: userMessage,
            context: c.getContext()
        }).then(function(response) {
            c.messages.push({
                type: 'ai',
                content: response.data.answer,
                timestamp: new Date(),
                confidence: response.data.confidence
            });
            c.isProcessing = false;
        }).catch(function(error) {
            c.messages.push({
                type: 'error',
                content: 'Sorry, I encountered an error. Please try again.',
                timestamp: new Date()
            });
            c.isProcessing = false;
        });
    };
    
    // Quick action
    c.executeQuickAction = function(action) {
        c.userInput = action.prompt;
        c.sendMessage();
    };
    
    // Get context for AI
    c.getContext = function() {
        return {
            user_id: c.data.userId,
            user_role: c.data.userRole,
            current_page: window.location.pathname,
            recent_actions: c.getRecentActions()
        };
    };
}
```

**HTML Template:**
```html
<div class="ai-assistant-widget">
    <!-- Header -->
    <div class="ai-header">
        <i class="fa fa-robot"></i>
        <h3>Platform AI Assistant</h3>
        <span class="ai-status" ng-class="{'active': data.aiEnabled}">
            {{data.aiEnabled ? 'Active' : 'Placeholder Mode'}}
        </span>
    </div>
    
    <!-- Quick Actions -->
    <div class="ai-quick-actions">
        <button ng-repeat="action in data.quickActions" 
                ng-click="c.executeQuickAction(action)"
                class="quick-action-btn">
            <i class="fa fa-{{action.icon}}"></i>
            {{action.label}}
        </button>
    </div>
    
    <!-- Chat Messages -->
    <div class="ai-messages">
        <div ng-repeat="msg in c.messages" 
             class="ai-message" 
             ng-class="msg.type">
            <div class="message-content" ng-bind-html="msg.content | markdown"></div>
            <div class="message-meta">
                <span class="timestamp">{{msg.timestamp | date:'short'}}</span>
                <span class="confidence" ng-if="msg.confidence">
                    Confidence: {{msg.confidence}}%
                </span>
            </div>
        </div>
        
        <!-- Processing Indicator -->
        <div ng-if="c.isProcessing" class="ai-processing">
            <i class="fa fa-spinner fa-spin"></i> Thinking...
        </div>
    </div>
    
    <!-- Input -->
    <div class="ai-input">
        <textarea ng-model="c.userInput" 
                  placeholder="Ask me anything about the platform..."
                  ng-keypress="$event.keyCode === 13 && !$event.shiftKey && c.sendMessage()"
                  ng-disabled="c.isProcessing"></textarea>
        <button ng-click="c.sendMessage()" 
                ng-disabled="!c.userInput || c.isProcessing">
            <i class="fa fa-paper-plane"></i>
        </button>
    </div>
</div>
```

---

## Component 2: AI Capabilities (Design Only)

### 1. Generate Service Requests

**Capability:** AI analyzes user intent and generates pre-filled service catalog requests

**Example Interaction:**
```
User: "I need to deploy a new Python microservice with PostgreSQL database"

AI Response:
"I'll help you create a service request. Based on your requirements:

1. New Microservice Creation
   - Technology Stack: Python
   - Database: PostgreSQL
   - Template: Python FastAPI

2. Database Provisioning
   - Type: PostgreSQL 14
   - Size: 10GB
   - Backup: Daily

Would you like me to submit these requests?"

[Generate Requests] [Modify] [Cancel]
```

**Implementation Hook:**
```javascript
// AI generates structured data
{
    "intent": "create_microservice",
    "catalog_items": [
        {
            "item_id": "b2b2b2b2b2b2b2b2b2b2b2b2b2b2b2b2",
            "variables": {
                "service_name": "payment-service",
                "technology_stack": "python",
                "database_type": "postgresql"
            }
        }
    ],
    "confidence": 0.92
}
```

### 2. Suggest CI/CD Pipelines

**Capability:** AI recommends pipeline configurations based on service type and requirements

**Example Interaction:**
```
User: "What CI/CD pipeline should I use for a Node.js REST API?"

AI Response:
"For a Node.js REST API, I recommend:

Pipeline Stages:
1. Build
   - npm install
   - npm run build
   
2. Test
   - npm run test
   - npm run test:integration
   
3. Security Scan
   - npm audit
   - SAST with SonarQube
   
4. Deploy
   - Build Docker image
   - Push to registry
   - Deploy to Kubernetes

Would you like me to generate the .gitlab-ci.yml file?"

[Generate Pipeline] [Customize] [View Example]
```

**Generated Output:**
```yaml
# .gitlab-ci.yml (AI Generated)
stages:
  - build
  - test
  - scan
  - deploy

build:
  stage: build
  script:
    - npm install
    - npm run build
  artifacts:
    paths:
      - dist/

test:
  stage: test
  script:
    - npm run test
    - npm run test:integration
  coverage: '/Statements\s*:\s*(\d+\.\d+)%/'

security_scan:
  stage: scan
  script:
    - npm audit
    - sonar-scanner

deploy_dev:
  stage: deploy
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
    - kubectl apply -f k8s/dev/
  only:
    - develop
```

### 3. Summarize Incidents

**Capability:** AI analyzes and summarizes incidents with pattern detection

**Example Interaction:**
```
User: "Summarize incidents from the last 24 hours"

AI Response:
"Incident Summary (Last 24 Hours):

Total Incidents: 12
Critical: 2 | High: 4 | Medium: 6

Top Issues:
1. Database Connection Timeouts (5 incidents)
   - Affected Services: payment-service, order-service
   - Root Cause: Connection pool exhaustion
   - Recommendation: Increase pool size to 50

2. API Gateway 503 Errors (3 incidents)
   - Peak Time: 14:00-15:00 UTC
   - Root Cause: Rate limit exceeded
   - Recommendation: Implement request queuing

3. Memory Leaks (2 incidents)
   - Affected: notification-service
   - Pattern: Gradual memory increase over 6 hours
   - Recommendation: Review event listener cleanup

[View Details] [Create Action Items] [Export Report]
```

### 4. Recommend Security Fixes

**Capability:** AI analyzes security vulnerabilities and recommends fixes

**Example Interaction:**
```
User: "Review security scan results for payment-service"

AI Response:
"Security Analysis for payment-service:

Critical Vulnerabilities: 2
High: 5
Medium: 12

Priority Fixes:

1. CRITICAL: SQL Injection in payment endpoint
   File: src/controllers/payment.controller.js:45
   Issue: Unsanitized user input in SQL query
   Fix: Use parameterized queries
   
   Recommended Code:
   ```javascript
   // Before (Vulnerable)
   const query = `SELECT * FROM payments WHERE id = ${req.params.id}`;
   
   // After (Secure)
   const query = 'SELECT * FROM payments WHERE id = ?';
   db.query(query, [req.params.id]);
   ```

2. CRITICAL: Exposed API Keys
   File: config/production.js
   Issue: Hardcoded API keys in source code
   Fix: Use environment variables or secrets manager
   
   Action: Move to HashiCorp Vault
   [Create Secrets Request]

[Apply Fixes] [Create Tickets] [Schedule Review]
```

---

## Component 3: API Abstraction Layer

### API Contract Definitions

#### 1. /ai/ask - General Q&A

**Endpoint:** `POST /api/x_ibmin_idpbob/ai/ask`

**Request:**
```json
{
  "question": "How do I deploy a new service?",
  "context": {
    "user_id": "user123",
    "user_role": "application_developer",
    "current_page": "/developer",
    "recent_actions": [
      {"action": "viewed_catalog", "timestamp": "2026-05-02T07:00:00Z"},
      {"action": "searched", "query": "deploy", "timestamp": "2026-05-02T07:05:00Z"}
    ]
  },
  "conversation_id": "conv_abc123",
  "options": {
    "include_sources": true,
    "max_tokens": 500
  }
}
```

**Response:**
```json
{
  "answer": "To deploy a new service, follow these steps:\n\n1. Create a service request...",
  "confidence": 0.95,
  "sources": [
    {
      "type": "knowledge_article",
      "id": "KB0001234",
      "title": "Service Deployment Guide",
      "url": "/kb?id=KB0001234"
    },
    {
      "type": "catalog_item",
      "id": "b2b2b2b2b2b2b2b2b2b2b2b2b2b2b2b2",
      "title": "New Microservice Creation",
      "url": "/sc_cat_item?sys_id=b2b2b2b2b2b2b2b2b2b2b2b2b2b2b2b2"
    }
  ],
  "suggested_actions": [
    {
      "label": "Create Service Request",
      "action": "open_catalog_item",
      "params": {"item_id": "b2b2b2b2b2b2b2b2b2b2b2b2b2b2b2b2"}
    }
  ],
  "conversation_id": "conv_abc123",
  "timestamp": "2026-05-02T07:10:00Z"
}
```

#### 2. /ai/recommend - Recommendations

**Endpoint:** `POST /api/x_ibmin_idpbob/ai/recommend`

**Request:**
```json
{
  "type": "pipeline_config",
  "context": {
    "service_type": "rest_api",
    "technology": "nodejs",
    "deployment_target": "kubernetes",
    "requirements": ["testing", "security_scan", "auto_deploy"]
  }
}
```

**Response:**
```json
{
  "recommendations": [
    {
      "id": "rec_001",
      "title": "Standard Node.js CI/CD Pipeline",
      "description": "Multi-stage pipeline with testing, security scanning, and automated deployment",
      "confidence": 0.92,
      "template": {
        "type": "gitlab_ci",
        "content": "# .gitlab-ci.yml\nstages:\n  - build\n  - test\n..."
      },
      "benefits": [
        "Automated testing reduces bugs by 60%",
        "Security scanning catches vulnerabilities early",
        "Automated deployment reduces deployment time by 80%"
      ],
      "estimated_setup_time": "30 minutes"
    }
  ],
  "alternatives": [
    {
      "id": "rec_002",
      "title": "Simplified Pipeline (No Security Scan)",
      "confidence": 0.75
    }
  ]
}
```

#### 3. /ai/summarize - Content Summarization

**Endpoint:** `POST /api/x_ibmin_idpbob/ai/summarize`

**Request:**
```json
{
  "type": "incidents",
  "filters": {
    "time_range": "24h",
    "severity": ["critical", "high"],
    "services": ["payment-service", "order-service"]
  },
  "format": "executive_summary"
}
```

**Response:**
```json
{
  "summary": {
    "overview": "12 incidents in last 24 hours, 2 critical, 4 high priority",
    "key_findings": [
      "Database connection pool exhaustion affecting 5 services",
      "API Gateway rate limiting causing 503 errors during peak hours",
      "Memory leak in notification-service requiring restart every 6 hours"
    ],
    "recommendations": [
      {
        "priority": "critical",
        "action": "Increase database connection pool size to 50",
        "impact": "Resolves 5 recurring incidents",
        "effort": "Low (configuration change)"
      },
      {
        "priority": "high",
        "action": "Implement request queuing in API Gateway",
        "impact": "Prevents 503 errors during traffic spikes",
        "effort": "Medium (2-3 days)"
      }
    ],
    "trends": {
      "increasing": ["database_timeouts", "memory_usage"],
      "decreasing": ["api_errors"]
    }
  },
  "detailed_report_url": "/reports/incident_summary_20260502",
  "generated_at": "2026-05-02T07:15:00Z"
}
```

#### 4. /ai/generate - Code/Config Generation

**Endpoint:** `POST /api/x_ibmin_idpbob/ai/generate`

**Request:**
```json
{
  "type": "kubernetes_manifest",
  "specifications": {
    "service_name": "payment-service",
    "image": "registry.example.com/payment-service:v1.0.0",
    "replicas": 3,
    "resources": {
      "cpu": "500m",
      "memory": "512Mi"
    },
    "environment": "production",
    "features": ["health_checks", "auto_scaling", "service_mesh"]
  }
}
```

**Response:**
```json
{
  "generated_code": {
    "deployment.yaml": "apiVersion: apps/v1\nkind: Deployment\n...",
    "service.yaml": "apiVersion: v1\nkind: Service\n...",
    "hpa.yaml": "apiVersion: autoscaling/v2\nkind: HorizontalPodAutoscaler\n..."
  },
  "explanation": "Generated Kubernetes manifests with:\n- 3 replicas for high availability\n- Resource limits to prevent resource exhaustion\n- Horizontal Pod Autoscaler for traffic spikes\n- Istio sidecar injection for service mesh",
  "validation": {
    "status": "passed",
    "warnings": [
      "Consider adding PodDisruptionBudget for production resilience"
    ]
  },
  "next_steps": [
    "Review generated manifests",
    "Apply to cluster: kubectl apply -f .",
    "Verify deployment: kubectl get pods -l app=payment-service"
  ]
}
```

#### 5. /ai/analyze - Root Cause Analysis

**Endpoint:** `POST /api/x_ibmin_idpbob/ai/analyze`

**Request:**
```json
{
  "type": "deployment_failure",
  "incident_id": "INC0012345",
  "logs": [
    "2026-05-02 07:00:00 ERROR: Failed to pull image",
    "2026-05-02 07:00:05 ERROR: ImagePullBackOff",
    "2026-05-02 07:00:10 ERROR: Back-off pulling image"
  ],
  "context": {
    "service": "payment-service",
    "environment": "production",
    "deployment_time": "2026-05-02T07:00:00Z"
  }
}
```

**Response:**
```json
{
  "root_cause": {
    "category": "image_registry_access",
    "description": "Unable to pull Docker image from registry due to authentication failure",
    "confidence": 0.94,
    "evidence": [
      "ImagePullBackOff error indicates registry access issue",
      "No network connectivity errors found",
      "Image exists in registry (verified)"
    ]
  },
  "resolution_steps": [
    {
      "step": 1,
      "action": "Verify image pull secret exists",
      "command": "kubectl get secret regcred -n production"
    },
    {
      "step": 2,
      "action": "Check secret expiration",
      "command": "kubectl get secret regcred -n production -o jsonpath='{.data.\\.dockerconfigjson}' | base64 -d"
    },
    {
      "step": 3,
      "action": "Recreate image pull secret if expired",
      "command": "kubectl create secret docker-registry regcred --docker-server=registry.example.com --docker-username=user --docker-password=pass"
    }
  ],
  "prevention": [
    "Implement secret rotation automation",
    "Add monitoring for secret expiration",
    "Use managed identity for registry access"
  ],
  "similar_incidents": [
    {
      "id": "INC0011234",
      "date": "2026-04-15",
      "resolution": "Recreated expired image pull secret"
    }
  ]
}
```

---

## Component 4: Event-Driven Integration Model

### Event Hooks

#### 1. Incident Created → AI Summary Trigger

**Business Rule:** `AI Incident Summary Generator`

```javascript
(function executeRule(current, previous /*null when async*/) {
    // Trigger only for critical/high incidents
    if (current.priority <= 2) {
        var aiOrchestrator = new global.AIOrchestrator();
        
        // Queue AI summary generation
        aiOrchestrator.queueTask({
            type: 'summarize_incident',
            incident_id: current.getUniqueValue(),
            priority: 'high',
            callback: 'updateIncidentWithSummary'
        });
    }
})(current, previous);
```

**AI Processing:**
```javascript
// AIOrchestrator.processIncidentSummary()
{
    "incident_id": "INC0012345",
    "ai_summary": "Database connection timeout affecting payment processing. Similar to 3 recent incidents. Root cause: Connection pool exhaustion during peak traffic.",
    "suggested_actions": [
        "Increase connection pool size",
        "Implement connection retry logic",
        "Add connection pool monitoring"
    ],
    "related_incidents": ["INC0012234", "INC0012123", "INC0011999"],
    "confidence": 0.89
}
```

#### 2. Deployment Failure → Root Cause Suggestion

**Business Rule:** `AI Deployment Failure Analyzer`

```javascript
(function executeRule(current, previous /*null when async*/) {
    // Trigger on deployment failure
    if (current.state == 'failed') {
        var aiOrchestrator = new global.AIOrchestrator();
        
        // Collect deployment logs
        var logs = getDeploymentLogs(current.deployment_id);
        
        // Queue AI analysis
        aiOrchestrator.queueTask({
            type: 'analyze_deployment_failure',
            deployment_id: current.getUniqueValue(),
            logs: logs,
            priority: 'critical',
            callback: 'suggestDeploymentFix'
        });
    }
})(current, previous);
```

**AI Processing:**
```javascript
// AIOrchestrator.analyzeDeploymentFailure()
{
    "deployment_id": "deploy_12345",
    "root_cause": {
        "category": "configuration_error",
        "description": "Invalid environment variable format in deployment manifest",
        "confidence": 0.96
    },
    "fix_suggestion": {
        "type": "manifest_update",
        "changes": [
            {
                "file": "deployment.yaml",
                "line": 45,
                "current": "value: ${DATABASE_URL}",
                "suggested": "value: \"$(DATABASE_URL)\""
            }
        ]
    },
    "auto_fix_available": true
}
```

#### 3. Security Scan Complete → Vulnerability Summary

**Business Rule:** `AI Security Scan Analyzer`

```javascript
(function executeRule(current, previous /*null when async*/) {
    // Trigger on scan completion
    if (current.state == 'completed' && current.vulnerabilities_found > 0) {
        var aiOrchestrator = new global.AIOrchestrator();
        
        aiOrchestrator.queueTask({
            type: 'analyze_security_scan',
            scan_id: current.getUniqueValue(),
            priority: current.critical_count > 0 ? 'critical' : 'normal',
            callback: 'generateSecurityReport'
        });
    }
})(current, previous);
```

#### 4. Service Request Submitted → Auto-Enrichment

**Business Rule:** `AI Service Request Enricher`

```javascript
(function executeRule(current, previous /*null when async*/) {
    var aiOrchestrator = new global.AIOrchestrator();
    
    // Enrich request with AI suggestions
    aiOrchestrator.queueTask({
        type: 'enrich_service_request',
        request_id: current.getUniqueValue(),
        request_type: current.cat_item.toString(),
        priority: 'low',
        callback: 'addAISuggestions'
    });
})(current, previous);
```

**AI Processing:**
```javascript
// AIOrchestrator.enrichServiceRequest()
{
    "request_id": "REQ0012345",
    "enrichments": {
        "suggested_configurations": {
            "replicas": 3,
            "resources": {"cpu": "500m", "memory": "512Mi"},
            "rationale": "Based on similar services, these settings provide optimal performance"
        },
        "related_services": [
            {
                "name": "authentication-service",
                "reason": "Your service will likely need authentication"
            }
        ],
        "estimated_cost": "$45/month",
        "estimated_setup_time": "2 hours"
    }
}
```

### Event Queue Architecture

```javascript
// Table: x_ibmin_idpbob_ai_task_queue
{
    "task_id": "task_12345",
    "type": "summarize_incident",
    "priority": "high",
    "status": "pending",
    "payload": {
        "incident_id": "INC0012345"
    },
    "callback": "updateIncidentWithSummary",
    "created_at": "2026-05-02T07:00:00Z",
    "scheduled_at": "2026-05-02T07:00:00Z",
    "started_at": null,
    "completed_at": null,
    "retry_count": 0,
    "max_retries": 3
}
```

**Task Processor (Scheduled Job):**
```javascript
// Runs every minute
var AITaskProcessor = Class.create();
AITaskProcessor.prototype = {
    process: function() {
        var gr = new GlideRecord('x_ibmin_idpbob_ai_task_queue');
        gr.addQuery('status', 'pending');
        gr.addQuery('scheduled_at', '<=', new GlideDateTime());
        gr.orderBy('priority');
        gr.setLimit(10);
        gr.query();
        
        while (gr.next()) {
            this.processTask(gr);
        }
    },
    
    processTask: function(task) {
        task.status = 'processing';
        task.started_at = new GlideDateTime();
        task.update();
        
        try {
            var aiOrchestrator = new global.AIOrchestrator();
            var result = aiOrchestrator.executeTask(task);
            
            // Execute callback
            if (task.callback) {
                this.executeCallback(task.callback, result);
            }
            
            task.status = 'completed';
            task.completed_at = new GlideDateTime();
            task.result = JSON.stringify(result);
            task.update();
            
        } catch (e) {
            task.retry_count = parseInt(task.retry_count) + 1;
            
            if (task.retry_count >= task.max_retries) {
                task.status = 'failed';
                task.error = e.message;
            } else {
                task.status = 'pending';
                task.scheduled_at = new GlideDateTime().addSeconds(60 * task.retry_count);
            }
            task.update();
        }
    }
};
```

---

## Component 5: AI Provider Integration

### Provider Abstraction

```javascript
var AIProvider = Class.create();
AIProvider.prototype = {
    initialize: function() {
        this.provider = gs.getProperty('x_ibmin_idpbob.ai_provider', 'placeholder');
        this.apiKey = gs.getProperty('x_ibmin_idpbob.ai_api_key', '');
        this.endpoint = gs.getProperty('x_ibmin_idpbob.ai_endpoint', '');
    },
    
    ask: function(question, context) {
        switch(this.provider) {
            case 'ibm_bob':
                return this._askIBMBob(question, context);
            case 'now_assist':
                return this._askNowAssist(question, context);
            case 'openai':
                return this._askOpenAI(question, context);
            default:
                return this._placeholder(question, context);
        }
    },
    
    _askIBMBob: function(question, context) {
        // IBM Bob AI integration
        var request = new sn_ws.RESTMessageV2();
        request.setEndpoint(this.endpoint + '/api/v1/ask');
        request.setHttpMethod('POST');
        request.setRequestHeader('Authorization', 'Bearer ' + this.apiKey);
        request.setRequestBody(JSON.stringify({
            question: question,
            context: context
        }));
        
        var response = request.execute();
        return JSON.parse(response.getBody());
    },
    
    _askNowAssist: function(question, context) {
        // ServiceNow Now Assist integration
        // Use ServiceNow's native GenAI capabilities
        var genAI = new sn_genai.GenAI();
        return genAI.ask(question, context);
    },
    
    _askOpenAI: function(question, context) {
        // OpenAI integration
        var request = new sn_ws.RESTMessageV2();
        request.setEndpoint('https://api.openai.com/v1/chat/completions');
        request.setHttpMethod('POST');
        request.setRequestHeader('Authorization', 'Bearer ' + this.apiKey);
        request.setRequestBody(JSON.stringify({
            model: 'gpt-4',
            messages: [
                {role: 'system', content: 'You are a helpful DevOps assistant.'},
                {role: 'user', content: question}
            ]
        }));
        
        var response = request.execute();
        var result = JSON.parse(response.getBody());
        return {
            answer: result.choices[0].message.content,
            confidence: 0.85
        };
    },
    
    _placeholder: function(question, context) {
        // Placeholder mode - returns canned responses
        return {
            answer: "This is a placeholder response. AI integration is not yet configured. To enable AI features, configure an AI provider in system properties.",
            confidence: 1.0,
            mode: 'placeholder'
        };
    }
};
```

---

## Database Tables

### 1. AI Conversation History

**Table:** `x_ibmin_idpbob_ai_conversation`

```javascript
{
    "user": "reference:sys_user",
    "question": "string:4000",
    "answer": "string:4000",
    "context": "string:4000",
    "confidence": "decimal",
    "provider": "string:50",
    "conversation_id": "string:100",
    "feedback": "choice:helpful,not_helpful,incorrect",
    "sys_created_on": "datetime"
}
```

### 2. AI Task Queue

**Table:** `x_ibmin_idpbob_ai_task_queue`

```javascript
{
    "task_id": "string:100",
    "type": "choice:summarize,analyze,generate,recommend",
    "priority": "choice:critical,high,normal,low",
    "status": "choice:pending,processing,completed,failed",
    "payload": "string:4000",
    "result": "string:4000",
    "callback": "string:255",
    "scheduled_at": "datetime",
    "started_at": "datetime",
    "completed_at": "datetime",
    "retry_count": "integer",
    "max_retries": "integer",
    "error": "string:1000"
}
```

### 3. AI Suggestions

**Table:** `x_ibmin_idpbob_ai_suggestion`

```javascript
{
    "target_table": "string:100",
    "target_record": "string:32",
    "suggestion_type": "choice:configuration,fix,optimization,alternative",
    "suggestion": "string:4000",
    "confidence": "decimal",
    "status": "choice:pending,accepted,rejected,applied",
    "applied_by": "reference:sys_user",
    "applied_at": "datetime"
}
```

---

## System Properties

```javascript
// AI Configuration
x_ibmin_idpbob.ai_enabled = false
x_ibmin_idpbob.ai_provider = placeholder  // ibm_bob, now_assist, openai, placeholder
x_ibmin_idpbob.ai_endpoint = https://ai.example.com
x_ibmin_idpbob.ai_api_key = [encrypted]
x_ibmin_idpbob.ai_model = gpt-4
x_ibmin_idpbob.ai_max_tokens = 1000
x_ibmin_idpbob.ai_temperature = 0.7
x_ibmin_idpbob.ai_timeout = 30

// Feature Flags
x_ibmin_idpbob.ai_auto_summarize_incidents = false
x_ibmin_idpbob.ai_auto_analyze_failures = false
x_ibmin_idpbob.ai_enrich_requests = false
x_ibmin_idpbob.ai_suggest_fixes = false
```

---

## Security & Compliance

### Data Privacy
- User conversations are stored with user consent
- Sensitive data is masked before sending to AI
- Conversation history can be deleted by user
- Admin can disable AI features per user/role

### Rate Limiting
```javascript
// Rate limit: 100 requests per user per hour
var rateLimiter = new global.RateLimiter();
if (!rateLimiter.checkLimit(userId, 'ai_requests', 100, 3600)) {
    return {error: 'Rate limit exceeded'};
}
```

### Audit Logging
- All AI requests are logged
- Responses are logged with confidence scores
- User feedback is tracked
- Provider API calls are audited

---

## Monitoring & Analytics

### Metrics to Track
- AI request volume
- Response time
- Confidence scores
- User feedback (helpful/not helpful)
- Provider API costs
- Error rates
- Task queue depth

### Dashboards
- AI Usage Dashboard
- AI Performance Metrics
- Cost Analysis
- User Satisfaction Scores

---

## Implementation Roadmap

### Phase 1: Foundation (Weeks 1-2)
- Create database tables
- Implement AI Orchestrator
- Build provider abstraction layer
- Create placeholder mode

### Phase 2: UI Components (Weeks 3-4)
- Build AI Assistant widget
- Implement chat interface
- Add quick actions
- Create conversation history

### Phase 3: Event Integration (Weeks 5-6)
- Implement business rules
- Create event queue
- Build task processor
- Add callbacks

### Phase 4: Provider Integration (Weeks 7-8)
- Integrate IBM Bob AI
- Integrate Now Assist
- Add OpenAI support
- Test and validate

### Phase 5: Production Rollout (Weeks 9-10)
- Beta testing with select users
- Performance optimization
- Security review
- Full production deployment

---

## Cost Estimation

### AI Provider Costs (Monthly)
- **IBM Bob AI:** $500-2000 (based on usage)
- **ServiceNow Now Assist:** Included with license
- **OpenAI GPT-4:** $0.03 per 1K tokens (~$300-1000/month)

### Infrastructure Costs
- **Task Queue Processing:** Minimal (existing infrastructure)
- **Storage:** ~10GB for conversation history (~$1/month)
- **API Gateway:** Minimal overhead

**Total Estimated Cost:** $500-3000/month depending on usage and provider

---

## Success Metrics

### Adoption Metrics
- % of users engaging with AI assistant
- Average questions per user per day
- Feature usage (generate, summarize, recommend, analyze)

### Quality Metrics
- Average confidence score
- User feedback ratio (helpful vs not helpful)
- Task completion rate
- Error rate

### Business Impact
- Time saved on incident analysis
- Reduction in deployment failures
- Increase in service request quality
- Developer productivity improvement

---

## Conclusion

The AI Orchestration Layer provides a **future-ready foundation** for integrating AI capabilities into the IDPBob Internal Developer Portal. The architecture is:

✅ **Provider-Agnostic** - Works with IBM Bob AI, Now Assist, OpenAI, or custom providers  
✅ **Event-Driven** - Automatic AI assistance triggered by platform events  
✅ **Scalable** - Queue-based processing handles high volumes  
✅ **Secure** - Multi-layer security with data privacy controls  
✅ **Extensible** - Easy to add new AI capabilities  

**Status:** Design complete, ready for implementation

---

**Document Version:** 1.0  
**Created:** 2026-05-02  
**Status:** Architecture Complete - Ready for Development