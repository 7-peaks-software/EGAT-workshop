# Use Case 02: HR Recruitment Assistant

## Overview

**Participant**: กฤตณณ์ คีรีทรัพย์ (Kriton Kireesat)
**Position**: นค.7 (Computer Technician Level 7)
**ID**: 595338
**Department**: กบคา-พ. (Human Resources Planning Division)

**Original Use Case**: ตรวจสอบและจัดการข้อมูลเอกสารสมัครงาน

**English Description**: AI Agent to help recruiters summarize all information from candidate CVs/Resumes and find/match suitable candidates for open positions.

## Business Problem

EGAT's HR recruitment team faces challenges when processing job applications:
- **High volume**: 50-200 applications per position
- **Manual screening**: Recruiters spend 3-5 minutes per CV
- **Inconsistent evaluation**: Different reviewers may assess differently
- **Time to shortlist**: 2-3 days to create initial shortlist
- **Information extraction**: Manual copying of candidate details
- **Candidate matching**: Difficult to compare candidates objectively

**Current Process Pain Points**:
- Manually reading each CV/Resume
- Extracting key information into spreadsheets
- Comparing candidates across multiple criteria
- Missing qualified candidates due to volume
- Slow response time to candidates

## Solution

A conversational AI agent powered by Microsoft Copilot Studio that:
- **Analyzes CVs/Resumes** automatically (PDF, Word, uploaded files)
- **Extracts key information**: Education, experience, skills, certifications
- **Matches candidates** to job requirements
- **Generates summaries** for each candidate
- **Creates comparison tables** for shortlisting
- **Suggests top candidates** based on criteria
- **Available 24/7** via Teams or web interface

## Business Value

### Expected Benefits
- **Time Savings**: Reduce initial screening time by 60-70%
- **Consistency**: Standardized evaluation criteria across all candidates
- **Quality**: Identify qualified candidates more reliably
- **Speed**: Create shortlists in hours instead of days

### Qualitative Benefits
- Improved candidate experience (faster response)
- Better hiring decisions (data-driven)
- Reduced recruiter fatigue
- More time for interviews and candidate engagement
- Audit trail of evaluation criteria

## Success Metrics

| Metric | Baseline | Target (3 months) | Target (6 months) |
|--------|----------|-------------------|-------------------|
| CV screening time | 3-5 min/CV | < 1 min/CV | < 30 sec/CV |
| Time to shortlist | 2-3 days | < 1 day | < 4 hours |
| Candidate satisfaction | N/A | Collect feedback | ≥ 4.0/5.0 |
| Qualified candidate miss rate | N/A | Measure & optimize | < 5% |
| Recruiter satisfaction | N/A | Collect feedback | ≥ 4.5/5.0 |

## Technical Architecture

```
┌─────────────────┐
│  HR Recruiter   │
│   (Teams/Web)   │
└────────┬────────┘
         │
         ↓
┌─────────────────┐
│ Copilot Studio  │
│  HR Assistant   │
└────────┬────────┘
         │
         ├──→ SharePoint (Job Descriptions)
         ├──→ Uploaded CVs/Resumes
         ├──→ OneDrive (Application folders)
         ├──→ Azure AI Document Intelligence (OCR)
         │
         ↓
┌─────────────────┐
│ AI Processing   │
│ - Extract info  │
│ - Match criteria│
│ - Score/Rank    │
└────────┬────────┘
         │
         ↓
┌─────────────────┐
│ Recruiter gets: │
│ - Summary       │
│ - Match score   │
│ - Comparison    │
│ - Shortlist     │
└─────────────────┘
```

## Prerequisites

### Access & Licenses
- [ ] Microsoft Copilot Studio license
- [ ] Power Platform environment access
- [ ] SharePoint Online access
- [ ] Azure AI Document Intelligence (optional, for better OCR)
- [ ] Microsoft Teams (for deployment)

### Technical Requirements
- [ ] SharePoint site for job descriptions
- [ ] OneDrive/SharePoint for CV storage
- [ ] Azure AD authentication configured
- [ ] File upload capability enabled

### Knowledge Requirements
- [ ] Completion of Lab 01 (Basic agent creation)
- [ ] Completion of Lab 02 (Knowledge sources)
- [ ] Understanding of Use Case 01 (Document search foundation)

## Implementation Complexity

**Overall**: ⭐⭐⭐ (Medium)

**Breakdown**:
- Agent creation: ⭐ (Easy)
- Document extraction setup: ⭐⭐⭐ (Medium-High)
- Matching logic: ⭐⭐⭐ (Medium-High)
- Testing and refinement: ⭐⭐ (Medium)
- Deployment: ⭐ (Easy)

**Estimated Time**: 6-8 hours for initial implementation

## File Structure

```
02-hr-recruitment-assistant/
├── README.md                           # This file - overview
├── implementation-guide.md             # Step-by-step instructions
├── connections-required.md             # Technical setup details
├── testing-guide.md                   # Testing procedures
├── comparison-with-alternatives.md     # Compare with ATS systems
└── assets/
    ├── sample-queries.md              # Example questions to test
    ├── sample-job-description.md      # Example JD template
    └── cv-evaluation-criteria.md      # Standard evaluation framework
```

## Quick Start

1. **Review Prerequisites** (above)
2. **Follow** [implementation-guide.md](./implementation-guide.md)
3. **Configure connections** per [connections-required.md](./connections-required.md)
4. **Test** using [testing-guide.md](./testing-guide.md)
5. **Review alternatives** in [comparison-with-alternatives.md](./comparison-with-alternatives.md)

## Related Use Cases

This implementation extends:
- **Use Case 01**: Document Inquiry (CV search instead of general documents)

Can be combined with:
- **Onboarding Assistant**: Transition from recruitment to onboarding
- **Employee Database Search**: Find existing employees with specific skills

## Industry Context

AI-powered recruitment tools have shown measurable value:
- Reduction in time-to-hire (typical: 30-50% improvement)
- Increased quality of hire through consistent evaluation
- Better candidate experience with faster responses
- Reduced unconscious bias in initial screening

**Note**: Results depend on EGAT's specific implementation, data quality, and job descriptions.

## Key Differentiators

### vs Traditional ATS (Applicant Tracking System)
- **Conversational interface** instead of complex forms
- **AI-powered matching** vs keyword search
- **Natural language queries** vs structured filters
- **Faster setup** (hours vs months)
- **Lower cost** for EGAT's use case

### vs Manual Screening
- **60-70% faster** initial screening
- **Consistent criteria** application
- **No fatigue** effect
- **Searchable history** of all candidates
- **Audit trail** for compliance

## Next Steps

1. ✅ Review this README
2. ⏳ Start with [implementation-guide.md](./implementation-guide.md)
3. ⏳ Configure [connections](./connections-required.md)
4. ⏳ Test thoroughly using [testing-guide.md](./testing-guide.md)
5. ⏳ Deploy to HR team
6. ⏳ Monitor and optimize

---

**Status**: ✅ Ready for Implementation
**Priority**: 🔥 High (Immediate Business Value)
**Last Updated**: 2025-11-12
