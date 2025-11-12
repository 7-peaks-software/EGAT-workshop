# Sample Queries: Timesheet Report Generator

## Overview

This document provides 80+ sample queries to test the Timesheet Report Generator agent across different scenarios and user needs.

---

## Query Categories

1. [Basic Report Generation](#1-basic-report-generation) (15 queries)
2. [Period Comparisons](#2-period-comparisons) (10 queries)
3. [Project-Specific Queries](#3-project-specific-queries) (12 queries)
4. [Team and Individual Analysis](#4-team-and-individual-analysis) (10 queries)
5. [Activity Type Analysis](#5-activity-type-analysis) (8 queries)
6. [Trend Analysis](#6-trend-analysis) (10 queries)
7. [Export and Sharing](#7-export-and-sharing) (8 queries)
8. [Troubleshooting and Help](#8-troubleshooting-and-help) (7 queries)
9. [Thai Language Queries](#9-thai-language-queries) (10 queries)

**Total**: 90 queries

---

## 1. Basic Report Generation

### English Queries

**Q1.1**: "Generate monthly timesheet report"
**Expected**: Agent asks for month/period

**Q1.2**: "Create timesheet summary for October 2025"
**Expected**: Generates full report for Oct 2025

**Q1.3**: "Show me last month's timesheet report"
**Expected**: Generates report for previous month

**Q1.4**: "I need the timesheet report for this month"
**Expected**: Generates report for current month

**Q1.5**: "Generate report for Q3 2025"
**Expected**: Asks which month in Q3 or generates quarterly summary

**Q1.6**: "Show timesheet data for September"
**Expected**: Asks for year, then generates Sep 2025 report

**Q1.7**: "Can you create a report for my team?"
**Expected**: Asks for period, then generates team-specific report

**Q1.8**: "Generate timesheet report for all projects"
**Expected**: Asks for month, generates comprehensive report

**Q1.9**: "Show me the latest timesheet report"
**Expected**: Generates report for most recent complete month

**Q1.10**: "Create summary for last 3 months"
**Expected**: Generates multi-month comparison or asks for specific months

**Q1.11**: "I want to see timesheet breakdown"
**Expected**: Asks for period, shows detailed breakdown

**Q1.12**: "Generate weekly timesheet summary"
**Expected**: Asks for week, generates weekly report

**Q1.13**: "Show hours logged in November"
**Expected**: Generates November report

**Q1.14**: "Create timesheet report for Project A"
**Expected**: Asks for period, generates project-specific report

**Q1.15**: "What's our timesheet status?"
**Expected**: Shows current month status and recent activity

---

## 2. Period Comparisons

### Comparative Analysis Queries

**Q2.1**: "Compare this month vs last month"
**Expected**: Shows side-by-side comparison with differences

**Q2.2**: "How do October hours compare to September?"
**Expected**: Detailed comparison with trends

**Q2.3**: "Show me month-over-month trends for Q3"
**Expected**: Trend analysis across Jul, Aug, Sep

**Q2.4**: "Compare this quarter to last quarter"
**Expected**: Quarterly comparison report

**Q2.5**: "Has our total hours increased or decreased?"
**Expected**: Trend analysis over recent months

**Q2.6**: "Compare October 2025 to October 2024"
**Expected**: Year-over-year comparison

**Q2.7**: "Show me the trend for Project A hours over last 6 months"
**Expected**: Project-specific trend analysis

**Q2.8**: "Are we logging more or less time than usual?"
**Expected**: Comparison to baseline/average

**Q2.9**: "Compare meeting hours across the last 3 months"
**Expected**: Activity-specific trend (meetings only)

**Q2.10**: "Show me how team utilization changed this year"
**Expected**: Utilization trend analysis

---

## 3. Project-Specific Queries

### Individual Project Analysis

**Q3.1**: "Show me all hours for Project A in October"
**Expected**: Project A breakdown with users, activities

**Q3.2**: "Which project consumed the most hours last month?"
**Expected**: Project ranking with top project highlighted

**Q3.3**: "How many hours were logged to the ERP Upgrade project?"
**Expected**: Specific project total (asks for period if not specified)

**Q3.4**: "Show me project breakdown by percentage"
**Expected**: Pie chart or table with percentages

**Q3.5**: "Which projects are we spending time on?"
**Expected**: List of active projects with hours

**Q3.6**: "Compare hours across all projects"
**Expected**: Comparative project analysis

**Q3.7**: "Is Project B over or under budget?"
**Expected**: Budget vs actual comparison (if budget data available)

**Q3.8**: "Show me projects with less than 50 hours logged"
**Expected**: Filter projects by hour threshold

**Q3.9**: "What percentage of time went to admin tasks?"
**Expected**: Admin/overhead percentage calculation

**Q3.10**: "Rank projects by total hours"
**Expected**: Sorted list, highest to lowest

**Q3.11**: "Show me hours for inactive projects"
**Expected**: Identifies projects with no recent activity

**Q3.12**: "Which projects had the biggest increase in hours?"
**Expected**: Project trend analysis showing growth

---

## 4. Team and Individual Analysis

### User-Level Queries

**Q4.1**: "Show me hours logged by each team member"
**Expected**: Per-user breakdown with totals

**Q4.2**: "Who logged the most hours last month?"
**Expected**: Top contributors ranking

**Q4.3**: "What's our team utilization rate?"
**Expected**: Utilization percentage calculation

**Q4.4**: "Show me my personal timesheet summary"
**Expected**: User's own hours (if personalized)

**Q4.5**: "Which team members are underutilized?"
**Expected**: Identifies users below target utilization

**Q4.6**: "Compare hours across team members"
**Expected**: Comparative analysis of team

**Q4.7**: "Show workload distribution"
**Expected**: Hours per person with balance analysis

**Q4.8**: "Who worked overtime last month?"
**Expected**: Identifies users > 40 hours/week

**Q4.9**: "Show me team capacity analysis"
**Expected**: Utilization vs capacity report

**Q4.10**: "Which users haven't logged time recently?"
**Expected**: Identifies non-compliant users

---

## 5. Activity Type Analysis

### Task Category Queries

**Q5.1**: "How much time was spent in meetings?"
**Expected**: Meeting hours total and percentage

**Q5.2**: "Show breakdown by activity type"
**Expected**: Table/chart of all activities

**Q5.3**: "What's the ratio of productive vs overhead time?"
**Expected**: Categorized analysis (dev/testing vs meetings/admin)

**Q5.4**: "How many hours of development work were logged?"
**Expected**: Development activity total

**Q5.5**: "Compare meeting hours vs last month"
**Expected**: Meeting-specific trend

**Q5.6**: "What percentage of time is spent on admin?"
**Expected**: Admin overhead percentage

**Q5.7**: "Show me all testing activity hours"
**Expected**: Testing activity breakdown

**Q5.8**: "Are we spending too much time in meetings?"
**Expected**: Meeting analysis with recommendations

---

## 6. Trend Analysis

### Historical and Predictive Queries

**Q6.1**: "Show me weekly trends for October"
**Expected**: Week-by-week breakdown within month

**Q6.2**: "What's the average hours per week?"
**Expected**: Weekly average calculation

**Q6.3**: "Are there any unusual patterns?"
**Expected**: Anomaly detection and highlights

**Q6.4**: "Show me peak activity days"
**Expected**: Day-of-week analysis

**Q6.5**: "What trends do you see in the data?"
**Expected**: AI-generated insights

**Q6.6**: "Predict hours for next month based on trends"
**Expected**: Forecast (if predictive logic enabled)

**Q6.7**: "Show me historical trends for last 12 months"
**Expected**: Long-term trend visualization

**Q6.8**: "Which weeks had the highest hours?"
**Expected**: Peak week identification

**Q6.9**: "Are we on track with our targets?"
**Expected**: Target vs actual comparison

**Q6.10**: "Show me capacity trends"
**Expected**: Utilization trend over time

---

## 7. Export and Sharing

### Report Distribution Queries

**Q7.1**: "Export this report to PDF"
**Expected**: Generates PDF download

**Q7.2**: "Send report to my email"
**Expected**: Emails report to user

**Q7.3**: "Save report to SharePoint"
**Expected**: Saves to designated SharePoint location

**Q7.4**: "Download as Excel"
**Expected**: Generates Excel file with data

**Q7.5**: "Create PowerPoint presentation"
**Expected**: Generates PPT with charts

**Q7.6**: "Share report with management team"
**Expected**: Asks for distribution method, then shares

**Q7.7**: "Post report to Teams channel"
**Expected**: Posts to specified Teams channel

**Q7.8**: "Schedule monthly report email"
**Expected**: Sets up automated email delivery

---

## 8. Troubleshooting and Help

### Support Queries

**Q8.1**: "Help"
**Expected**: Shows available commands and capabilities

**Q8.2**: "What can you do?"
**Expected**: Lists features and examples

**Q8.3**: "How do I generate a report?"
**Expected**: Step-by-step instructions

**Q8.4**: "Why is data missing for October?"
**Expected**: Troubleshoots data availability

**Q8.5**: "The numbers don't look right"
**Expected**: Offers to verify calculations or check data source

**Q8.6**: "Can you explain this metric?"
**Expected**: Defines utilization rate, overhead %, etc.

**Q8.7**: "What does 'team utilization rate' mean?"
**Expected**: Explanation of calculation

---

## 9. Thai Language Queries

### ภาษาไทย (Thai)

**Q9.1**: "สวัสดี"
**Expected**: Thai greeting + capabilities overview

**Q9.2**: "สร้างรายงานไทม์ชีท"
**Expected**: Asks for period (in Thai)

**Q9.3**: "แสดงรายงานเดือนตุลาคม"
**Expected**: Generates October report in Thai

**Q9.4**: "เปรียบเทียบเดือนนี้กับเดือนที่แล้ว"
**Expected**: Month comparison in Thai

**Q9.5**: "ช่วยเหลือ"
**Expected**: Help information in Thai

**Q9.6**: "โครงการไหนใช้เวลามากที่สุด"
**Expected**: Top project by hours (Thai)

**Q9.7**: "แสดงชั่วโมงทำงานของทีม"
**Expected**: Team hours breakdown (Thai)

**Q9.8**: "ส่งออกเป็น PDF"
**Expected**: PDF export (Thai confirmation)

**Q9.9**: "คำนวณอัตราการใช้งานทีม"
**Expected**: Utilization calculation (Thai)

**Q9.10**: "แนวโน้มชั่วโมงทำงานเป็นอย่างไร"
**Expected**: Trend analysis in Thai

---

## 10. Edge Cases and Complex Queries

### Advanced Scenarios

**Q10.1**: "Show me projects with zero hours logged"
**Expected**: Identifies inactive projects

**Q10.2**: "What if we removed all meeting time, how many productive hours?"
**Expected**: Hypothetical calculation

**Q10.3**: "Show me time entries with no project assigned"
**Expected**: Identifies orphaned entries

**Q10.4**: "Compare my team's hours to other teams"
**Expected**: Inter-team comparison (if data available)

**Q10.5**: "Generate report for custom date range: Oct 15 - Nov 15"
**Expected**: Custom period report

**Q10.6**: "Show me only billable hours"
**Expected**: Filters by billable category (if applicable)

**Q10.7**: "Which users logged time on weekends?"
**Expected**: Weekend activity analysis

**Q10.8**: "Show me multi-project users"
**Expected**: Users working on multiple projects

**Q10.9**: "Calculate cost based on hours (assume $50/hour)"
**Expected**: Cost calculation if rates available

**Q10.10**: "Identify time entry anomalies"
**Expected**: Flags unusually high/low entries

---

## Expected Response Formats

### Executive Summary Example

```
**October 2025 Timesheet Summary**

**Overview**:
- Total Hours: 1,247.5 hours
- Active Projects: 8
- Team Members: 15
- Utilization Rate: 78%

**Top 3 Projects**:
1. ERP Upgrade (520h, 42%)
2. Cloud Migration (380h, 30%)
3. Security Audit (240h, 19%)

**Activity Breakdown**:
- Meetings: 47% (587h)
- Development: 24% (300h)
- Reviews: 19% (237h)
- Other: 10% (124h)

**Key Insights**:
✅ Team utilization within target range (75-85%)
⚠️ High meeting load - consider optimization
✅ Major projects on track
```

### Comparison Example

```
**Comparison: October vs September 2025**

**Total Hours**:
- October: 1,247.5 hours
- September: 1,180.0 hours
- Change: +67.5 hours (+5.7%) 📈

**Project Changes**:
- ERP Upgrade: +35 hours (+7%)
- Cloud Migration: +20 hours (+5%)
- Security Audit: +12 hours (+5%)

**Activity Changes**:
- Meetings: +15 hours (+3%) ⚠️
- Development: +30 hours (+11%) ✅
- Reviews: +10 hours (+4%)

**Utilization Rate**:
- October: 78%
- September: 74%
- Improvement: +4% ✅
```

---

## Query Testing Checklist

Use this checklist to systematically test agent:

### Basic Functionality
- [ ] Q1.1-Q1.15: All basic queries work
- [ ] Reports generate < 2 minutes
- [ ] Data accuracy verified

### Comparisons
- [ ] Q2.1-Q2.10: Comparison logic correct
- [ ] Trends calculated accurately
- [ ] Visualizations clear

### Filtering
- [ ] Q3.1-Q3.12: Project filters work
- [ ] Q4.1-Q4.10: User filters work
- [ ] Q5.1-Q5.8: Activity filters work

### Advanced
- [ ] Q6.1-Q6.10: Trend analysis accurate
- [ ] Q7.1-Q7.8: Export functions work
- [ ] Q8.1-Q8.7: Help system useful

### Bilingual
- [ ] Q9.1-Q9.10: Thai queries work
- [ ] Mixed language handled gracefully

### Edge Cases
- [ ] Q10.1-Q10.10: Complex scenarios handled

**Overall Pass Rate Target**: ≥ 95% (86/90 queries)

---

## Sample Conversation Flow

**Complete interaction example**:

```
User: "Hi, I need a timesheet report"

Agent: "Hello! I can help you generate timesheet reports from OpenProject.

What period would you like to report on?

Options:
- Current month
- Last month
- Specific month (tell me which)
- Custom date range"

User: "Last month"

Agent: "Generating timesheet report for October 2025...

⏳ Fetching data from OpenProject...
⏳ Processing 1,247 time entries...
⏳ Calculating metrics...

✅ Report ready!

**EXECUTIVE SUMMARY**
[Full report content]

Would you like:
- Detailed project breakdown?
- Team analysis?
- Export report?
- Compare to another period?"

User: "Compare to September"

Agent: "[Generates comparison]

**COMPARISON: October vs September**
[Comparison content]

Anything else you'd like to analyze?"

User: "Export to PDF"

Agent: "✅ PDF generated successfully!

[Download PDF] button

Report saved to: SharePoint → Timesheet Reports → Oct_2025_Report.pdf

Is there anything else I can help with?"

User: "No, thanks"

Agent: "You're welcome! Feel free to ask anytime you need timesheet insights. 👍"
```

---

**Document Version**: 1.0
**Last Updated**: 2025-11-12
**Total Queries**: 90+
