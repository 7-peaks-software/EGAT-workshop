# Use Case 01: Document Inquiry Chatbot

## Overview

**Participant**: ธนิตศร ไชยวุฒิ (Thanitsorn Chaiyawut)
**Position**: วศ.4 (Engineer Level 4)
**ID**: 599326
**Department**: กฟผ-พ. (Power Plant Division)

**Original Use Case**: Chatbot agent สอบถามข้อมูลจากเอกสาร

**English Description**: AI chatbot that EGAT staff can use to inquiry and find a site or document reference.

## Business Problem

EGAT employees frequently need to:
- Find specific documents across multiple repositories
- Search for policies, procedures, and guidelines
- Locate reference materials quickly
- Get answers from technical documentation
- Access historical project documents

**Current Challenges**:
- Documents scattered across SharePoint, Teams, file shares
- Time-consuming manual searches
- Difficulty finding exact information within large documents
- Knowledge silos - information locked in specific team folders
- New employees struggle to find resources

## Solution

A conversational AI agent powered by Microsoft Copilot Studio that:
- Indexes documents from multiple sources (SharePoint, OneDrive, file uploads)
- Allows natural language queries to find documents and information
- Provides relevant excerpts with source citations
- Suggests related documents
- Available 24/7 via Teams, web, or mobile

## Business Value

### Expected Benefits
- **Efficiency**: Reduce average time to locate documents and information
- **Accessibility**: 24/7 self-service access to organizational knowledge
- **Consistency**: Standardized approach to finding information across departments
- **Employee Experience**: Reduced dependency on manual searches and helpdesk

### Qualitative Benefits
- Improved knowledge sharing across departments
- Faster onboarding for new employees
- Better compliance (easier to find policies)
- Reduced bottlenecks from information requests

## Success Metrics

| Metric | Baseline | Target (3 months) | Target (6 months) |
|--------|----------|-------------------|-------------------|
| Document queries handled | N/A | Track usage | Establish benchmark |
| Documents indexed | 0 | 500+ | 1,000+ |
| Active users (monthly) | 0 | 50-100 | 200-300 |
| User satisfaction | N/A | Collect feedback | ≥ 4.0/5.0 |
| Successful resolutions | N/A | Measure & optimize | ≥ 70% |

## Technical Architecture

```
┌─────────────────┐
│   User Query    │
│  (Teams/Web)    │
└────────┬────────┘
         │
         ↓
┌─────────────────┐
│ Copilot Studio  │
│     Agent       │
└────────┬────────┘
         │
         ├──→ SharePoint Knowledge Source
         ├──→ OneDrive Knowledge Source
         ├──→ Uploaded Documents
         └──→ Web URLs (optional)
         │
         ↓
┌─────────────────┐
│ Generative AI   │
│   Response      │
└────────┬────────┘
         │
         ↓
┌─────────────────┐
│ User gets:      │
│ - Answer        │
│ - Source docs   │
│ - Related refs  │
└─────────────────┘
```

## Prerequisites

### Access & Licenses
- [ ] Microsoft Copilot Studio license
- [ ] Power Platform environment access
- [ ] SharePoint Online access
- [ ] Microsoft Teams (for deployment)

### Technical Requirements
- [ ] SharePoint site(s) with documents
- [ ] Permissions to access document libraries
- [ ] Azure AD authentication configured

### Knowledge Requirements
- [ ] Completion of Lab 01 (Basic agent creation)
- [ ] Completion of Lab 02 (Knowledge sources)
- [ ] Understanding of SharePoint structure

## Implementation Complexity

**Overall**: ⭐⭐ (Easy to Medium)

**Breakdown**:
- Agent creation: ⭐ (Easy)
- Knowledge source setup: ⭐⭐ (Medium)
- Testing and refinement: ⭐⭐ (Medium)
- Deployment: ⭐ (Easy)

**Estimated Time**: 4-6 hours for initial implementation

## File Structure

```
01-document-inquiry-chatbot/
├── README.md                           # This file - overview
├── implementation-guide.md             # Step-by-step instructions
├── connections-required.md             # Technical setup details
├── testing-guide.md                   # Testing procedures
├── copilot-studio-vs-m365-copilot.md  # Comparison guide
└── assets/
    ├── sample-queries.md              # Example questions to test
    └── sharepoint-setup.md            # SharePoint configuration guide
```

## Quick Start

1. **Understand the decision**: Review [Copilot Studio vs M365 Copilot](./copilot-studio-vs-m365-copilot.md)
2. **Review Prerequisites** (above)
3. **Follow** [implementation-guide.md](./implementation-guide.md)
4. **Configure connections** per [connections-required.md](./connections-required.md)
5. **Test** using [testing-guide.md](./testing-guide.md)

## Related Use Cases

This implementation can be extended for:
- **HR Recruitment Assistant** - Searching CV/Resume documents
- **Project Documentation Search** - Finding project-specific files
- **Compliance Document Assistant** - Locating policies and regulations

## Industry Context

Document search and knowledge management solutions have shown measurable value across organizations:
- Reduced time spent searching for information (typical range: 30-50% improvement)
- Decreased dependency on IT/support teams for document location
- Improved employee onboarding efficiency
- Better compliance through easier policy access

**Note**: Actual results will vary based on EGAT's specific implementation, document organization, and user adoption.

## Next Steps

1. ✅ Review this README
2. ⏳ Start with [implementation-guide.md](./implementation-guide.md)
3. ⏳ Configure [connections](./connections-required.md)
4. ⏳ Test thoroughly using [testing-guide.md](./testing-guide.md)
5. ⏳ Deploy to production
6. ⏳ Monitor and optimize

---

**Status**: ✅ Ready for Implementation
**Priority**: 🔥 High (Quick Win)
**Last Updated**: 2025-11-12
