# Use Case 06: Project Manager Assistant

## Overview

**Participant**: ศุภณัฐ เจนเจนจบเมธา (Suphanat Jenjenchobametha)
**Position**: วศ.9 (Engineer Level 9)
**ID**: 577278
**Department**: สำรอง (Reserve/Standby)

**Original Use Case**: agent ผู้ช่วย Project Manager

**English Description**: Automated workflow agent that pulls calendar bookings from Outlook for specific users, identifies development/project-related tasks, and automatically creates corresponding timesheet entries in OpenProject platform.

## Business Problem

EGAT project managers face daily administrative burdens:
- **Manual timesheet entry**: 15-30 minutes per day entering meeting time into OpenProject
- **Context switching**: Between Outlook (calendar) and OpenProject (timesheet)
- **Forgotten entries**: Meetings not logged, causing timesheet gaps
- **Duplicate work**: Same information in two systems (calendar + timesheet)
- **Time tracking accuracy**: Difficult to remember exact time spent after the fact
- **Monthly reconciliation**: Hours spent correcting timesheet vs calendar mismatches
- **Team compliance**: Ensuring all team members log time correctly

**Current Process Pain Points**:
- Check Outlook calendar for meetings
- Manually identify which meetings are project-related
- Open OpenProject for each project
- Create timesheet entries one by one
- Categorize time (meeting, development, review, etc.)
- Reconcile discrepancies at month-end
- Chase team members for missing entries

## Solution

An intelligent automation agent powered by Microsoft Copilot Studio that:
- **Syncs Outlook calendar** automatically for designated users
- **Identifies project tasks** using AI (keywords, attendees, calendar categories)
- **Creates OpenProject entries** automatically with proper categorization
- **Maps meetings to projects** based on participants, topics, or manual rules
- **Categorizes activity types**: Meeting, development, review, admin, etc.
- **Handles exceptions**: Asks for clarification when uncertain
- **Provides summaries**: Daily/weekly time allocation reports
- **Ensures compliance**: Alerts for missing or incomplete timesheets
- **Available conversationally** via Teams for queries and corrections

## Business Value

### Expected Benefits
- **Time Savings**: 15-30 minutes/day × 20 working days = 5-10 hours/month per PM
- **Accuracy**: Calendar is source of truth (no forgotten entries)
- **Compliance**: Automated tracking ensures complete timesheets
- **Reporting**: Real-time visibility into time allocation

### Qualitative Benefits
- Project managers focus on delivery vs admin
- Better project cost tracking (accurate hours)
- Improved team morale (less administrative burden)
- Data-driven resource planning
- Reduced month-end reconciliation effort

## Success Metrics

| Metric | Baseline | Target (3 months) | Target (6 months) |
|--------|----------|-------------------|-------------------|
| Daily timesheet entry time | 15-30 min | < 5 min | < 2 min |
| Timesheet completion rate | 70-80% | ≥ 95% | ≥ 98% |
| Calendar-timesheet mismatch | 15-20% | < 5% | < 2% |
| PM satisfaction | N/A | Collect feedback | ≥ 4.5/5.0 |
| Time saved per PM/month | 0 | 5-8 hours | 8-10 hours |

## Technical Architecture

```
┌─────────────────────────────────────────┐
│        Outlook Calendar                 │
│   (PM's meetings and appointments)      │
└──────────────┬──────────────────────────┘
               │ Microsoft Graph API
               ↓
┌─────────────────────────────────────────┐
│     Power Automate Flow                 │
│  - Trigger: New/updated calendar event  │
│  - Fetch event details                  │
└──────────────┬──────────────────────────┘
               │
               ↓
┌─────────────────────────────────────────┐
│    Copilot Studio Agent                 │
│  AI Classification Engine               │
│  - Is this project-related?             │
│  - Which project?                       │
│  - What activity type?                  │
│  - How many hours?                      │
└──────────────┬──────────────────────────┘
               │
               ├──→ SharePoint (project mapping rules)
               ├──→ Azure AD (user/team lookup)
               ├──→ Dataverse (classification history)
               │
               ↓
┌─────────────────────────────────────────┐
│       OpenProject API                   │
│  - Create timesheet entry               │
│  - Link to project & work package       │
│  - Set activity type                    │
│  - Record hours                         │
└──────────────┬──────────────────────────┘
               │
               ↓
┌─────────────────────────────────────────┐
│    Confirmation & Exceptions            │
│  - Teams notification (for review)      │
│  - Ask PM if unsure about classification│
│  - Daily summary report                 │
└─────────────────────────────────────────┘
```

## Prerequisites

### Access & Licenses
- [ ] Microsoft Copilot Studio license
- [ ] Power Platform environment access
- [ ] Power Automate Premium (for Outlook and API connectors)
- [ ] Microsoft 365 (Outlook, Teams, Graph API access)
- [ ] OpenProject account and API access
- [ ] Azure AD permissions for user lookup

### Technical Requirements
- [ ] Outlook calendar access (delegated or application permissions)
- [ ] OpenProject API token with write permissions
- [ ] SharePoint for project mapping configuration
- [ ] Network access between Power Platform and OpenProject
- [ ] Teams for notifications and interactions

### Knowledge Requirements
- [ ] Completion of Lab 01 and Lab 02
- [ ] Understanding of Outlook calendar structure
- [ ] Familiarity with OpenProject timesheet module
- [ ] Basic Power Automate flow creation
- [ ] API integration concepts

## Implementation Complexity

**Overall**: ⭐⭐⭐⭐⭐ (High)

**Breakdown**:
- Agent creation: ⭐⭐ (Medium)
- Outlook Calendar integration: ⭐⭐⭐ (Medium-High)
- OpenProject API integration: ⭐⭐⭐⭐ (High)
- AI classification logic: ⭐⭐⭐⭐ (High)
- Exception handling: ⭐⭐⭐ (Medium-High)
- Testing with real data: ⭐⭐⭐⭐ (High)
- Deployment: ⭐⭐ (Medium)

**Estimated Time**: 12-16 hours for initial implementation

## File Structure

```
06-project-manager-assistant/
├── README.md                           # This file - overview
├── implementation-guide.md             # Step-by-step instructions
├── connections-required.md             # Technical setup details
├── testing-guide.md                   # Testing procedures
├── openproject-integration.md         # OpenProject API guide
└── assets/
    ├── sample-queries.md              # Example PM interactions
    ├── classification-rules.md        # How to categorize activities
    ├── power-automate-flows.md        # Flow templates
    └── exception-handling.md          # Edge cases and solutions
```

## Quick Start

1. **Review Prerequisites** (above)
2. **Set up API access** (Outlook Graph API + OpenProject API)
3. **Define classification rules** (which meetings are project-related)
4. **Follow** [implementation-guide.md](./implementation-guide.md)
5. **Configure connections** per [connections-required.md](./connections-required.md)
6. **Test** using [testing-guide.md](./testing-guide.md)

## Related Use Cases

This implementation works with:
- **Use Case 04**: Timesheet Report Generator (creates reports from this data)
- **Resource Planning Assistant**: Uses time data for capacity planning
- **Project Dashboard**: Real-time project hours tracking

## Industry Context

Automated timesheet management provides value:
- Time savings: 60-80% reduction in manual entry
- Improved accuracy through calendar sync
- Better project cost tracking
- Enhanced compliance and audit trails

**Note**: Results depend on calendar discipline and classification rule quality.

## Key Differentiators

### vs Manual Timesheet Entry
- **90% faster** (automated vs manual)
- **Higher accuracy** (calendar is source of truth)
- **Complete data** (no forgotten entries)
- **Real-time sync** vs end-of-week batch entry

### vs Basic Calendar-Timesheet Sync Tools
- **AI-powered classification** (not just rule-based)
- **Conversational exceptions** (ask PM when unsure)
- **Context-aware** (learns from past classifications)
- **Integrated with M365** (native Outlook, Teams)

### vs Enterprise Time Tracking Systems
- **Much lower cost** ($2-3K vs $20-50K/year)
- **Faster implementation** (days vs months)
- **Works with existing tools** (Outlook + OpenProject)
- **Conversational interface** (Teams chat vs web forms)

## Example Usage

### Daily Summary Notification

**Teams Message (8:30 AM)**:
```
Good morning! ☀️

**Yesterday's Time Summary** (2025-11-11):
I've processed your calendar and created timesheet entries:

✅ **Auto-Created Entries** (7 meetings, 5.5 hours):
1. Project A - Status Meeting (1h) → OpenProject #1234
2. Project B - Technical Review (1.5h) → OpenProject #1235
3. Team Stand-up (0.5h) → Admin/General
4. Budget Review (1h) → Project A #1234
5. Client Meeting (1h) → Project C #1236
6. Sprint Planning (0.5h) → Project B #1235

⚠️ **Needs Your Input** (2 meetings):
7. "Q4 Strategy Discussion" (1h)
   → Which project should this be logged to?
   Options: [Project A] [Project B] [Admin] [Skip]

8. "Coffee with External Vendor" (0.5h)
   → Is this project-related or administrative?
   Options: [Project C] [Admin] [Skip]

📊 **Today's Allocation** (so far):
- Project A: 2 hours
- Project B: 2 hours
- Project C: 1 hour
- Admin: 0.5 hours
Total: 5.5 hours

⏰ **Calendar for Today** (2025-11-12):
- 6 meetings scheduled (5 hours)
- Will auto-create entries after each meeting
- Current week: 22.5 / 40 hours logged

[Review in OpenProject] [Adjust Classification] [Report Issue]
```

### Conversational Interaction

**PM asks in Teams**:
```
"How did I spend my time last week?"
```

**Agent responds**:
```
**Time Breakdown - Week of Nov 6-10, 2025**

**Total Hours Logged**: 42.5 hours

**By Project**:
- Project A (ERP Upgrade): 18 hours (42%)
  → Meetings: 8h, Development: 6h, Reviews: 4h
- Project B (Cloud Migration): 14 hours (33%)
  → Meetings: 5h, Planning: 6h, Technical work: 3h
- Project C (Security Audit): 7 hours (16%)
  → All meetings/coordination
- Administrative: 3.5 hours (8%)

**By Activity Type**:
- Meetings: 20 hours (47%)
- Development: 10 hours (24%)
- Reviews: 8 hours (19%)
- Planning: 4.5 hours (11%)

**Insights**:
⚠️ High meeting load (47%) - consider blocking focus time
✅ Project A on track (planned: 16h, actual: 18h)
⚠️ Project B slightly under (planned: 16h, actual: 14h)

**Comparison to Previous Week**:
📈 Total hours: +2.5 hours
📊 Meeting time: +3 hours (consider optimizing)
📉 Development time: -1 hour

Would you like:
- Export detailed report?
- Compare to project plan?
- Suggestions for next week?
```

### Exception Handling

**PM receives alert**:
```
🤔 **Classification Needed**

Meeting: "Workshop on New Technology"
Time: 2 hours
Attendees: John, Mary, External Consultant

I couldn't automatically classify this. Please help:

**Question 1**: Is this project-related?
[ ] Yes - Project work
[ ] No - Professional development
[ ] Administrative

**If project-related, Question 2**: Which project?
[ ] Project A
[ ] Project B
[ ] Project C
[ ] Other: ___________

**Question 3**: Activity type?
[ ] Meeting/Discussion
[ ] Training/Learning
[ ] Planning
[ ] Development

Your answers will help me learn for future similar meetings!

[Quick Answer] [Provide Details] [Skip This Entry]
```

## Next Steps

1. ✅ Review this README
2. ⏳ Set up Outlook Graph API access
3. ⏳ Set up OpenProject API access
4. ⏳ Define classification rules
5. ⏳ Follow [implementation-guide.md](./implementation-guide.md)
6. ⏳ Configure [connections](./connections-required.md)
7. ⏳ Test with pilot PM (1-2 weeks)
8. ⏳ Refine classification based on feedback
9. ⏳ Roll out to all PMs

---

**Status**: ✅ Ready for Implementation
**Priority**: 🔥 High (Significant PM Time Savings)
**Last Updated**: 2025-11-12
**Complexity Note**: This is the most complex use case - recommend starting with simpler ones first
