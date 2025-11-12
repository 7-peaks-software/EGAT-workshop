# Connections Required: Application Health Monitoring Agent

## Overview

This document details all connections, network requirements, and technical setup needed for the Application Health Monitoring Agent.

**Use Case**: UC05 - Application Health Monitoring Agent
**Complexity**: ⭐⭐⭐⭐ (Medium-High)
**Setup Time**: 2-3 hours

---

## 1. Network Connectivity Requirements

### 1.1 Power Platform to Applications

**Requirement**: Power Automate must reach application health check endpoints

**Source**: Power Platform (dynamic IPs)
- **Service**: Power Automate (cloud flows)
- **Region**: Your Power Platform region (e.g., Southeast Asia)
- **IP Ranges**: Download from Microsoft: https://www.microsoft.com/download/details.aspx?id=56519
  - Look for: `PowerPlatformInfra.[YourRegion]` and `PowerPlatformPlex.[YourRegion]`

**Destination**: Your application servers
- **Protocol**: HTTPS
- **Port**: 443
- **URLs**: Health check endpoints for each monitored application

**Firewall Configuration**:

```
Rule Name: Allow Power Platform Health Checks
Source: Power Platform IP ranges (from Microsoft download)
Destination: Application servers/load balancers
Protocol: TCP
Port: 443 (HTTPS)
Action: ALLOW
```

**For on-premise applications**:
1. **Option A**: Whitelist Power Platform IPs in firewall
2. **Option B**: Use On-Premise Data Gateway (for complex scenarios)
3. **Option C**: Expose via Azure Application Gateway with IP filtering

### 1.2 Network Testing

**Test Connectivity** before implementation:

```powershell
# Test from Power Automate HTTP action
Method: GET
URI: https://your-app.egat.co.th/health
Expected: HTTP 200 OK response

# If fails:
# - Check firewall rules
# - Verify DNS resolution
# - Check SSL certificate validity
# - Confirm health endpoint exists
```

**Common Issues**:
- ❌ **Connection timeout**: Firewall blocking Power Platform IPs
- ❌ **SSL certificate error**: Certificate not trusted or expired
- ❌ **DNS not found**: Application not accessible from public internet
- ❌ **401/403 Unauthorized**: Authentication required (configure in HTTP action)

---

## 2. Power Automate Connections

### 2.1 Required Connectors

| Connector | Type | Purpose | License Required |
|-----------|------|---------|------------------|
| **HTTP** | Premium | Call application health endpoints | Power Automate Premium |
| **HTTP with Azure AD** | Premium | For Azure AD protected apps | Power Automate Premium |
| **SharePoint** | Standard | Store application inventory & logs | Office 365 E3+ |
| **Microsoft Teams** | Standard | Send alerts and notifications | Office 365 E3+ |
| **Dataverse** | Standard | Alternative to SharePoint | Power Apps/Automate |
| **Azure Monitor** | Standard | Get infrastructure metrics (optional) | Azure subscription |
| **ServiceNow** | Premium | Create incidents (optional) | ServiceNow license |
| **Office 365 Outlook** | Standard | Email alerts (optional) | Office 365 E3+ |

### 2.2 HTTP Connector Setup

**Connection Configuration**:

1. **Create HTTP Connection**:
   ```
   Power Automate → Data → Connections → + New connection
   Search: "HTTP"
   Select: "HTTP" (Premium)
   ```

2. **HTTP Action Configuration** (in flow):

**For public endpoints without authentication**:
```
Method: GET
URI: https://your-app.egat.co.th/health

Headers:
  Accept: application/json
  User-Agent: EGAT-HealthMonitor/1.0

Authentication: None

Timeout: PT30S (30 seconds)
```

**For endpoints with API key authentication**:
```
Method: GET
URI: https://your-app.egat.co.th/health

Headers:
  Accept: application/json
  Authorization: Bearer YOUR_API_KEY
  User-Agent: EGAT-HealthMonitor/1.0

Authentication: None (handled in headers)
```

**For Azure AD protected applications**:
- Use **HTTP with Azure AD** connector instead
- Configure app registration in Azure AD
- Grant permissions to health endpoint

### 2.3 SharePoint Connection

**Purpose**: Store application inventory, health logs, and incident records

**Connection Setup**:
```
Power Automate → Data → Connections → + New connection
Search: "SharePoint"
Select: "SharePoint"
Authenticate: Use service account or your credentials
```

**SharePoint Lists to Create**:

#### List 1: Application Inventory

**Site**: [Your SharePoint site]
**List Name**: `ApplicationInventory`

| Column Name | Type | Required | Description |
|-------------|------|----------|-------------|
| ApplicationID | Single line of text | Yes | Unique identifier (e.g., APP-001) |
| ApplicationName | Single line of text | Yes | Display name |
| Environment | Choice (Production, Staging, Dev) | Yes | Environment type |
| Criticality | Choice (Critical, High, Medium, Low) | Yes | Business criticality |
| HealthCheckURL | Hyperlink | Yes | Health check endpoint |
| ExpectedResponseTime | Number | Yes | Threshold in milliseconds |
| HealthCheckInterval | Choice (5, 10, 15, 30) | Yes | Check frequency in minutes |
| PrimaryContact | Person or Group | Yes | Primary responsible person |
| SecondaryContact | Person or Group | No | Backup contact |
| AlertChannel | Single line of text | Yes | Teams channel for alerts |
| IsActive | Yes/No | Yes | Whether to monitor |
| LastCheckTime | Date and Time | No | Last health check timestamp |
| CurrentStatus | Choice (healthy, degraded, unhealthy, unknown) | No | Current health status |
| Notes | Multiple lines of text | No | Additional information |

**Sample Data**:
```
ApplicationID: APP-001
ApplicationName: EGAT Employee Portal
Environment: Production
Criticality: Critical
HealthCheckURL: https://portal.egat.co.th/health
ExpectedResponseTime: 2000
HealthCheckInterval: 5
PrimaryContact: john.doe@egat.co.th
AlertChannel: IT Ops Alerts
IsActive: Yes
```

#### List 2: Health Check Log

**List Name**: `HealthCheckLog`

| Column Name | Type | Description |
|-------------|------|-------------|
| CheckID | Auto-generated ID | Unique check identifier |
| ApplicationID | Lookup (to ApplicationInventory) | Which app was checked |
| CheckTime | Date and Time | When check was performed |
| Status | Choice (healthy, degraded, unhealthy) | Result status |
| HTTPStatusCode | Number | HTTP response code (200, 503, etc.) |
| ResponseTimeMS | Number | Response time in milliseconds |
| ErrorMessage | Multiple lines of text | Error details if unhealthy |
| RawResponse | Multiple lines of text | Full response body (if needed) |

**Retention Policy**: Keep last 90 days, archive older records

#### List 3: Incidents

**List Name**: `ApplicationIncidents`

| Column Name | Type | Description |
|-------------|------|-------------|
| IncidentID | Auto-generated | Unique incident ID |
| ApplicationID | Lookup (to ApplicationInventory) | Affected application |
| Severity | Choice (Critical, High, Medium, Low) | Incident severity |
| Status | Choice (Open, Investigating, Resolved, Closed) | Current status |
| DetectedTime | Date and Time | When issue first detected |
| ResolvedTime | Date and Time | When issue resolved |
| Duration | Number | Downtime in minutes |
| RootCause | Multiple lines of text | Identified root cause |
| ResolutionNotes | Multiple lines of text | How it was resolved |
| AssignedTo | Person or Group | Engineer handling incident |

**Permissions**:
- Service Account: Contribute (read/write)
- IT Operations Team: Contribute
- Management: Read

---

## 3. Microsoft Teams Integration

### 3.1 Teams Channel Setup

**Create Dedicated Alert Channel**:

```
Team: EGAT IT Operations
Channel Name: "Application Health Alerts"
Privacy: Private (IT Ops team only)
Purpose: Receive automated health alerts from monitoring agent

Tabs to add:
- Copilot Studio agent (as app)
- SharePoint list (ApplicationInventory)
- Power BI dashboard (optional)
```

**Channel Configuration**:
- **Notifications**: Important messages only (reduce noise)
- **Mentions**: @mention on-call engineer for critical alerts
- **Mobile push**: Enabled for critical alerts

### 3.2 Teams Connector

**Connection Setup**:
```
Power Automate → Data → Connections → + New connection
Search: "Microsoft Teams"
Select: "Microsoft Teams"
Authenticate: Use service account with Teams access
```

**Post Message Configuration**:

```
Action: Post adaptive card in a chat or channel

Team: EGAT IT Operations
Channel: Application Health Alerts

Card: [See implementation-guide.md for adaptive card JSON]

Importance: Normal (for warnings), Important (for critical)
```

### 3.3 Agent Deployment to Teams

**Deploy Copilot Studio Agent**:

1. Copilot Studio → [Your agent] → Channels → Microsoft Teams

2. Configuration:
   ```
   ☑ Enable app in Teams
   ☑ Available to: Specific security group (IT-Operations-Team)
   ☑ Show in personal apps
   ☑ Allow in chats
   ☑ Allow in channels

   Appearance:
   - Icon: Upload custom icon (512x512 PNG)
   - Accent color: #D13438 (Red for monitoring)
   - Short description: "Monitor application health and receive alerts"
   ```

3. Share installation link with IT team

---

## 4. Azure Monitor Integration (Optional)

### 4.1 When to Use Azure Monitor

**Use Cases**:
- Applications hosted in Azure (App Services, VMs, Containers)
- Need infrastructure metrics (CPU, memory, disk, network)
- Want to correlate application health with infrastructure health

**Connection Setup**:

1. **Azure Portal**: Create Service Principal
   ```
   App Registrations → New registration
   Name: PowerAutomate-Monitor-Reader
   Supported account types: This organization directory only
   Redirect URI: (leave blank)

   After creation:
   → Certificates & secrets → New client secret
   → Copy: Application (client) ID, Directory (tenant) ID, Client secret value
   ```

2. **Grant Permissions**:
   ```
   Navigate to resource (App Service, VM, etc.)
   → Access control (IAM) → Add role assignment
   Role: Monitoring Reader
   Assign access to: Service Principal
   Select: PowerAutomate-Monitor-Reader
   ```

3. **Power Automate Connection**:
   ```
   Connector: "Azure Monitor Logs"
   Authentication: Service Principal
   Tenant ID: [Your tenant ID]
   Client ID: [Application ID]
   Client Secret: [Secret value]
   ```

### 4.2 Sample Kusto Query

**Get application metrics**:

```kusto
AzureMetrics
| where TimeGenerated > ago(15m)
| where ResourceProvider == "MICROSOFT.WEB"
| where ResourceId contains "your-app-service-name"
| where MetricName in ("CpuPercentage", "MemoryPercentage", "Http5xx", "ResponseTime")
| summarize avg(Total) by MetricName, bin(TimeGenerated, 5m)
| project TimeGenerated, MetricName, AvgValue = avg_Total
```

**Use in Alert Logic**:
- If CPU > 90% AND app unhealthy → Likely resource exhaustion
- If Http5xx spike → Application errors
- If ResponseTime increasing → Performance degradation

---

## 5. Copilot Studio Configuration

### 5.1 Environment Requirements

**Environment Setup**:
```
Environment Name: EGAT Production
Region: Southeast Asia (Thailand)
Type: Production (with Dataverse)
Security Group: EGAT-IT-Operations

Capacity:
- AI Builder credits: 5,000-10,000/month
- Storage: 10-20 GB (for logs)
```

### 5.2 Agent Variables

**Global Variables** (Configure in Topics):

```
Variable: DefaultCheckInterval
Type: Number
Default: 5
Description: Default health check interval in minutes

Variable: CriticalAlertThreshold
Type: Number
Default: 3
Description: Consecutive failures before critical alert

Variable: TeamContactEmail
Type: String
Default: it-ops@egat.co.th
Description: Team distribution list
```

### 5.3 Data Source Connections

**SharePoint as Knowledge Source** (optional):

If adding documentation:

1. **SharePoint Document Library**: "Application Runbooks"
   ```
   Documents:
   - Runbook_EmployeePortal.pdf
   - Runbook_DocumentManagement.pdf
   - Escalation_Procedures.pdf
   - Common_Issues_Resolutions.pdf
   ```

2. **Connect to Agent**:
   ```
   Copilot Studio → [Agent] → Knowledge → + Add knowledge
   Source: SharePoint
   Site: [Your site]
   Library: Application Runbooks
   Authentication: Service account
   Refresh: Daily at 2:00 AM
   ```

---

## 6. Security and Compliance

### 6.1 Authentication & Authorization

**Service Account**:
- Create dedicated account: `svc-healthmonitor@egat.co.th`
- Permissions:
  - SharePoint: Contribute to monitoring lists
  - Teams: Post to alert channels
  - Azure Monitor: Monitoring Reader (if used)
  - Applications: Read-only access to health endpoints

**Azure AD Security Groups**:
```
Group: EGAT-IT-Ops-HealthMonitor-Users
Members: IT Operations staff
Purpose: Access to agent and alert channels
```

**Data Access Control**:
- Health Check Log: IT Ops team (read/write)
- Incidents: IT Ops team (read/write), Management (read)
- Application Inventory: IT Ops team (read/write), Management (read)

### 6.2 Data Privacy (PDPA Compliance)

**Personal Data Handling**:

```
Data Collected:
- Application names and URLs (not personal data)
- Contact names (business contacts only)
- Alert recipients (business emails)

PDPA Requirements:
✅ Purpose: Legitimate business need (IT operations)
✅ Consent: Not required (business operational data)
✅ Storage: Secure (SharePoint/Dataverse with encryption)
✅ Access: Limited to authorized IT staff
✅ Retention: 90 days for logs, incidents kept longer
```

**No Sensitive Data**:
- Do NOT log user credentials
- Do NOT store user activity (only system health)
- Do NOT expose database credentials in health checks

### 6.3 Audit Logging

**Enable Auditing**:

1. **Power Platform**:
   ```
   Power Platform Admin Center → Environments → [Your env]
   Settings → Audit and logs → Audit settings
   ☑ Enable auditing
   Retention: 90 days
   ```

2. **SharePoint**:
   ```
   Site Settings → Site collection audit settings
   ☑ Items, lists, and libraries: Edit, Delete
   ☑ Enable audit log trimming: 90 days
   ```

3. **Monitor Events**:
   - Health check executions
   - Alert deliveries
   - Agent queries
   - Configuration changes

---

## 7. Network Architecture

### 7.1 Connection Flow Diagram

```
┌──────────────────────────────────────────────────────────┐
│              EGAT Application Landscape                  │
│                                                          │
│  ┌──────────┐   ┌──────────┐   ┌──────────┐           │
│  │ Web App 1│   │ Web App 2│   │ Web App N│           │
│  │ /health  │   │ /health  │   │ /health  │           │
│  └─────┬────┘   └─────┬────┘   └─────┬────┘           │
└────────┼──────────────┼──────────────┼─────────────────┘
         │ HTTPS:443    │              │
         │              │              │
         ↓              ↓              ↓
┌──────────────────────────────────────────────────────────┐
│           Firewall / Load Balancer                       │
│   Rule: Allow from Power Platform IP ranges              │
└──────────────────────────────────────────────────────────┘
         ↑              ↑              ↑
         │ HTTPS        │              │
         └──────────────┴──────────────┘
                        │
         ┌──────────────┴─────────────┐
         │   Power Automate Flow       │
         │   (HTTP Connector)          │
         │   Every 5-15 minutes        │
         └──────────────┬──────────────┘
                        │
         ┌──────────────┴──────────────┐
         │  Store results & trigger     │
         │                              │
         ├→ SharePoint (Health logs)    │
         ├→ Copilot Studio (Analyze)    │
         └→ Teams (Alerts)              │

┌──────────────────────────────────────────────────────────┐
│              Microsoft 365 Environment                   │
│                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐ │
│  │  SharePoint  │  │Copilot Studio│  │    Teams     │ │
│  │  (Data)      │  │   (Agent)    │  │  (Alerts)    │ │
│  └──────────────┘  └──────────────┘  └──────────────┘ │
└──────────────────────────────────────────────────────────┘
         ↑                    ↑                  ↑
         │                    │                  │
         └────────────────────┴──────────────────┘
                    │
         ┌──────────┴──────────┐
         │   IT Operations     │
         │   Staff (Users)     │
         └─────────────────────┘
```

### 7.2 IP Whitelisting Procedure

**Step-by-Step**:

1. **Download Microsoft IP ranges**:
   ```
   URL: https://www.microsoft.com/download/details.aspx?id=56519
   File: ServiceTags_Public_[YYYYMMDD].json
   ```

2. **Extract Power Platform IPs for your region**:
   ```json
   {
     "name": "PowerPlatformInfra.SoutheastAsia",
     "properties": {
       "addressPrefixes": [
         "20.195.64.0/19",
         "20.195.96.0/20",
         // ... more IP ranges
       ]
     }
   }
   ```

3. **Configure Firewall**:
   ```
   For each IP range in addressPrefixes:
     → Add inbound rule
     → Source: [IP range]
     → Destination: Your application servers
     → Port: 443
     → Action: ALLOW
   ```

4. **Schedule Quarterly Updates**:
   - Microsoft updates IP ranges regularly
   - Set calendar reminder to review quarterly
   - Automate if possible (firewall supports API)

---

## 8. Cost Breakdown

### Monthly Operational Costs

| Component | Cost | Notes |
|-----------|------|-------|
| **Power Automate Premium** | $15/user/month | Need 1 service account |
| **Copilot Studio** | $200/month | 25,000 messages included |
| **SharePoint Storage** | Included | Part of M365 E3+ |
| **Teams** | Included | Part of M365 E3+ |
| **Azure Monitor** (optional) | $0-50/month | If querying logs |
| **ServiceNow/Jira** (optional) | Existing license | If integrating ticketing |

**Total Estimated Cost**: $215-265/month

**Premium Runs Cost**:
- Included: 10,000 runs/month per user
- Additional: $0.60 per 1,000 runs
- Example: 20 apps × 288 checks/day × 30 days = 172,800 runs/month
  - Overage: 162,800 runs × $0.60/1,000 = ~$98/month

**Optimization**:
- Use 10-15 min intervals for non-critical apps (reduce runs)
- Group checks in single flow (reduce flow starts)

**vs. Enterprise Monitoring (Datadog, New Relic)**:
- Enterprise APM: $50-100K/year for 20-50 apps
- This solution: $3K/year
- **Savings**: 94-97%

---

## 9. Testing Connections Checklist

### Pre-Implementation Checklist

**Network Connectivity**:
- [ ] Can access application health endpoints from browser
- [ ] SSL certificates valid (no browser warnings)
- [ ] DNS resolves correctly for all application URLs
- [ ] Firewall rules configured for Power Platform IPs
- [ ] Test HTTP action in Power Automate successfully connects

**Power Automate Connectors**:
- [ ] HTTP connector available and tested
- [ ] SharePoint connector established
- [ ] Teams connector established
- [ ] Test flow runs end-to-end successfully

**SharePoint Lists**:
- [ ] ApplicationInventory list created with correct columns
- [ ] HealthCheckLog list created
- [ ] ApplicationIncidents list created
- [ ] Service account has Contribute permissions
- [ ] At least 3 test applications added to inventory

**Microsoft Teams**:
- [ ] Alert channel created
- [ ] IT Ops team members added to channel
- [ ] Agent deployed to Teams
- [ ] Test message posts successfully
- [ ] Notifications configured properly

**Copilot Studio Agent**:
- [ ] Agent created and configured
- [ ] Test queries respond correctly
- [ ] Can query SharePoint data
- [ ] Authentication working
- [ ] Agent accessible in Teams

---

## 10. Troubleshooting Connection Issues

### Issue: HTTP Connection Times Out

**Symptoms**: Health check fails with timeout error

**Possible Causes**:
1. Firewall blocking Power Platform IPs
2. Application slow to respond
3. Network latency

**Solutions**:
```
1. Verify firewall rules allow Power Platform IPs
2. Increase timeout in HTTP action (30s → 60s)
3. Check application performance (may need optimization)
4. Test connectivity from Azure VM in same region
5. Consider using On-Premise Data Gateway if on-prem app
```

### Issue: SharePoint Permission Denied

**Symptoms**: "Access denied" error when writing to SharePoint

**Possible Causes**:
1. Service account lacks permissions
2. List doesn't exist
3. Wrong site URL

**Solutions**:
```
1. Grant service account "Contribute" on SharePoint site
2. Verify list names match exactly (case-sensitive)
3. Check site URL in flow (no trailing slashes)
4. Test: Manually add item to list as service account
```

### Issue: Teams Alerts Not Posting

**Symptoms**: Alerts don't appear in Teams channel

**Possible Causes**:
1. Wrong channel ID
2. App not added to team
3. Permissions issue

**Solutions**:
```
1. Get channel ID: Teams → Channel → More options → Get link
2. Add agent app to team: Apps → [Your agent] → Add to team
3. Verify flow uses correct Teams connector
4. Test: Manually post message to channel via flow
```

### Issue: Agent Can't Access SharePoint Data

**Symptoms**: Agent says "no data found" or errors when querying

**Possible Causes**:
1. Connection not established
2. Permissions missing
3. Wrong list/site

**Solutions**:
```
1. Re-establish SharePoint connection in Copilot Studio
2. Grant agent service principal read access to SharePoint
3. Verify list names and site URL correct
4. Test: Query list directly in Power Automate first
```

---

## 11. Next Steps

After configuring all connections:

1. ✅ Verify all connections tested and working
2. ✅ Whitelist Power Platform IPs in firewall
3. ✅ Create SharePoint lists with sample data
4. ✅ Deploy Power Automate monitoring flows
5. ✅ Configure Copilot Studio agent
6. ⏩ Proceed to **[testing-guide.md](./testing-guide.md)**

---

**Document Version**: 1.0
**Last Updated**: 2025-11-12
**Maintained By**: EGAT IT Operations Team
**Next Review**: 2025-12-12
