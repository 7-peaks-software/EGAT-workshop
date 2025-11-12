# Use Case 03: Test Case Generator

## Overview

**Participant**: ปริศนา ชัยยา (Prisana Chaiya)
**Position**: วศ.8 (Engineer Level 8)
**ID**: 590926
**Department**: กฟผ-พ. (Power Plant Division)

**Original Use Case**: Agent ช่วย Generate Test Case จาก Requirement / User Story Flow

**English Description**: AI Agent to help QA teams generate comprehensive test cases automatically from project documentation including Requirements, User Story Flows, and specifications.

## Business Problem

EGAT's QA and testing teams face challenges when creating test cases:
- **Time-consuming manual work**: 2-4 hours to write test cases per user story
- **Inconsistent coverage**: Different testers create varying quality of test cases
- **Missing edge cases**: Easy to overlook boundary conditions and error scenarios
- **Repetitive work**: Similar functionality requires rewriting similar tests
- **Documentation lag**: Test cases written after development starts
- **Knowledge dependency**: Junior testers struggle to identify all scenarios

**Current Process Pain Points**:
- Reading lengthy requirement documents
- Manually identifying test scenarios
- Writing test cases in standard format
- Ensuring comprehensive coverage (happy path + negative scenarios)
- Maintaining test case documentation
- Updating test cases when requirements change

## Solution

A conversational AI agent powered by Microsoft Copilot Studio that:
- **Analyzes requirements** from various sources (Word, PDF, user stories, flow diagrams)
- **Generates test cases** automatically with comprehensive coverage
- **Identifies scenarios**: Happy path, negative scenarios, boundary conditions, edge cases
- **Follows standards**: Uses EGAT's test case template format
- **Suggests test data**: Provides sample input values
- **Creates test matrices**: Organizes test cases by priority and type
- **Updates existing cases**: Modifies test cases when requirements change
- **Available 24/7** via Teams or web interface

## Business Value

### Expected Benefits
- **Time Savings**: Reduce test case creation time by 60-70%
- **Quality**: More comprehensive test coverage
- **Consistency**: Standardized test case format across teams
- **Speed**: Faster time from requirements to testing

### Qualitative Benefits
- Improved software quality (better test coverage)
- Faster onboarding for new QA team members
- More time for exploratory testing and test execution
- Better requirements clarity (AI asks clarifying questions)
- Reduced defect leakage to production

## Success Metrics

| Metric | Baseline | Target (3 months) | Target (6 months) |
|--------|----------|-------------------|-------------------|
| Test case creation time | 2-4 hrs/story | < 1 hr/story | < 30 min/story |
| Test coverage completeness | Variable | ≥ 90% | ≥ 95% |
| QA team satisfaction | N/A | Collect feedback | ≥ 4.0/5.0 |
| Defects found in testing | N/A | Establish baseline | +20% increase |
| Test case reusability | N/A | Measure | ≥ 30% reuse |

## Technical Architecture

```
┌─────────────────┐
│   QA Engineer   │
│   (Teams/Web)   │
└────────┬────────┘
         │
         ↓
┌─────────────────┐
│ Copilot Studio  │
│  Test Case Gen  │
└────────┬────────┘
         │
         ├──→ Requirements (Word/PDF/SharePoint)
         ├──→ User Stories (Azure DevOps/SharePoint)
         ├──→ Flow Diagrams (Visio/Images)
         ├──→ Test Case Templates (SharePoint)
         │
         ↓
┌─────────────────┐
│ AI Processing   │
│ - Parse docs    │
│ - Identify cases│
│ - Generate tests│
└────────┬────────┘
         │
         ↓
┌─────────────────┐
│ QA gets:        │
│ - Test cases    │
│ - Test data     │
│ - Coverage matrix│
│ - Export to Excel│
└─────────────────┘
```

## Prerequisites

### Access & Licenses
- [ ] Microsoft Copilot Studio license
- [ ] Power Platform environment access
- [ ] SharePoint Online access
- [ ] Azure DevOps access (if using for user stories)
- [ ] Microsoft Teams (for deployment)

### Technical Requirements
- [ ] SharePoint site for requirements documents
- [ ] Test case template (Excel/Word)
- [ ] Access to requirement repositories
- [ ] Azure AD authentication configured

### Knowledge Requirements
- [ ] Completion of Lab 01 (Basic agent creation)
- [ ] Completion of Lab 02 (Knowledge sources)
- [ ] Understanding of Use Case 01 (Document search foundation)
- [ ] Basic understanding of software testing concepts

## Implementation Complexity

**Overall**: ⭐⭐⭐ (Medium to Medium-High)

**Breakdown**:
- Agent creation: ⭐ (Easy)
- Document parsing setup: ⭐⭐ (Medium)
- Test case generation logic: ⭐⭐⭐ (Medium-High)
- Testing and refinement: ⭐⭐⭐ (Medium-High)
- Deployment: ⭐ (Easy)

**Estimated Time**: 6-8 hours for initial implementation

## File Structure

```
03-test-case-generator/
├── README.md                           # This file - overview
├── implementation-guide.md             # Step-by-step instructions
├── connections-required.md             # Technical setup details
├── testing-guide.md                   # Testing procedures
├── test-case-best-practices.md        # QA standards and templates
└── assets/
    ├── sample-queries.md              # Example prompts
    ├── sample-requirement.md          # Example requirement doc
    ├── sample-test-cases.md           # Example generated output
    └── test-case-template.md          # Standard template format
```

## Quick Start

1. **Review Prerequisites** (above)
2. **Follow** [implementation-guide.md](./implementation-guide.md)
3. **Configure connections** per [connections-required.md](./connections-required.md)
4. **Test** using [testing-guide.md](./testing-guide.md)
5. **Review standards** in [test-case-best-practices.md](./test-case-best-practices.md)

## Related Use Cases

This implementation extends:
- **Use Case 01**: Document Inquiry (requirement search)

Can be combined with:
- **Defect Management Assistant**: Link test cases to bug reports
- **Test Automation Helper**: Convert manual test cases to automated scripts
- **Requirements Traceability**: Map requirements → test cases → defects

## Industry Context

AI-powered test case generation has shown measurable value:
- Reduction in test case creation time (typical: 50-70% improvement)
- Improved test coverage through AI-identified edge cases
- Better consistency in test documentation
- Faster adaptation to requirement changes

**Note**: Results depend on requirement quality, testing standards, and team adoption.

## Key Differentiators

### vs Manual Test Case Writing
- **60-70% faster** initial creation
- **More comprehensive** coverage (AI identifies edge cases humans miss)
- **Consistent format** across all test cases
- **Easier maintenance** when requirements change

### vs Test Case Management Tools
- **Conversational interface** instead of forms
- **AI-powered generation** vs manual entry
- **Natural language** vs structured input
- **Integrated with existing docs** vs separate system

### vs Commercial Test Case Generators
- **Customizable** to EGAT standards
- **Lower cost** ($1-3K vs $10-20K/year)
- **Integrated with M365** tools already used
- **Flexible** matching logic and formats

## Example Usage

**QA Engineer asks**:
```
"Generate test cases for the User Login feature from
the requirement document in SharePoint"
```

**Agent responds**:
```
I've analyzed the User Login requirements and generated
15 test cases covering:

**Happy Path (5 test cases)**:
1. Valid username and password
2. Remember me functionality
3. Successful login redirect
4. Session management
5. Logout functionality

**Negative Scenarios (6 test cases)**:
6. Invalid username
7. Invalid password
8. Empty credentials
9. SQL injection attempt
10. Account locked after 3 failed attempts
11. Expired session handling

**Boundary Conditions (4 test cases)**:
12. Maximum password length
13. Minimum password length
14. Special characters in username
15. Concurrent login sessions

Would you like me to:
- Generate detailed steps for each test case?
- Create test data sets?
- Export to Excel format?
- Add more edge case scenarios?
```

## Next Steps

1. ✅ Review this README
2. ⏳ Start with [implementation-guide.md](./implementation-guide.md)
3. ⏳ Configure [connections](./connections-required.md)
4. ⏳ Test thoroughly using [testing-guide.md](./testing-guide.md)
5. ⏳ Review [best practices](./test-case-best-practices.md)
6. ⏳ Deploy to QA team
7. ⏳ Monitor and optimize

---

**Status**: ✅ Ready for Implementation
**Priority**: 🔥 High (Significant QA Efficiency Gain)
**Last Updated**: 2025-11-12
