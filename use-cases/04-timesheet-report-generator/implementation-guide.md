# Implementation Guide: Timesheet Report Generator

## Table of Contents

1. [Prerequisites Check](#prerequisites-check)
2. [Phase 1: OpenProject API Setup](#phase-1-openproject-api-setup)
3. [Phase 2: Power Automate Flow Creation](#phase-2-power-automate-flow-creation)
4. [Phase 3: Agent Creation](#phase-3-agent-creation)
5. [Phase 4: Data Processing Logic](#phase-4-data-processing-logic)
6. [Phase 5: Visualization Setup](#phase-5-visualization-setup)
7. [Phase 6: Testing](#phase-6-testing)
8. [Phase 7: Deployment](#phase-7-deployment)
9. [Phase 8: Monitoring & Optimization](#phase-8-monitoring--optimization)

**Estimated Total Time**: 8-12 hours

---

## Prerequisites Check

Before starting, verify you have:

- [ ] Reviewed [README.md](./README.md)
- [ ] Reviewed [connections-required.md](./connections-required.md)
- [ ] OpenProject instance with API access
- [ ] OpenProject API token obtained
- [ ] Power Automate Premium license
- [ ] Copilot Studio license
- [ ] SharePoint site for report storage
- [ ] Completed Lab 01 and Lab 02

---

## Phase 1: OpenProject API Setup

**Time**: 1-2 hours

### Step 1.1: Obtain OpenProject API Token

1. **Log into OpenProject**
   - URL: `https://[your-openproject-instance].openproject.com`
   - Use your administrator account

2. **Navigate to API Settings**:
   - Click your avatar (top-right)
   - Select **My Account**
   - Click **Access tokens** tab

3. **Create New API Token**:
   ```
   Token name: "Copilot Studio Timesheet Integration"
   Scopes: Read-only (for safety)
   Expiry: 1 year (or as per policy)
   ```

4. **Copy and Secure Token**:
   - Copy token immediately (shown only once)
   - Store in secure location (Azure Key Vault recommended)
   - Document token name and creation date

### Step 1.2: Test API Access

Test the API using PowerShell or Postman:

```bash
# Test API endpoint
curl -H "Authorization: Bearer YOUR_API_TOKEN" \
  https://[instance].openproject.com/api/v3/time_entries

# Expected: JSON response with time entries
```

**Verify**:
- [ ] API returns 200 OK status
- [ ] Time entry data is accessible
- [ ] User permissions are correct

### Step 1.3: Document OpenProject Structure

Map your OpenProject structure:

```markdown
## OpenProject Data Structure

**Projects**:
- Project A (ID: 1)
- Project B (ID: 2)
- Project C (ID: 3)

**Work Package Types**:
- Task
- Bug
- Feature
- Meeting

**Activity Types**:
- Development
- Meeting
- Testing
- Documentation
- Admin

**Users to Monitor**:
- User 1 (ID: 10, Team: Development)
- User 2 (ID: 11, Team: Development)
- [List all team members]
```

---

## Phase 2: Power Automate Flow Creation

**Time**: 2-3 hours

### Step 2.1: Create HTTP Connection

1. **Open Power Automate**:
   - Go to https://make.powerautomate.com
   - Select your environment

2. **Create New Flow**:
   ```
   Type: Scheduled cloud flow
   Name: "OpenProject Timesheet Data Sync"
   Schedule: Daily at 6:00 AM
   ```

3. **Add HTTP Action**:
   - Action: **HTTP** (Premium connector)
   - Method: `GET`
   - URI: `https://[instance].openproject.com/api/v3/time_entries`
   - Headers:
     ```
     Authorization: Bearer [YOUR_API_TOKEN]
     Content-Type: application/json
     ```

### Step 2.2: Create Data Extraction Flow

**Flow Structure**:

```
Trigger: Recurrence (Daily 6:00 AM)
  ↓
Action 1: Get Time Entries (HTTP GET)
  ↓
Action 2: Parse JSON (Parse API response)
  ↓
Action 3: Filter Time Entries (Only last month)
  ↓
Action 4: Store in SharePoint/Dataverse
  ↓
Action 5: Notify completion (optional)
```

**Action 2 Schema** (Parse JSON):
```json
{
    "type": "object",
    "properties": {
        "_embedded": {
            "type": "object",
            "properties": {
                "elements": {
                    "type": "array",
                    "items": {
                        "type": "object",
                        "properties": {
                            "id": {"type": "integer"},
                            "spentOn": {"type": "string"},
                            "hours": {"type": "string"},
                            "comment": {"type": "string"},
                            "_links": {
                                "type": "object",
                                "properties": {
                                    "project": {
                                        "type": "object",
                                        "properties": {
                                            "title": {"type": "string"}
                                        }
                                    },
                                    "user": {
                                        "type": "object",
                                        "properties": {
                                            "title": {"type": "string"}
                                        }
                                    },
                                    "activity": {
                                        "type": "object",
                                        "properties": {
                                            "title": {"type": "string"}
                                        }
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }
    }
}
```

### Step 2.3: Create Aggregation Flow

**Flow: Calculate Monthly Metrics**

```
Trigger: When timesheet data is stored
  ↓
Action 1: Get all entries for month
  ↓
Action 2: Group by Project
  ↓
Action 3: Group by User
  ↓
Action 4: Group by Activity Type
  ↓
Action 5: Calculate totals and percentages
  ↓
Action 6: Store aggregated data
```

**Sample Calculation Logic** (using Compose actions):

```javascript
// Total hours per project
outputs('Filter_array_by_project')?['length']

// Percentage calculation
mul(div(variables('ProjectHours'), variables('TotalHours')), 100)

// Average hours per day
div(variables('TotalHours'), variables('WorkingDays'))
```

---

## Phase 3: Agent Creation

**Time**: 1 hour

### Step 3.1: Create Copilot Studio Agent

1. Navigate to [Copilot Studio](https://copilotstudio.microsoft.com/)

2. Create new agent:
   ```
   Name: EGAT Timesheet Report Generator
   Description: Generates monthly timesheet reports with insights from OpenProject
   Language: Thai and English
   ```

3. **Configure Agent Instructions**:

```
You are the EGAT Timesheet Report Generator. Your role is to help project managers
create and analyze timesheet reports from OpenProject data.

Core Capabilities:
1. Generate monthly timesheet summary reports
2. Analyze time allocation across projects and activities
3. Identify trends, patterns, and anomalies
4. Provide actionable insights for resource planning
5. Compare periods (month-over-month, quarter-over-quarter)
6. Create visualizations and export reports

Report Components You Generate:
1. EXECUTIVE SUMMARY
   - Total hours logged
   - Number of projects worked
   - Team utilization rate
   - Key highlights and concerns

2. PROJECT BREAKDOWN
   - Hours per project (with percentages)
   - Project ranking by effort
   - Budget vs actual comparison (if available)

3. ACTIVITY ANALYSIS
   - Time by activity type (Meeting, Development, etc.)
   - Productive vs non-productive time ratio
   - Overhead analysis

4. TEAM INSIGHTS
   - Individual utilization rates
   - Overtime tracking
   - Workload distribution

5. TRENDS AND PATTERNS
   - Week-over-week trends
   - Peak productivity days/times
   - Comparison to previous periods

6. RECOMMENDATIONS
   - Resource reallocation suggestions
   - Capacity planning insights
   - Process improvement opportunities

Data Sources:
- OpenProject API (via Power Automate)
- SharePoint (aggregated data)
- Historical reports (for comparisons)

When generating reports:
1. Start with high-level summary (executive view)
2. Provide detailed breakdowns upon request
3. Highlight anomalies and concerns
4. Always compare to baselines/targets
5. Use visualizations for clarity
6. Provide actionable recommendations

Format Guidelines:
- Use tables for numerical data
- Use bullet points for insights
- Include percentages and trends
- Highlight concerns with ⚠️ symbol
- Highlight successes with ✅ symbol
- Include period comparisons

Bilingual Support:
- Accept queries in Thai or English
- Generate reports in requested language
- Support mixed-language scenarios
```

---

## Phase 4: Data Processing Logic

**Time**: 2-3 hours

### Step 4.1: Create "Generate Monthly Report" Topic

1. **Create Topic**: Monthly Timesheet Report

2. **Trigger Phrases**:
```
- "Generate monthly timesheet report"
- "Create timesheet summary for [month]"
- "Show me timesheet report"
- "รายงานไทม์ชีทประจำเดือน"
- "สร้างรายงานเวลาทำงาน"
```

3. **Flow Design**:

**Node 1: Ask for Period**
```
Question: "Which month would you like to report on?"

Options:
- Current month
- Last month
- Custom month (specify: YYYY-MM)

Save to: Topic.ReportMonth
```

**Node 2: Ask for Scope**
```
Question: "Report scope?"

Options:
- My team only
- Specific project
- All projects
- Custom selection

Save to: Topic.ReportScope
```

**Node 3: Fetch Data** (via Power Automate)
```
Call Power Automate flow: "Get Timesheet Data"

Input:
- Month: {Topic.ReportMonth}
- Scope: {Topic.ReportScope}

Output saved to: Topic.TimesheetData
```

**Node 4: Generate Summary** (AI Processing)
```
Use Generative Answers node:

Prompt: "Analyze this timesheet data and create an executive summary:

Data: {Topic.TimesheetData}

Include:
1. Total hours logged
2. Number of active projects
3. Top 3 projects by hours
4. Team utilization rate
5. Key insights (3-5 points)
6. Top concerns (if any)

Format as markdown with clear sections."

Save to: Topic.ExecutiveSummary
```

**Node 5: Generate Breakdowns**
```
Create detailed breakdowns:
- By project
- By activity type
- By team member
- By week

Use parallel processing for speed.
```

**Node 6: Create Visualizations**
```
Generate chart descriptions:
- Pie chart: Hours by project
- Bar chart: Weekly hours trend
- Heatmap: Daily activity
- Line chart: Month-over-month comparison
```

**Node 7: Compile Final Report**
```
Combine all sections:
1. Executive Summary
2. Detailed Breakdowns
3. Visualizations
4. Insights & Recommendations
5. Action Items

Format for readability.
```

**Node 8: Offer Export**
```
Question: "How would you like to receive this report?"

Options:
- View in Teams
- Download as PDF
- Download as PowerPoint
- Download as Excel
- Email report
- Save to SharePoint

Execute based on selection.
```

### Step 4.2: Create Comparison Logic

**Topic**: Compare Periods

Allow PM to compare:
- This month vs last month
- This quarter vs last quarter
- This month vs same month last year
- Custom period comparison

**Key Metrics to Compare**:
- Total hours (±% change)
- Project allocation shifts
- Utilization rate changes
- Overtime trends
- Productivity indicators

---

## Phase 5: Visualization Setup

**Time**: 1-2 hours

### Step 5.1: Power BI Integration (Optional)

If using Power BI for advanced visualizations:

1. **Create Power BI Dataset**:
   - Source: SharePoint list or Dataverse
   - Refresh: Daily
   - Measures: Total Hours, Avg Hours, Utilization %

2. **Create Dashboard**:
   - Page 1: Executive Summary
   - Page 2: Project Breakdown
   - Page 3: Team Analysis
   - Page 4: Trends

3. **Embed in Agent** (using Power BI connector)

### Step 5.2: Chart Generation in Agent

For simpler visualizations without Power BI:

**Generate Chart Data**:
```
Agent creates data in format suitable for charts:

Pie Chart Data (Hours by Project):
{
  "labels": ["Project A", "Project B", "Project C", "Admin"],
  "data": [520, 380, 240, 140],
  "percentages": [41%, 30%, 19%, 11%]
}

Present as ASCII table or formatted text.
```

**Example Output**:
```
Hours by Project:
==================
Project A: ████████████████████████░░░░░░ 520h (41%)
Project B: ██████████████████░░░░░░░░░░░░ 380h (30%)
Project C: ███████████░░░░░░░░░░░░░░░░░░░ 240h (19%)
Admin:     ██████░░░░░░░░░░░░░░░░░░░░░░░░ 140h (11%)
```

---

## Phase 6: Testing

**Time**: 1-2 hours

See [testing-guide.md](./testing-guide.md) for comprehensive testing.

### Quick Testing Checklist

- [ ] Data fetched correctly from OpenProject API
- [ ] Calculations are accurate (verify manually)
- [ ] Report generates in < 2 minutes
- [ ] All sections included
- [ ] Visualizations clear and accurate
- [ ] Export functions work
- [ ] Comparisons calculate correctly
- [ ] Thai and English queries work

---

## Phase 7: Deployment

**Time**: 30 minutes

### Step 7.1: Publish Agent

1. Click **Publish** in Copilot Studio
2. Review changes
3. Confirm publication

### Step 7.2: Deploy to Teams

1. **Channels** → **Microsoft Teams**
2. Configure:
   ```
   ☑ Available to: Project Managers group
   ☑ Allow in chats
   ☑ Allow in channels (PM channel)
   ```
3. Share link with PMs

### Step 7.3: Schedule Automated Reports

Create Power Automate flow:
```
Trigger: Recurrence (1st of each month, 8:00 AM)
  ↓
Action 1: Call Copilot Studio agent API
Action 2: Generate report for previous month
Action 3: Post to Teams channel
Action 4: Send email to PMs
```

---

## Phase 8: Monitoring & Optimization

**Time**: Ongoing

### Step 8.1: Monitor Usage

Track:
- Number of reports generated per week
- Most requested report types
- Average generation time
- User satisfaction

### Step 8.2: Optimize Performance

Monthly:
- Review slow queries
- Optimize Power Automate flows
- Clean up old data
- Update insights logic based on feedback

### Step 8.3: Expand Features

Based on feedback, add:
- Budget vs actual tracking
- Resource forecast projections
- Custom report templates
- Integration with other tools

---

## Success Criteria

- [ ] Report generation time < 2 minutes
- [ ] Data accuracy ≥ 99%
- [ ] PM satisfaction ≥ 4.5/5.0
- [ ] Time saved ≥ 4 hours/month per PM
- [ ] Reports used for monthly reviews

---

## Troubleshooting

### Issue: API Connection Fails

**Cause**: Token expired or invalid permissions

**Solution**:
1. Regenerate API token in OpenProject
2. Update token in Power Automate
3. Verify API permissions
4. Test connection manually

### Issue: Inaccurate Calculations

**Cause**: Data filtering or aggregation logic error

**Solution**:
1. Verify Power Automate filter conditions
2. Check date range calculations
3. Manually validate sample data
4. Review aggregation formulas

### Issue: Slow Report Generation

**Cause**: Too much data or inefficient queries

**Solution**:
1. Add data pagination
2. Pre-aggregate data in Power Automate
3. Cache frequently requested reports
4. Optimize API calls

---

**Congratulations!** You've implemented an automated Timesheet Report Generator.

**Next**: See [testing-guide.md](./testing-guide.md) for comprehensive testing procedures.
