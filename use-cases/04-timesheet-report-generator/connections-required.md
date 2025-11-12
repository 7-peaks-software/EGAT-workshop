# Connections Required: Timesheet Report Generator

## Overview

This document details all connections, data sources, and technical requirements needed to implement the Timesheet Report Generator use case.

**Use Case**: UC04 - Timesheet Report Generator
**Complexity**: ⭐⭐⭐⭐ (Medium-High)
**Setup Time**: 2-4 hours

---

## 1. OpenProject API Connection

### 1.1 Access Requirements

**Prerequisites**:
- OpenProject instance (cloud or on-premise)
- Administrator or API access rights
- API v3 endpoint availability

**OpenProject Editions**:
- ✅ **Community Edition**: API available (free)
- ✅ **Enterprise Cloud**: Full API access
- ✅ **Enterprise On-Premise**: Full API access

### 1.2 API Token Setup

**Step-by-Step**:

1. **Log into OpenProject**:
   ```
   URL: https://[your-instance].openproject.com
   User: Administrator or user with API permissions
   ```

2. **Navigate to Access Tokens**:
   - Click avatar (top-right) → **My Account**
   - Select **Access tokens** tab
   - Click **+ Create new API token**

3. **Configure Token**:
   ```
   Name: "Copilot Studio Timesheet Integration"
   Scopes:
     ☑ Read time entries
     ☑ Read projects
     ☑ Read users
     ☑ Read work packages (optional)

   Expiry: 1 year (or per organizational policy)
   ```

4. **Store Token Securely**:
   ```
   ⚠️ CRITICAL: Copy token immediately (shown only once)

   Recommended storage:
   - Azure Key Vault (preferred for production)
   - Power Automate secure input (minimum)
   - Never commit to source control
   ```

### 1.3 API Endpoints Used

| Endpoint | Purpose | Method | Authentication |
|----------|---------|--------|----------------|
| `/api/v3/time_entries` | Fetch timesheet data | GET | Bearer token |
| `/api/v3/projects` | Get project list | GET | Bearer token |
| `/api/v3/users` | Get user information | GET | Bearer token |
| `/api/v3/activities` | Get activity types | GET | Bearer token |
| `/api/v3/work_packages` | Get work package details | GET | Bearer token |

**Sample API Call**:
```bash
curl -X GET "https://[instance].openproject.com/api/v3/time_entries?filters=[{\"spent_on\":{\"operator\":\"<t-\",\"values\":[\"30\"]}}]" \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json"
```

**Expected Response Structure**:
```json
{
  "_type": "Collection",
  "total": 150,
  "_embedded": {
    "elements": [
      {
        "id": 12345,
        "spentOn": "2025-11-10",
        "hours": "PT4H30M",
        "comment": "Development work on feature X",
        "_links": {
          "project": {
            "href": "/api/v3/projects/5",
            "title": "EGAT ERP Upgrade"
          },
          "user": {
            "href": "/api/v3/users/42",
            "title": "John Doe"
          },
          "activity": {
            "href": "/api/v3/time_entries/activities/10",
            "title": "Development"
          }
        }
      }
    ]
  }
}
```

### 1.4 Rate Limits and Best Practices

**OpenProject API Limits**:
- Community: ~100 requests/minute
- Enterprise: ~500 requests/minute
- Use pagination for large datasets (`pageSize` parameter)

**Optimization**:
```
✅ DO:
- Use date filters to limit results
- Paginate large datasets (pageSize=100)
- Cache reference data (projects, users, activities)
- Schedule data sync during off-peak hours

❌ DON'T:
- Fetch all historical data every time
- Make redundant calls for reference data
- Run sync during business hours (high API usage)
```

### 1.5 Network Requirements

**Firewall Rules**:
```
From: Power Platform (dynamic IPs)
To: OpenProject instance
Port: 443 (HTTPS)
Protocol: TLS 1.2+

For On-Premise OpenProject:
- Whitelist Power Platform IP ranges
- Configure reverse proxy if needed
- Ensure SSL certificate is valid
```

**Power Platform IP Ranges**:
- Download from: https://www.microsoft.com/download/confirmation.aspx?id=56519
- Service Tag: `PowerPlatform.<region>`
- Update quarterly (Microsoft publishes changes)

---

## 2. Power Automate Connections

### 2.1 Required Connectors

| Connector | Type | Purpose | License Required |
|-----------|------|---------|------------------|
| **HTTP** | Premium | Call OpenProject API | Power Automate Premium |
| **HTTP with Azure AD** | Premium | Alternative auth method | Power Automate Premium |
| **SharePoint** | Standard | Store aggregated data | Office 365 E3+ |
| **Dataverse** | Standard | Alternative data storage | Power Apps/Automate |
| **Office 365 Outlook** | Standard | Email reports (optional) | Office 365 E3+ |
| **Microsoft Teams** | Standard | Send notifications | Office 365 E3+ |

### 2.2 HTTP Connector Setup

**Connection Configuration**:

1. **Create HTTP Connection**:
   ```
   Power Automate → Data → Connections → + New connection
   Search: "HTTP"
   Select: "HTTP" (Premium)
   ```

2. **Configure Authentication** (in Flow action):
   ```
   Method: GET
   URI: https://[instance].openproject.com/api/v3/time_entries

   Headers:
   {
     "Authorization": "Bearer @{parameters('OpenProjectAPIToken')}",
     "Content-Type": "application/json"
   }

   Authentication: None (handled in headers)
   ```

**Alternative: HTTP with Azure AD**:
- Better for enterprise environments
- Requires registering OpenProject as Azure AD app
- More complex setup but more secure

### 2.3 SharePoint Connection

**Purpose**: Store aggregated timesheet data and reports

**Setup Requirements**:

1. **Create SharePoint Lists**:

**List 1: Timesheet Entries**
```
Site: [Your SharePoint site]
List Name: "TimesheetEntries"

Columns:
- EntryID (Single line of text) - OpenProject ID
- SpentOn (Date)
- Hours (Number, decimal)
- UserName (Single line of text)
- ProjectName (Single line of text)
- ActivityType (Choice: Meeting, Development, Testing, Admin, Other)
- Comment (Multiple lines of text)
- ImportDate (Date and Time)
- Month (Single line of text) - Format: YYYY-MM
- Year (Number)
```

**List 2: Monthly Aggregations**
```
List Name: "MonthlyTimesheetSummary"

Columns:
- Month (Single line of text) - Format: YYYY-MM
- TotalHours (Number, decimal)
- ProjectBreakdown (Multiple lines of text, JSON)
- ActivityBreakdown (Multiple lines of text, JSON)
- TeamUtilization (Number, decimal) - Percentage
- ReportGeneratedDate (Date and Time)
```

**List 3: Project Mapping** (Reference data)
```
List Name: "ProjectMapping"

Columns:
- OpenProjectID (Number)
- ProjectName (Single line of text)
- ProjectCode (Single line of text)
- Department (Choice: IT, HR, Finance, Operations, etc.)
- IsActive (Yes/No)
- BudgetedHours (Number)
```

2. **Configure Permissions**:
   ```
   Service Account: "CopilotStudio-Timesheet"
   Permissions: Contribute (read/write to lists)
   ```

3. **Create Power Automate Connection**:
   ```
   Data → Connections → + New connection
   Search: "SharePoint"
   Select: "SharePoint"
   Authenticate: Use service account or your credentials
   ```

### 2.4 Dataverse Connection (Alternative)

**When to Use**:
- Need complex relationships between tables
- Require advanced security/permissions
- Want better performance for large datasets

**Tables to Create**:

**Table 1: Timesheet Entry**
```
Table Name: egat_timesheetentry

Columns:
- egat_entryid (Text) - Primary key from OpenProject
- egat_spenton (Date Only)
- egat_hours (Decimal)
- egat_username (Text)
- egat_projectname (Text)
- egat_activitytype (Choice)
- egat_comment (Multiline Text)
- egat_month (Text)
- egat_importdate (Date and Time)
```

**Table 2: Monthly Summary**
```
Table Name: egat_monthlysummary

Columns:
- egat_month (Text) - Format: YYYY-MM
- egat_totalhours (Decimal)
- egat_projectbreakdown (Multiline Text, JSON)
- egat_activitybreakdown (Multiline Text, JSON)
- egat_utilizationrate (Decimal)
```

**Connection Setup**:
```
Data → Connections → + New connection
Search: "Microsoft Dataverse"
Select: "Microsoft Dataverse"
Environment: [Your environment]
```

---

## 3. Copilot Studio Configuration

### 3.1 Environment Requirements

**Environment Setup**:
```
Environment Name: EGAT Production (or Dev for testing)
Region: Southeast Asia (closest to Thailand)
Type: Production (with Dataverse)
Security Group: EGAT-CopilotStudio-Users

Capacity Requirements:
- AI Builder credits: 5,000-10,000/month
- Storage: 10-20 GB (for logs and data)
```

### 3.2 Knowledge Sources

**Optional - For Enhanced Reports**:

If adding documentation or guidelines to agent:

1. **SharePoint Document Library**:
   ```
   Site: [Your site]
   Library: "Timesheet Guidelines"

   Documents to add:
   - Timesheet policy (PDF)
   - Project categorization guide
   - Activity type definitions
   - FAQ for common timesheet questions
   ```

2. **Connect to Copilot**:
   ```
   Copilot Studio → [Your agent] → Knowledge → + Add knowledge
   Source: SharePoint
   Select site and library
   Authentication: Service account
   Refresh: Daily
   ```

### 3.3 Variables and Parameters

**Global Variables** (Configure in Copilot Studio):

```
Variable Name: DefaultTimesheetMonth
Type: String
Default Value: [Calculated - last month]
Description: Default reporting period

Variable Name: OpenProjectAPIEndpoint
Type: String
Default Value: https://[instance].openproject.com/api/v3
Description: Base API URL

Variable Name: ReportRecipients
Type: String
Default Value: pm-team@egat.co.th
Description: Email distribution list
```

---

## 4. Azure Key Vault (Recommended for Production)

### 4.1 Setup

**Purpose**: Securely store OpenProject API token

**Steps**:

1. **Create Key Vault**:
   ```
   Azure Portal → Create Resource → Key Vault

   Name: egat-copilot-secrets
   Region: Southeast Asia
   Pricing Tier: Standard
   ```

2. **Add Secret**:
   ```
   Key Vault → Secrets → + Generate/Import

   Name: OpenProjectAPIToken
   Value: [Paste your API token]
   Content Type: text/plain
   Activation date: [Today]
   Expiration date: [1 year from now]
   ```

3. **Grant Access to Power Automate**:
   ```
   Key Vault → Access policies → + Add Access Policy

   Secret permissions: Get, List
   Select principal: [Your Power Automate service account]
   ```

4. **Use in Power Automate**:
   ```
   Add action: "Azure Key Vault - Get secret"

   Vault Name: egat-copilot-secrets
   Secret Name: OpenProjectAPIToken

   Then use: @{body('Get_secret')?['value']}
   ```

---

## 5. Microsoft Teams Integration

### 5.1 Teams Channel Setup

**Create Dedicated Channel**:
```
Team: EGAT Project Management
Channel Name: "Timesheet Reports"
Privacy: Private (only PMs)

Tabs to Add:
- Copilot Studio agent (as app)
- SharePoint list (Monthly summaries)
- Power BI report (optional)
```

### 5.2 Deploy Agent to Teams

**Deployment Steps**:
```
Copilot Studio → [Your agent] → Channels → Microsoft Teams

Settings:
☑ Enable app in Teams
☑ Available to: Specific security group (EGAT-PM-Team)
☑ Show in personal apps
☑ Allow in chats
☑ Allow in channels
☐ Allow in meetings (not needed)

Appearance:
- Icon: Upload custom icon (512x512 PNG)
- Accent color: #0078D4 (EGAT blue)
- Short description: "Generate monthly timesheet reports from OpenProject"
```

---

## 6. Data Requirements

### 6.1 OpenProject Data Quality

**Required Data in OpenProject**:

✅ **Complete Time Entries**:
- All team members log time daily
- Projects are correctly assigned
- Activity types are standardized
- Comments are meaningful (optional but helpful)

✅ **Project Setup**:
- All active projects exist in OpenProject
- Projects have clear names and codes
- Projects are associated with correct users

✅ **User Profiles**:
- All users have complete profiles
- Email addresses are up to date
- Roles/teams are defined

**Data Quality Checks** (Run before implementation):
```sql
-- Check for time entries without projects
SELECT * FROM time_entries WHERE project_id IS NULL;

-- Check for users without email
SELECT * FROM users WHERE mail IS NULL;

-- Check for undefined activity types
SELECT DISTINCT activity_id FROM time_entries
WHERE activity_id NOT IN (SELECT id FROM enumerations WHERE type = 'TimeEntryActivity');
```

### 6.2 Reference Data

**Maintain in SharePoint** (for mapping and enrichment):

**Project Codes Mapping**:
```
OpenProject ID → Project Code → Department
1 → ERP-2025 → IT
2 → HR-Portal → HR
3 → Cloud-Migration → IT Operations
```

**Activity Type Standardization**:
```
OpenProject Activity → Categorization
Meeting → Overhead
Development → Productive
Code Review → Productive
Testing → Productive
Admin → Overhead
Training → Investment
```

**User Team Mapping**:
```
UserID → Team → Department → Manager
42 → Development Team A → IT → John Manager
43 → Development Team A → IT → John Manager
44 → Infrastructure → IT Ops → Jane Manager
```

---

## 7. Security and Compliance

### 7.1 Data Privacy (PDPA Compliance)

**Personal Data Handling**:

```
Data Collected:
- User names (from OpenProject)
- Email addresses (for reports)
- Time entries (work hours)

PDPA Requirements:
✅ Purpose: Legitimate business need (timesheet reporting)
✅ Consent: Implied (employees using corporate systems)
✅ Storage: Secure (Azure/Microsoft 365)
✅ Access: Limited to authorized PMs and admins
✅ Retention: As per company policy (e.g., 7 years)

Data Processing Agreement:
- Microsoft Power Platform: Covered under M365 agreement
- OpenProject: Verify DPA if cloud-hosted
```

### 7.2 Access Control

**Role-Based Permissions**:

| Role | Access Level | Permissions |
|------|-------------|-------------|
| **Project Managers** | Read reports | View own team reports, export data |
| **HR/Admin** | Read all reports | View all reports, comparative analysis |
| **IT Admins** | Full access | Configure agent, modify flows, manage connections |
| **Copilot Studio Service Account** | Technical | API access, data sync operations |

**Implementation**:
```
Azure AD Security Groups:
- EGAT-Timesheet-PM: Project managers
- EGAT-Timesheet-Admin: HR and senior leadership
- EGAT-Timesheet-IT: IT administrators

Copilot Studio:
Settings → Security → Authentication
☑ Require authentication
☑ Azure AD authentication
Allowed groups: [Select security groups above]
```

### 7.3 Audit Logging

**Enable Logging**:

1. **Power Platform Audit**:
   ```
   Power Platform Admin Center → Environments → [Your env]
   Settings → Audit and logs → Audit settings
   ☑ Enable auditing
   Retention: 90 days (or per policy)
   ```

2. **Log Key Events**:
   - API calls to OpenProject
   - Report generation requests
   - Data exports
   - Configuration changes

3. **Monitor with Azure Log Analytics** (Optional):
   - Export Power Platform logs to Azure
   - Create alerts for anomalies
   - Compliance reporting

---

## 8. Testing Connections Checklist

Before implementing the full solution, verify each connection:

### Pre-Implementation Checklist

**OpenProject API**:
- [ ] API token generated and stored securely
- [ ] Can fetch time entries via API (test with curl/Postman)
- [ ] API returns expected data structure
- [ ] Rate limits understood and documented
- [ ] Network access confirmed (firewall rules)

**Power Automate**:
- [ ] Premium license assigned to service account
- [ ] HTTP connector available and tested
- [ ] Can parse OpenProject JSON response
- [ ] SharePoint/Dataverse connection established
- [ ] Test flow runs successfully end-to-end

**Copilot Studio**:
- [ ] Environment created with Dataverse
- [ ] Agent created and published
- [ ] Can trigger Power Automate flows
- [ ] Test queries return expected results
- [ ] Authentication configured

**Microsoft Teams**:
- [ ] Agent deployed to Teams
- [ ] Accessible to target security group
- [ ] Notifications working
- [ ] Channel configured

**Data Quality**:
- [ ] OpenProject has sufficient historical data (at least 1 month)
- [ ] Reference data (projects, users, activities) is complete
- [ ] No orphaned entries (entries without projects/users)

---

## 9. Connection Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    EGAT Timesheet Report Generator          │
│                     Connection Architecture                  │
└─────────────────────────────────────────────────────────────┘

┌──────────────────────┐
│   OpenProject API    │ ◄──── API Token (Bearer Auth)
│  (On-prem or Cloud)  │       Stored in Azure Key Vault
└──────────┬───────────┘
           │ HTTPS (TLS 1.2+)
           │ Port 443
           │
           ↓
┌──────────────────────┐
│  Power Automate Flow │ ◄──── HTTP Premium Connector
│   (Data Sync Job)    │       Scheduled: Daily 6:00 AM
└──────────┬───────────┘
           │
           ├──→ Parse JSON (OpenProject response)
           │
           ├──→ Transform data (aggregations)
           │
           ↓
┌──────────────────────┐
│ SharePoint Lists OR  │ ◄──── SharePoint/Dataverse Connector
│   Dataverse Tables   │       Standard connector (included)
└──────────┬───────────┘
           │
           │ Data Source for Reports
           │
           ↓
┌──────────────────────┐
│ Copilot Studio Agent │ ◄──── Power Automate Trigger
│  (Report Generator)  │       Cloud flow action
└──────────┬───────────┘
           │
           ├──→ Generative AI (report analysis)
           │
           ├──→ Query timesheet data
           │
           ↓
┌──────────────────────┐
│   Microsoft Teams    │ ◄──── Teams Connector
│ (Delivery Channel)   │       Standard connector
└──────────────────────┘
           │
           ├──→ Interactive chat with PMs
           ├──→ Automated notifications
           └──→ Report exports (PDF/Excel)

┌──────────────────────┐
│   Azure Key Vault    │ ◄──── Secure secret storage
│   (API Tokens)       │       Azure Key Vault connector
└──────────────────────┘

┌──────────────────────┐
│   Azure AD           │ ◄──── User authentication
│ (Authentication)     │       Single sign-on
└──────────────────────┘
```

---

## 10. Cost Breakdown

### Monthly Operational Costs

| Component | Cost | Notes |
|-----------|------|-------|
| **Power Automate Premium** | $15/user/month | Need 1-2 service accounts |
| **Copilot Studio** | $200/month | 25,000 messages included |
| **SharePoint Storage** | Included | Part of M365 E3+ |
| **Dataverse Storage** | ~$0-10/month | If using Dataverse (10GB included) |
| **Azure Key Vault** | $0.03/10K ops | ~$1-2/month |
| **Azure Log Analytics** | Optional | $0-5/month for basic logs |
| **OpenProject** | $0-495/month | Community (free) or Enterprise |

**Total Estimated Cost**: $230-250/month for production system

**vs. Manual Timesheet Reporting**:
- PM time saved: 4-8 hours/month × 10 PMs = 40-80 hours/month
- Cost of PM time: 80 hours × $50/hour = $4,000/month saved
- **Net Savings**: ~$3,750/month

---

## 11. Troubleshooting Common Connection Issues

### Issue: OpenProject API Returns 401 Unauthorized

**Causes**:
- Expired API token
- Incorrect token in Power Automate
- Token not properly passed in header

**Solutions**:
1. Regenerate token in OpenProject
2. Update token in Azure Key Vault or Power Automate
3. Verify header format: `Authorization: Bearer [token]`
4. Check for extra spaces or special characters

### Issue: Power Automate Flow Times Out

**Causes**:
- Large dataset (too many time entries)
- OpenProject instance slow/overloaded
- Network latency

**Solutions**:
1. Add pagination to API calls (`pageSize=100`)
2. Use date filters to limit results
3. Increase timeout in HTTP action (default: 2 min → 5 min)
4. Run sync during off-peak hours

### Issue: SharePoint Connection Permission Denied

**Causes**:
- Service account lacks permissions
- List permissions too restrictive

**Solutions**:
1. Grant "Contribute" permissions to service account
2. Verify list exists and is accessible
3. Check site permissions (not just list)

### Issue: Copilot Studio Can't Trigger Power Automate

**Causes**:
- Flow not shared with agent
- Flow not turned on
- Connection reference not resolved

**Solutions**:
1. Share flow explicitly with Copilot Studio app
2. Verify flow is "On" in Power Automate
3. Re-establish connection in Copilot Studio

---

## 12. Next Steps

After configuring all connections:

1. ✅ Verify all connections established
2. ✅ Test OpenProject API access
3. ✅ Create SharePoint lists (or Dataverse tables)
4. ✅ Deploy Power Automate flows
5. ✅ Configure Copilot Studio agent
6. ⏩ Proceed to **[testing-guide.md](./testing-guide.md)**

---

**Document Version**: 1.0
**Last Updated**: 2025-11-12
**Maintained By**: EGAT IT Team
**Next Review**: 2025-12-12
