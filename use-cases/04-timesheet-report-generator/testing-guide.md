# Testing Guide: Timesheet Report Generator

## Overview

This comprehensive testing guide ensures your Timesheet Report Generator agent works correctly before production deployment.

**Use Case**: UC04 - Timesheet Report Generator
**Testing Time**: 2-3 hours
**Prerequisites**: Complete implementation per [implementation-guide.md](./implementation-guide.md)

---

## Testing Phases

1. [Phase 1: API Connection Testing](#phase-1-api-connection-testing) (15 min)
2. [Phase 2: Data Sync Testing](#phase-2-data-sync-testing) (20 min)
3. [Phase 3: Agent Response Testing](#phase-3-agent-response-testing) (30 min)
4. [Phase 4: Report Accuracy Testing](#phase-4-report-accuracy-testing) (30 min)
5. [Phase 5: Performance Testing](#phase-5-performance-testing) (15 min)
6. [Phase 6: Error Handling Testing](#phase-6-error-handling-testing) (20 min)
7. [Phase 7: User Acceptance Testing](#phase-7-user-acceptance-testing) (30 min)

---

## Phase 1: API Connection Testing

**Objective**: Verify OpenProject API connectivity and data retrieval

### Test 1.1: Basic API Connectivity

**Steps**:
1. Open Power Automate
2. Navigate to your "OpenProject Timesheet Data Sync" flow
3. Click **Test** → **Manually** → **Run flow**

**Expected Result**:
```
✅ HTTP action succeeds (status code 200)
✅ Response body contains JSON data
✅ Flow completes without errors
```

**What to Check**:
- HTTP status code = 200
- Response time < 5 seconds
- JSON structure matches schema

**If Test Fails**:
```
❌ Status 401: Check API token validity
❌ Status 403: Verify API permissions
❌ Status 404: Verify API endpoint URL
❌ Timeout: Check network/firewall rules
```

### Test 1.2: Data Format Validation

**Steps**:
1. In flow run history, expand **HTTP** action
2. View **Outputs** → **Body**
3. Verify JSON structure

**Expected Data Structure**:
```json
{
  "_type": "Collection",
  "total": [number],
  "_embedded": {
    "elements": [
      {
        "id": [integer],
        "spentOn": "YYYY-MM-DD",
        "hours": "PT[H]H[M]M",
        "comment": "[string]",
        "_links": {
          "project": { "title": "[string]" },
          "user": { "title": "[string]" },
          "activity": { "title": "[string]" }
        }
      }
    ]
  }
}
```

**Validation Checklist**:
- [ ] `_embedded.elements` is an array
- [ ] Each element has `id`, `spentOn`, `hours`
- [ ] `_links` contains `project`, `user`, `activity`
- [ ] Dates are in ISO format (YYYY-MM-DD)
- [ ] Hours are in ISO 8601 duration format (PT4H30M)

### Test 1.3: Filtered Data Retrieval

**Steps**:
1. Modify HTTP action to add date filter:
   ```
   URI: https://[instance].openproject.com/api/v3/time_entries?filters=[{"spent_on":{"operator":"<t-","values":["30"]}}]
   ```
2. Run flow
3. Verify only last 30 days of data returned

**Expected Result**:
```
✅ All entries have spentOn within last 30 days
✅ Total count matches expectation
✅ No entries older than 30 days
```

**Pass Criteria**: Flow returns filtered data correctly ✅

---

## Phase 2: Data Sync Testing

**Objective**: Verify data is correctly stored in SharePoint/Dataverse

### Test 2.1: SharePoint List Population

**Steps**:
1. Run Power Automate sync flow
2. Open SharePoint list: "TimesheetEntries"
3. Verify new items created

**Expected Result**:
```
✅ List contains new entries
✅ All columns populated correctly
✅ Data matches OpenProject source
```

**Data Validation**:

| Field | Validation |
|-------|------------|
| EntryID | Matches OpenProject time_entry.id |
| SpentOn | Valid date, within expected range |
| Hours | Decimal number > 0 |
| UserName | Not empty, matches OpenProject user |
| ProjectName | Not empty, matches OpenProject project |
| ActivityType | One of: Meeting, Development, Testing, Admin, Other |
| Month | Format: YYYY-MM |

**Sample Check**:
```
Pick random entry from SharePoint:
  EntryID: 12345
  Hours: 4.5

Cross-reference with OpenProject:
  → Find entry ID 12345
  → Verify hours match (4.5 = PT4H30M)
  → Verify project name matches
```

### Test 2.2: Data Transformation Accuracy

**Test Hour Format Conversion**:

OpenProject uses ISO 8601 duration format (PT4H30M), SharePoint should store decimal (4.5).

**Test Cases**:

| OpenProject Format | Expected Decimal | Pass/Fail |
|-------------------|------------------|-----------|
| PT1H | 1.0 | |
| PT2H30M | 2.5 | |
| PT8H | 8.0 | |
| PT0H45M | 0.75 | |
| PT4H15M | 4.25 | |

**Verification**:
1. Pick 5 random entries
2. Compare OpenProject hours vs SharePoint hours
3. Confirm conversion accuracy

**Pass Criteria**: 100% of conversions accurate ✅

### Test 2.3: Monthly Aggregation

**Steps**:
1. Run aggregation flow (or wait for scheduled run)
2. Open SharePoint list: "MonthlyTimesheetSummary"
3. Verify monthly totals

**Expected Result**:
```
✅ One row per month
✅ TotalHours = sum of all entries for that month
✅ ProjectBreakdown contains JSON with project totals
✅ ActivityBreakdown contains JSON with activity totals
```

**Manual Validation**:
```
1. Filter TimesheetEntries for specific month (e.g., 2025-10)
2. Sum Hours column manually (or use Excel)
3. Compare with TotalHours in MonthlyTimesheetSummary
4. Should match exactly
```

**Pass Criteria**: Aggregations accurate within 0.1 hour tolerance ✅

---

## Phase 3: Agent Response Testing

**Objective**: Verify Copilot Studio agent responds correctly

### Test 3.1: Basic Query Handling

**Test in Copilot Studio Test Chat**:

| Query | Expected Behavior |
|-------|------------------|
| "Hello" | Greeting + explanation of capabilities |
| "Help" | List of available commands/reports |
| "Generate monthly report" | Asks for month/period |
| "Show timesheet for October 2025" | Generates report for Oct 2025 |

**Pass Criteria**: Agent responds appropriately to all queries ✅

### Test 3.2: Report Generation Flow

**Test Case**: Generate monthly report

**Steps**:
1. Open Teams → Timesheet Report Generator agent
2. Type: `"Generate monthly timesheet report for October 2025"`
3. Follow prompts

**Expected Conversation Flow**:
```
User: "Generate monthly timesheet report for October 2025"

Agent: "I'll generate the timesheet report for October 2025.
        What scope would you like?"

        Options:
        - My team only
        - Specific project
        - All projects

User: [Selects "All projects"]

Agent: "Generating report for October 2025 (All projects)...
        ⏳ Fetching data from OpenProject...
        ⏳ Analyzing 1,247 time entries...
        ⏳ Calculating metrics...

        ✅ Report ready!

        [Shows executive summary]"
```

**Validation**:
- [ ] Agent asks for month
- [ ] Agent asks for scope
- [ ] Agent shows progress indicators
- [ ] Report generates within 2 minutes
- [ ] Report content is accurate

### Test 3.3: Report Content Validation

**Verify Report Sections**:

**Section 1: Executive Summary**
- [ ] Total hours logged
- [ ] Number of projects
- [ ] Team utilization rate
- [ ] Key highlights (3-5 points)

**Section 2: Project Breakdown**
- [ ] Hours per project
- [ ] Percentages add up to ~100%
- [ ] Projects ranked by hours
- [ ] Visualization (ASCII chart or table)

**Section 3: Activity Analysis**
- [ ] Hours by activity type (Meeting, Development, etc.)
- [ ] Percentages calculated correctly
- [ ] Productive vs overhead ratio shown

**Section 4: Team Insights**
- [ ] Individual utilization rates
- [ ] Workload distribution
- [ ] Top contributors listed

**Section 5: Trends**
- [ ] Week-over-week breakdown
- [ ] Comparison to previous month (if available)

**Section 6: Recommendations**
- [ ] Actionable insights provided
- [ ] Based on actual data analysis

**Pass Criteria**: All sections present and accurate ✅

### Test 3.4: Bilingual Support

**Test Thai Queries**:

| Thai Query | Expected Behavior |
|------------|------------------|
| "สวัสดี" | Thai greeting + capabilities |
| "สร้างรายงานไทม์ชีท" | Generate report (Thai) |
| "แสดงรายงานเดือนตุลาคม" | October report (Thai) |

**Test Mixed Language**:
```
User: "Generate รายงาน for October"
Agent: Should handle gracefully (primarily English response)
```

**Pass Criteria**: Agent handles Thai queries correctly ✅

---

## Phase 4: Report Accuracy Testing

**Objective**: Verify calculations and data accuracy

### Test 4.1: Total Hours Calculation

**Manual Verification**:

1. **Select Test Month**: October 2025
2. **Get Agent Report**: Total hours from agent
3. **Manual Calculation**:
   ```
   - Export TimesheetEntries for Oct 2025 to Excel
   - Sum "Hours" column
   - Compare with agent's reported total
   ```

**Expected**: Match within 0.5 hour (accounting for rounding)

**Test Cases**:

| Month | Agent Total | Manual Total | Difference | Pass/Fail |
|-------|-------------|--------------|------------|-----------|
| Oct 2025 | 1,247.5 | | | |
| Sep 2025 | 1,180.0 | | | |
| Aug 2025 | 1,320.5 | | | |

**Pass Criteria**: Difference < 0.5 hours (< 0.04%) ✅

### Test 4.2: Project Allocation Accuracy

**Test Case**: Verify project hour distribution

**Steps**:
1. Agent reports: "Project A: 42% (520 hours)"
2. Manual check:
   ```
   - Filter entries for Project A in Oct 2025
   - Sum hours
   - Calculate percentage: (Project A hours / Total hours) × 100
   ```

**Expected**: Matches agent's calculation

**Test 3 Projects**:

| Project | Agent Hours | Agent % | Manual Hours | Manual % | Pass/Fail |
|---------|-------------|---------|--------------|----------|-----------|
| Project A | 520 | 42% | | | |
| Project B | 380 | 30% | | | |
| Project C | 240 | 19% | | | |

**Pass Criteria**: All within 1% tolerance ✅

### Test 4.3: Activity Type Distribution

**Verify Activity Breakdown**:

Agent reports:
```
Activity Breakdown:
- Meetings: 47% (587 hours)
- Development: 24% (300 hours)
- Reviews: 19% (237 hours)
- Planning: 11% (137 hours)
```

**Manual Validation**:
1. Group entries by ActivityType
2. Sum hours per activity
3. Calculate percentages
4. Compare with agent

**Pass Criteria**: Activity percentages accurate within 1% ✅

### Test 4.4: Trend Calculations

**Test Month-over-Month Comparison**:

Agent reports:
```
Comparison to Previous Month (Sep 2025):
📈 Total hours: +2.5 hours (+2.1%)
📊 Meeting time: +3 hours (+15%)
📉 Development time: -1 hour (-3%)
```

**Manual Verification**:
```
Oct hours: 1,247.5
Sep hours: 1,180.0
Difference: 1,247.5 - 1,180.0 = 67.5 hours

Agent says: +2.5 hours ← CHECK IF CORRECT
```

**If discrepancy found**: Review calculation logic in agent prompts

**Pass Criteria**: Trend calculations accurate ✅

---

## Phase 5: Performance Testing

**Objective**: Ensure agent responds within acceptable timeframes

### Test 5.1: Report Generation Speed

**Test Different Data Volumes**:

| Scenario | Time Entries | Expected Time | Actual Time | Pass/Fail |
|----------|--------------|---------------|-------------|-----------|
| Small dataset (100 entries) | 100 | < 30 sec | | |
| Medium dataset (500 entries) | 500 | < 60 sec | | |
| Large dataset (2000 entries) | 2000 | < 120 sec | | |

**How to Test**:
1. Use different months with varying entry counts
2. Measure from user query to report delivery
3. Run each test 3 times, take average

**Pass Criteria**: 95% of reports generate < 2 minutes ✅

### Test 5.2: Concurrent User Testing

**Simulate Multiple PMs**:

1. Have 3-5 users request reports simultaneously
2. Measure response times
3. Check for degradation

**Expected Behavior**:
- All users get responses
- No significant slowdown (< 20% increase in response time)
- No errors or timeouts

**Pass Criteria**: System handles 5 concurrent users without issues ✅

### Test 5.3: API Rate Limit Testing

**Test OpenProject API Limits**:

1. Trigger multiple sync flows in short succession
2. Monitor for rate limit errors (429 status)
3. Verify retry logic works

**Expected**:
- Flow handles rate limits gracefully
- Automatic retry after delay
- No data loss

**Pass Criteria**: No rate limit errors in normal usage ✅

---

## Phase 6: Error Handling Testing

**Objective**: Verify agent handles errors gracefully

### Test 6.1: API Connection Failures

**Simulate Failures**:

**Test 6.1a: OpenProject API Down**
```
Steps:
1. Temporarily disable OpenProject instance (or block network)
2. Request report from agent
3. Observe behavior

Expected:
- Agent detects issue
- Shows friendly error message:
  "⚠️ I'm unable to connect to OpenProject right now.
  Please try again in a few minutes or contact IT support."
- Does not crash or show technical error
```

**Test 6.1b: Invalid API Token**
```
Steps:
1. Change API token to invalid value in Power Automate
2. Run sync flow
3. Observe error handling

Expected:
- Flow catches 401 error
- Sends notification to IT team
- Agent informs user: "Data sync issue detected. IT team notified."
```

**Pass Criteria**: All errors handled gracefully, no technical jargon shown to users ✅

### Test 6.2: Missing or Incomplete Data

**Test 6.2a: No Data for Requested Month**
```
User Query: "Generate report for December 2030"

Expected Agent Response:
"⚠️ I couldn't find any timesheet data for December 2030.
This could be because:
- The month hasn't occurred yet
- No time entries logged for that period
- Data not yet synced from OpenProject

Would you like to:
- Choose a different month?
- See available date ranges?"
```

**Test 6.2b: Partial Data (Project Missing)**
```
Scenario: Entry references non-existent project

Expected:
- Agent includes entry in "Other/Unassigned" category
- Flags in report: "⚠️ 12 hours logged to undefined projects"
- Suggests: "Please review project assignments in OpenProject"
```

**Pass Criteria**: Agent handles missing data gracefully ✅

### Test 6.3: User Input Validation

**Test Invalid Inputs**:

| Invalid Input | Expected Behavior |
|---------------|------------------|
| "Generate report for ABC" | "Please specify a valid month (e.g., October 2025)" |
| "Report for 13th month" | "Invalid month. Please choose 1-12 or month name." |
| "Show me last year" | "Please specify exact month/year (e.g., 'October 2024')" |
| Empty/nonsense input | "I didn't understand. Try 'Generate monthly report'" |

**Pass Criteria**: All invalid inputs handled with helpful messages ✅

---

## Phase 7: User Acceptance Testing

**Objective**: Real PM users validate functionality

### Test 7.1: Pilot User Testing

**Setup**:
1. Select 2-3 project managers for pilot
2. Provide brief training (10 minutes)
3. Have them use agent for 1 week
4. Collect feedback

**Test Scenarios for Pilot Users**:

**Scenario 1: Generate Own Team Report**
```
Task: "Generate a timesheet report for your team for last month"

Success Criteria:
- User able to complete task without assistance
- Report generated accurately
- User finds report useful
```

**Scenario 2: Compare Two Periods**
```
Task: "Compare this month's hours vs last month"

Success Criteria:
- User can request comparison
- Agent provides clear comparison
- Trends are understandable
```

**Scenario 3: Export Report**
```
Task: "Export report to PDF or Excel for presentation"

Success Criteria:
- Export function works
- Format is professional
- Data matches Teams report
```

### Test 7.2: Feedback Collection

**User Feedback Form**:

```markdown
## Timesheet Report Generator - Pilot Feedback

**User**: [Name]
**Role**: Project Manager
**Test Period**: [Dates]

### Usability (1-5 stars)
- Ease of use: ⭐⭐⭐⭐⭐
- Response time: ⭐⭐⭐⭐⭐
- Report clarity: ⭐⭐⭐⭐⭐
- Overall satisfaction: ⭐⭐⭐⭐⭐

### Accuracy
- Data matched expectations: YES / NO
- Calculations correct: YES / NO
- Found discrepancies: YES / NO
  - If yes, describe: ___________

### Value
- Time saved vs manual reporting: [X hours/month]
- Would recommend to other PMs: YES / NO
- Most useful feature: ___________
- Missing feature: ___________

### Open Feedback
What worked well:
[Free text]

What needs improvement:
[Free text]

Suggested enhancements:
[Free text]
```

**Target Scores**:
- Average rating: ≥ 4.0/5.0
- Accuracy: ≥ 95% "YES"
- Recommendation: ≥ 80% "YES"

**Pass Criteria**: Pilot users satisfied (≥ 4.0/5.0 rating) ✅

### Test 7.3: Real-World Scenario Testing

**Test Case: Monthly Management Review**

**Scenario**: PM needs to present last month's project hours to management

**Steps**:
1. PM requests October 2025 report
2. Agent generates comprehensive report
3. PM exports to PowerPoint/PDF
4. PM presents to management
5. Management asks follow-up questions
6. PM uses agent for ad-hoc queries during meeting

**Questions to Answer**:
- Which project consumed most hours?
- How did this month compare to last?
- What's our team utilization rate?
- Are we on track with project budgets?

**Success Criteria**:
- [ ] PM successfully generates report
- [ ] Report answers management questions
- [ ] Ad-hoc queries work during meeting
- [ ] Management satisfied with insights

**Pass Criteria**: Successfully supports real management review ✅

---

## Test Results Summary

### Overall Testing Scorecard

| Phase | Tests | Passed | Failed | Pass Rate |
|-------|-------|--------|--------|-----------|
| 1. API Connection | 3 | | | |
| 2. Data Sync | 3 | | | |
| 3. Agent Response | 4 | | | |
| 4. Report Accuracy | 4 | | | |
| 5. Performance | 3 | | | |
| 6. Error Handling | 3 | | | |
| 7. User Acceptance | 3 | | | |
| **TOTAL** | **23** | | | |

**Required Pass Rate for Production**: ≥ 95% (22/23 tests)

---

## Known Issues Log

Track issues discovered during testing:

| Issue ID | Description | Severity | Status | Resolution |
|----------|-------------|----------|--------|------------|
| TST-001 | Example: Slow response for >1000 entries | Medium | Open | Add pagination |
| | | | | |

**Severity Levels**:
- **Critical**: Blocks production use
- **High**: Major functionality affected
- **Medium**: Minor functionality or performance issue
- **Low**: Cosmetic or rare edge case

---

## Production Readiness Checklist

Before deploying to production:

### Technical Readiness
- [ ] All Phase 1-6 tests passed (≥ 95%)
- [ ] Performance meets targets (< 2 min for reports)
- [ ] Error handling validated
- [ ] Security review completed
- [ ] Backup/rollback plan documented

### User Readiness
- [ ] Pilot testing completed successfully
- [ ] User feedback ≥ 4.0/5.0
- [ ] Training materials prepared
- [ ] Support process defined

### Operational Readiness
- [ ] Monitoring configured
- [ ] Alerting set up for failures
- [ ] IT support team briefed
- [ ] Escalation process defined
- [ ] Maintenance windows scheduled

### Documentation
- [ ] User guide finalized
- [ ] Admin guide complete
- [ ] Troubleshooting runbook ready
- [ ] Change log maintained

---

## Post-Deployment Validation

**Week 1 After Launch**:
- [ ] Monitor usage (number of reports generated)
- [ ] Check error rates (< 5%)
- [ ] Collect user feedback
- [ ] Review performance metrics
- [ ] Address critical issues

**Month 1 After Launch**:
- [ ] Review success metrics vs targets
- [ ] Conduct user satisfaction survey
- [ ] Identify enhancement opportunities
- [ ] Document lessons learned

---

## Regression Testing

**When to Re-test**:
- After any agent configuration changes
- After Power Automate flow updates
- After OpenProject API changes
- Quarterly (scheduled maintenance)

**Quick Regression Test** (30 minutes):
1. Run Phase 1: API Connection (Test 1.1 only)
2. Generate sample report (Test 3.2)
3. Verify accuracy on 1 month (Test 4.1)
4. Check performance (Test 5.1 - small dataset)
5. Test 1 error scenario (Test 6.1a)

**Pass Criteria**: All 5 quick tests pass ✅

---

## Support Contacts

**During Testing**:
- Technical Issues: IT Support (support@egat.co.th)
- OpenProject API: [OpenProject Admin Contact]
- Copilot Studio: [Implementation Team Lead]

**Production Support**:
- Tier 1: IT Helpdesk
- Tier 2: Copilot Studio Admin Team
- Tier 3: Microsoft Support (Premier)

---

**Testing Sign-Off**

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Test Lead | | | |
| PM Representative | | | |
| IT Manager | | | |

**Approval for Production**: YES / NO

**Conditions/Notes**: _________________________________

---

**Next Steps After Testing**:
1. ✅ All tests passed
2. ⏩ Address any issues found
3. ⏩ Complete documentation
4. ⏩ Deploy to production
5. ⏩ Monitor and optimize

---

**Document Version**: 1.0
**Last Updated**: 2025-11-12
**Next Review**: After pilot completion
