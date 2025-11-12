# Implementation Guide: Application Health Monitoring Agent

## Table of Contents

1. [Prerequisites Check](#prerequisites-check)
2. [Phase 1: Application Inventory](#phase-1-application-inventory)
3. [Phase 2: Health Check Endpoints Setup](#phase-2-health-check-endpoints-setup)
4. [Phase 3: Power Automate Monitoring Flows](#phase-3-power-automate-monitoring-flows)
5. [Phase 4: Agent Creation](#phase-4-agent-creation)
6. [Phase 5: Alert Logic Configuration](#phase-5-alert-logic-configuration)
7. [Phase 6: Integration with Existing Tools](#phase-6-integration-with-existing-tools)
8. [Phase 7: Testing](#phase-7-testing)
9. [Phase 8: Deployment](#phase-8-deployment)
10. [Phase 9: Monitoring & Optimization](#phase-9-monitoring--optimization)

**Estimated Total Time**: 10-14 hours

---

## Prerequisites Check

Before starting, verify you have:

- [ ] Reviewed [README.md](./README.md)
- [ ] Reviewed [connections-required.md](./connections-required.md)
- [ ] Microsoft Copilot Studio license
- [ ] Power Automate Premium license
- [ ] Access to application infrastructure
- [ ] Teams channel for alerts
- [ ] Completed Lab 01 and Lab 02

---

## Phase 1: Application Inventory

**Time**: 1-2 hours

### Step 1.1: Create Application Register

Document all applications to be monitored:

**Create SharePoint List**: "Application Inventory"

**Columns**:
```
- ApplicationID (Single line of text) - Unique identifier
- ApplicationName (Single line of text)
- Environment (Choice: Production, Staging, Development)
- Criticality (Choice: Critical, High, Medium, Low)
- HealthCheckURL (Hyperlink)
- ExpectedResponseTime (Number) - milliseconds
- HealthCheckInterval (Choice: 5min, 10min, 15min, 30min)
- PrimaryContact (Person)
- SecondaryContact (Person)
- AlertChannel (Single line of text) - Teams channel URL
- IsActive (Yes/No)
- Notes (Multiple lines of text)
```

**Sample Data**:

| ApplicationID | Name | Environment | Criticality | HealthCheckURL | ExpectedResponseTime | Interval |
|---------------|------|-------------|-------------|----------------|---------------------|----------|
| APP-001 | EGAT Employee Portal | Production | Critical | https://portal.egat.co.th/health | 2000 | 5min |
| APP-002 | Document Management | Production | High | https://docs.egat.co.th/api/health | 3000 | 10min |
| APP-003 | Project Tracking | Production | Medium | https://projects.egat.co.th/status | 5000 | 15min |

### Step 1.2: Categorize Applications

**By Criticality**:
- **Critical** (24/7 monitoring, 5-min intervals): User-facing production apps
- **High** (Business hours +, 10-min intervals): Internal tools with high usage
- **Medium** (Business hours, 15-min intervals): Support systems
- **Low** (Daily checks): Development/test environments

**By Technology Stack**:
- Web applications (HTTP/HTTPS health checks)
- API services (RESTful endpoints)
- Database servers (connection checks)
- Background services (custom endpoints)

### Step 1.3: Define Health Check Standards

**Standard Health Check Response** (recommend to development teams):

```json
{
  "status": "healthy",
  "timestamp": "2025-11-12T14:30:00Z",
  "version": "1.2.3",
  "components": {
    "database": "healthy",
    "cache": "healthy",
    "external_api": "degraded"
  },
  "metrics": {
    "response_time_ms": 145,
    "memory_usage_percent": 68,
    "cpu_usage_percent": 42,
    "active_connections": 45
  }
}
```

**HTTP Status Codes**:
- `200 OK`: Application healthy
- `503 Service Unavailable`: Application down
- `429 Too Many Requests`: Rate limited (degraded)
- `500 Internal Server Error`: Application error

**For applications without health endpoints**: Use homepage/login page (check for HTTP 200)

---

## Phase 2: Health Check Endpoints Setup

**Time**: 2-3 hours

### Step 2.1: Audit Existing Health Endpoints

For each application, verify:

**Test with curl/Postman**:
```bash
# Test health endpoint
curl -i -X GET https://portal.egat.co.th/health

# Expected response:
HTTP/1.1 200 OK
Content-Type: application/json
{
  "status": "healthy",
  "timestamp": "2025-11-12T14:30:00Z"
}
```

**Checklist per Application**:
- [ ] Health endpoint exists and responds
- [ ] Response time < expected threshold
- [ ] Returns valid JSON (if applicable)
- [ ] Accessible from Power Platform IP ranges
- [ ] No authentication required (or token available)

### Step 2.2: Create Health Endpoints for Apps Without Them

**For apps without health endpoints**, work with development teams to add:

**Option A: Simple HTTP Endpoint**
```csharp
// ASP.NET Core example
app.MapGet("/health", () => Results.Ok(new {
    status = "healthy",
    timestamp = DateTime.UtcNow
}));
```

**Option B: Azure App Service Built-in Health Check**
```
Portal → App Service → Health check
Enable: Yes
Path: /health
```

**Option C: Monitor Homepage** (last resort)
- URL: https://app.egat.co.th
- Success criteria: HTTP 200 + page contains "EGAT" text

### Step 2.3: Network Configuration

**Whitelist Power Platform IP Ranges**:

1. Download IP ranges: https://www.microsoft.com/download/details.aspx?id=56519
2. Extract IPs for your region (e.g., "PowerPlatformInfra.SoutheastAsia")
3. Configure firewall rules:
   ```
   Source: Power Platform IP ranges
   Destination: Your application servers
   Port: 443 (HTTPS)
   Action: Allow
   ```

**For On-Premise Applications**:
- Configure reverse proxy (if needed)
- Ensure SSL certificates valid
- Test connectivity from Power Automate (HTTP connector test)

---

## Phase 3: Power Automate Monitoring Flows

**Time**: 3-4 hours

### Step 3.1: Create Master Monitoring Flow

**Flow Name**: "Application Health Check - Master"

**Trigger**: Recurrence
```
Interval: 5 minutes
Start time: [Current date] 00:00:00
Time zone: (UTC+07:00) Bangkok, Hanoi, Jakarta
```

**Flow Structure**:

```
Trigger: Recurrence (every 5 minutes)
  ↓
Action 1: Get Application Inventory
  → SharePoint: Get items from "Application Inventory"
  → Filter: IsActive = Yes
  ↓
Action 2: Apply to each (application)
  ↓
  Condition: Check monitoring interval
    → If interval = 5min OR (interval = 10min AND minutes mod 10 = 0) OR ...
    ↓
    Action 2a: HTTP - Call Health Check
      → URI: CurrentItem HealthCheckURL
      → Method: GET
      → Timeout: CurrentItem ExpectedResponseTime × 2 (with max)
    ↓
    Action 2b: Parse Health Response
      → Parse JSON (if applicable)
    ↓
    Action 2c: Evaluate Health Status
      → Check: HTTP status code
      → Check: Response time
      → Check: Response content (if JSON)
    ↓
    Condition: Is Unhealthy?
      → Yes: Trigger Alert Flow
      → No: Update Status (healthy)
    ↓
  Action 3: Store Health Check Result
    → SharePoint: Add item to "Health Check Log"
```

### Step 3.2: Implement Health Check Logic

**Action 2a: HTTP Request**

```
Method: GET
URI: @{items('Apply_to_each')?['HealthCheckURL']}

Headers:
  Accept: application/json
  User-Agent: EGAT-HealthMonitor/1.0

Authentication: None (or API key if required)

Timeout: PT30S (30 seconds)
```

**Action 2c: Evaluate Health Status**

Use **Compose** action with formula:

```javascript
// Calculate health status
@{
  if(
    or(
      not(equals(outputs('HTTP')['statusCode'], 200)),
      greater(outputs('HTTP')['headers']?['X-Response-Time'], mul(items('Apply_to_each')?['ExpectedResponseTime'], 2))
    ),
    'unhealthy',
    if(
      greater(outputs('HTTP')['headers']?['X-Response-Time'], items('Apply_to_each')?['ExpectedResponseTime']),
      'degraded',
      'healthy'
    )
  )
}
```

**Criteria for Statuses**:
- **healthy**: HTTP 200, response time < expected
- **degraded**: HTTP 200, response time > expected but < 2× expected
- **unhealthy**: Non-200 status, timeout, or response time > 2× expected

### Step 3.3: Create Health Check Log

**SharePoint List**: "Health Check Log"

**Columns**:
```
- CheckID (Auto-generated)
- ApplicationID (Lookup to Application Inventory)
- CheckTime (Date and Time)
- Status (Choice: healthy, degraded, unhealthy)
- HTTPStatusCode (Number)
- ResponseTimeMS (Number)
- ErrorMessage (Multiple lines of text)
- RawResponse (Multiple lines of text)
```

**Action 3: Store Result**

```
SharePoint: Create item in "Health Check Log"

Fields:
  ApplicationID: @{items('Apply_to_each')?['ApplicationID']}
  CheckTime: @{utcNow()}
  Status: @{outputs('Compose_Health_Status')}
  HTTPStatusCode: @{outputs('HTTP')?['statusCode']}
  ResponseTimeMS: @{outputs('HTTP')?['headers']?['X-Response-Time']}
  ErrorMessage: @{if(equals(outputs('Compose_Health_Status'), 'unhealthy'), body('HTTP'), '')}
```

### Step 3.4: Create Alert Trigger Flow

**Flow Name**: "Application Health Alert Trigger"

**Trigger**: When called from Master Monitoring Flow

**Inputs**:
- ApplicationName (string)
- Status (string)
- ErrorDetails (string)
- Criticality (string)
- PrimaryContact (string)
- AlertChannel (string)

**Flow Actions**:

```
Step 1: Check if this is a new incident or ongoing
  → Query Health Check Log for recent entries
  → If last 3 checks = unhealthy → Ongoing incident
  → If last check = healthy, this check = unhealthy → New incident

Step 2: Determine alert severity
  → Critical app + unhealthy = CRITICAL alert
  → High app + unhealthy = HIGH alert
  → Medium/Low app + unhealthy = MEDIUM alert
  → Any app + degraded = WARNING alert

Step 3: Create incident record (if new)
  → SharePoint: Create item in "Incidents"
  → Fields: IncidentID, ApplicationID, StartTime, Status (Open), Severity

Step 4: Send alert to Teams
  → Use "Post adaptive card" action
  → Channel: From input parameter "AlertChannel"
  → Card: Alert template (see Phase 5)

Step 5: Optional: Create ticket
  → If critical, create ServiceNow/Jira ticket
  → Assign to on-call engineer

Step 6: Optional: Trigger auto-remediation
  → If known issue, call remediation flow
  → Examples: Restart app pool, clear cache, etc.
```

---

## Phase 4: Agent Creation

**Time**: 1 hour

### Step 4.1: Create Copilot Studio Agent

1. Navigate to [Copilot Studio](https://copilotstudio.microsoft.com/)

2. Create new agent:
   ```
   Name: EGAT Application Health Monitor
   Description: Monitors web application health and provides proactive alerts
   Language: Thai and English
   ```

3. **Configure Agent Instructions**:

```
You are the EGAT Application Health Monitoring Assistant. Your role is to help
IT Operations staff monitor application health, investigate issues, and provide
insights about application availability.

Core Capabilities:
1. Report current health status of all monitored applications
2. Provide detailed health information for specific applications
3. Show historical health trends and patterns
4. Alert on detected issues with context and suggested actions
5. Answer queries about application uptime and performance
6. Provide incident history and resolution guidance

Data Sources:
- Application Inventory (SharePoint)
- Health Check Log (SharePoint)
- Incidents (SharePoint)
- Azure Monitor (optional integration)

When reporting health status:
1. Start with overall summary (% healthy apps)
2. Highlight any critical or high-priority issues first
3. Provide recent trend (better/worse than yesterday)
4. For unhealthy apps, include:
   - When issue detected
   - Duration of outage/degradation
   - Likely cause (based on historical data)
   - Suggested remediation steps
5. Always include context (business impact, affected users)

Health Status Definitions:
- ✅ Healthy: Responding normally, response time within threshold
- ⚠️ Degraded: Responding but slow (response time > expected)
- 🔴 Unhealthy: Not responding, error status, or timeout

Alert Priority:
- CRITICAL: Production critical apps down
- HIGH: High-priority apps down or critical apps degraded
- MEDIUM: Medium-priority apps down or high-priority apps degraded
- LOW: Low-priority apps down or degraded

When asked about specific applications:
1. Show current status
2. Recent health checks (last hour)
3. Response time trend (avg last 24 hours)
4. Last incident (if any)
5. Uptime percentage (last 7/30 days)

Bilingual Support:
- Accept queries in Thai or English
- Respond in user's language
- Support technical terms in both languages

Proactive Monitoring:
- Continuously monitor health check results
- Alert when status changes from healthy to unhealthy
- Provide context and historical data with alerts
- Suggest remediation based on incident history
```

### Step 4.2: Configure Knowledge Sources

**Add Application Documentation** (if available):

1. **Knowledge source**: SharePoint document library
   ```
   Site: [Your site]
   Library: "Application Documentation"

   Documents:
   - Application architecture diagrams
   - Runbooks for common issues
   - Escalation procedures
   - Contact lists
   ```

2. **Refresh schedule**: Daily at 2:00 AM

---

## Phase 5: Alert Logic Configuration

**Time**: 2 hours

### Step 5.1: Create Alert Topic in Agent

**Topic Name**: "Application Health Alert"

**Trigger Phrases**:
```
- [Automated] Application {ApplicationName} is {Status}
- Health check failed for {ApplicationName}
```

**Note**: This topic is triggered BY Power Automate, not by user input

**Topic Nodes**:

**Node 1: Receive Alert Data** (from Power Automate)
```
Input variables:
- ApplicationName
- Status (unhealthy/degraded)
- ErrorDetails
- Criticality
- Timestamp
- IncidentID
```

**Node 2: Assess Severity**
```
Condition: Criticality = "Critical" AND Status = "unhealthy"
  → Path 1: CRITICAL alert
  → Path 2: HIGH/MEDIUM alert
```

**Node 3: Get Historical Context**
```
Query: Health Check Log
Filter: ApplicationID = current app, Last 24 hours
Calculate:
- Number of recent incidents
- Average recovery time
- Common error patterns
```

**Node 4: Generate Alert Message** (AI)
```
Use Generative Answers:

Prompt: "Generate a comprehensive alert message for IT Operations:

Application: {ApplicationName}
Status: {Status}
Criticality: {Criticality}
Error Details: {ErrorDetails}
Detected: {Timestamp}

Historical Context:
{HistoricalData}

Include:
1. Clear status summary
2. Business impact assessment
3. Affected users/systems
4. Likely cause (based on history)
5. Recommended actions (3-5 steps)
6. Related incidents (if any)

Format for Teams adaptive card with clear sections and emoji indicators."

Save to: Topic.AlertMessage
```

**Node 5: Send to Teams**
```
Call Power Automate: "Send Teams Alert"

Input:
- Channel: {AlertChannel}
- Message: {Topic.AlertMessage}
- Severity: CRITICAL/HIGH/MEDIUM
- IncidentID: {IncidentID}
- Actions: [Acknowledge, Escalate, View Details]
```

### Step 5.2: Create Conversational Query Topics

**Topic 1**: "Check Application Health"

**Trigger Phrases**:
```
- "What's the health status?"
- "Are all applications healthy?"
- "Show me application status"
- "แสดงสถานะแอปพลิเคชัน" (Thai)
```

**Flow**:
```
Node 1: Fetch Current Status
  → Query SharePoint: Get latest health check for each app (last 10 min)

Node 2: Categorize Applications
  → Group by status: healthy, degraded, unhealthy

Node 3: Generate Summary
  → AI: Create summary with counts, percentages, trends

Node 4: Present to User
  → Show summary with option to drill into specific apps
```

**Topic 2**: "Check Specific Application"

**Trigger Phrases**:
```
- "How is {ApplicationName} doing?"
- "Status of Employee Portal"
- "Check Document Management"
- "เช็คสถานะ {ApplicationName}" (Thai)
```

**Flow**:
```
Node 1: Identify Application
  → Entity extraction or ask user to select

Node 2: Get Detailed Status
  → Latest health check
  → Response time trend (last 24h)
  → Recent incidents
  → Uptime percentage (7/30 days)

Node 3: Present Details
  → Show comprehensive status report
```

**Topic 3**: "Show Health Trends"

**Trigger Phrases**:
```
- "Show me health trends"
- "How has uptime been?"
- "Application performance last week"
```

### Step 5.3: Create Teams Adaptive Card Template

**Adaptive Card JSON** (for critical alerts):

```json
{
  "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
  "type": "AdaptiveCard",
  "version": "1.4",
  "body": [
    {
      "type": "Container",
      "style": "attention",
      "items": [
        {
          "type": "ColumnSet",
          "columns": [
            {
              "type": "Column",
              "width": "auto",
              "items": [
                {
                  "type": "TextBlock",
                  "text": "🔴",
                  "size": "ExtraLarge"
                }
              ]
            },
            {
              "type": "Column",
              "width": "stretch",
              "items": [
                {
                  "type": "TextBlock",
                  "text": "CRITICAL ALERT - Application Issue Detected",
                  "weight": "Bolder",
                  "size": "Large",
                  "wrap": true
                }
              ]
            }
          ]
        }
      ]
    },
    {
      "type": "FactSet",
      "facts": [
        {
          "title": "Application:",
          "value": "${ApplicationName}"
        },
        {
          "title": "Status:",
          "value": "${Status}"
        },
        {
          "title": "Detected:",
          "value": "${Timestamp}"
        },
        {
          "title": "Duration:",
          "value": "${Duration}"
        }
      ]
    },
    {
      "type": "TextBlock",
      "text": "**Impact Assessment:**",
      "weight": "Bolder",
      "spacing": "Medium"
    },
    {
      "type": "TextBlock",
      "text": "${ImpactDescription}",
      "wrap": true
    },
    {
      "type": "TextBlock",
      "text": "**Suggested Actions:**",
      "weight": "Bolder",
      "spacing": "Medium"
    },
    {
      "type": "TextBlock",
      "text": "${SuggestedActions}",
      "wrap": true
    }
  ],
  "actions": [
    {
      "type": "Action.OpenUrl",
      "title": "View Full Incident",
      "url": "${IncidentURL}"
    },
    {
      "type": "Action.Submit",
      "title": "Acknowledge",
      "data": {
        "action": "acknowledge",
        "incidentId": "${IncidentID}"
      }
    },
    {
      "type": "Action.Submit",
      "title": "Escalate",
      "data": {
        "action": "escalate",
        "incidentId": "${IncidentID}"
      }
    }
  ]
}
```

---

## Phase 6: Integration with Existing Tools

**Time**: 1-2 hours (optional)

### Step 6.1: Azure Monitor Integration

If using Azure Monitor for infrastructure metrics:

**Power Automate Action**: "Azure Monitor - Query"

```
Query:
AzureMetrics
| where TimeGenerated > ago(5m)
| where ResourceId contains "your-app-resource-id"
| where MetricName in ("CpuPercentage", "MemoryPercentage", "Http5xx")
| summarize avg(Total) by MetricName
```

**Use Case**: Enrich health check alerts with infrastructure metrics

### Step 6.2: ServiceNow/Jira Integration

**For Critical Incidents**, auto-create tickets:

**Power Automate Action**: "ServiceNow - Create Incident"

```
Fields:
  Short Description: Application {ApplicationName} is {Status}
  Description: {Full alert details}
  Urgency: Critical
  Impact: High
  Category: Application
  Assignment Group: IT Operations
  Caller: Monitoring System
```

### Step 6.3: Email Notification (Backup)

**Power Automate Action**: "Send an email (V2)"

```
To: on-call@egat.co.th
Subject: [ALERT] {ApplicationName} - {Status}
Body: {Alert details}
Priority: High
```

---

## Phase 7: Testing

**Time**: 2-3 hours

See [testing-guide.md](./testing-guide.md) for comprehensive testing procedures.

**Quick Test Checklist**:
- [ ] Health checks running every 5 minutes
- [ ] Agent responds to status queries
- [ ] Alerts sent to Teams when app unhealthy
- [ ] Historical data tracked correctly
- [ ] Thai and English queries work

---

## Phase 8: Deployment

**Time**: 1 hour

### Step 8.1: Publish Agent

1. Click **Publish** in Copilot Studio
2. Review changes
3. Confirm publication

### Step 8.2: Deploy to Teams

1. **Channels** → **Microsoft Teams**
2. Configure:
   ```
   ☑ Available to: IT Operations team
   ☑ Allow in chats
   ☑ Allow in channels (IT Ops channel)
   ```
3. Share link with IT staff

### Step 8.3: Enable Monitoring Flows

1. Turn on "Application Health Check - Master" flow
2. Verify first run successful (check run history)
3. Monitor for 1 hour to ensure no errors

### Step 8.4: Configure Alert Channel

1. Create Teams channel: "Application Alerts"
2. Add IT Ops team members
3. Configure channel notifications (important messages only)
4. Pin agent to channel for easy access

---

## Phase 9: Monitoring & Optimization

**Time**: Ongoing

### Step 9.1: Monitor False Positives

**Week 1-2**: Track alert accuracy
- Log all alerts
- Verify each alert (was it a real issue?)
- Calculate false positive rate
- Target: < 5%

**If high false positives**:
- Adjust response time thresholds
- Refine health check logic
- Improve network stability

### Step 9.2: Optimize Check Intervals

**Review monthly**:
- Are 5-minute intervals necessary for all critical apps?
- Can some apps move to 10-minute intervals?
- Are any apps checking too infrequently?

**Cost optimization**:
- Power Automate runs = Applications × Checks/day × Days/month
- Example: 20 apps × 288 checks/day (5min) × 30 days = 172,800 runs/month
- Consider: Premium runs included in license, then $0.60 per 1,000 runs

### Step 9.3: Expand Monitoring

**After stable operation** (Month 2-3):
- Add more applications
- Implement advanced metrics (memory, CPU, DB connections)
- Add predictive alerts (trending toward unhealthy)
- Integrate with capacity planning

---

## Success Criteria

- [ ] All critical applications monitored 24/7
- [ ] Mean time to detection < 10 minutes (target: < 5 min)
- [ ] False positive rate < 5%
- [ ] IT staff using agent daily for status checks
- [ ] At least 60% of issues detected before user reports

---

## Troubleshooting

### Issue: Health Checks Timing Out

**Cause**: Network latency, application slow

**Solution**:
1. Increase timeout in HTTP action (30s → 60s)
2. Check network path (firewall, proxy)
3. Verify application performance
4. Consider caching/CDN if consistently slow

### Issue: Too Many False Positives

**Cause**: Threshold too aggressive

**Solution**:
1. Review response time thresholds (increase by 20-30%)
2. Add retry logic (check 2-3 times before alerting)
3. Implement "flapping" detection (ignore brief blips)

### Issue: Alerts Not Reaching Teams

**Cause**: Permissions, channel configuration

**Solution**:
1. Verify Teams channel URL is correct
2. Check app permissions in Teams
3. Verify Power Automate can post to channel
4. Test with manual "Post message" action

---

**Congratulations!** You've implemented an automated Application Health Monitoring system.

**Next**: See [testing-guide.md](./testing-guide.md) for comprehensive testing procedures.
