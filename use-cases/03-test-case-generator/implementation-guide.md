# Implementation Guide: Test Case Generator

## Table of Contents

1. [Prerequisites Check](#prerequisites-check)
2. [Phase 1: Requirements Repository Setup](#phase-1-requirements-repository-setup)
3. [Phase 2: Test Case Template Preparation](#phase-2-test-case-template-preparation)
4. [Phase 3: Agent Creation](#phase-3-agent-creation)
5. [Phase 4: Document Processing Configuration](#phase-4-document-processing-configuration)
6. [Phase 5: Test Case Generation Logic](#phase-5-test-case-generation-logic)
7. [Phase 6: Testing](#phase-6-testing)
8. [Phase 7: Deployment](#phase-7-deployment)
9. [Phase 8: Monitoring & Optimization](#phase-8-monitoring--optimization)

**Estimated Total Time**: 6-8 hours

---

## Prerequisites Check

Before starting, verify you have completed:

- [ ] Reviewed [README.md](./README.md)
- [ ] Reviewed [connections-required.md](./connections-required.md)
- [ ] Access to Microsoft Copilot Studio
- [ ] Access to SharePoint sites for requirements
- [ ] Completed Lab 01 and Lab 02 from this workshop
- [ ] Familiarity with Use Case 01 (Document search)
- [ ] Copilot Studio license activated
- [ ] Basic QA/testing knowledge

---

## Phase 1: Requirements Repository Setup

**Time**: 1 hour

### Step 1.1: Create SharePoint Structure

1. **Create SharePoint Site** (or use existing project site)
   ```
   Site name: EGAT Software Projects
   URL: https://egat.sharepoint.com/sites/SoftwareProjects
   ```

2. **Create Document Libraries**:
   ```
   /sites/SoftwareProjects
     ├── Requirements/
     │   ├── Functional-Requirements/
     │   ├── User-Stories/
     │   ├── Flow-Diagrams/
     │   └── Technical-Specifications/
     ├── Test-Cases/
     │   ├── Generated/
     │   ├── Reviewed/
     │   └── Templates/
     └── Project-Documentation/
         └── [Various project docs]
   ```

3. **Add Metadata Columns** to Requirements library:
   - **Project Name** (Text)
   - **Feature Name** (Text)
   - **Requirement ID** (Text)
   - **Priority** (Choice: High, Medium, Low)
   - **Status** (Choice: Draft, Approved, In Development, Testing)
   - **Test Case Generated** (Yes/No)
   - **Last Modified** (Date - automatic)

### Step 1.2: Organize Requirement Documents

Best practices for requirements organization:

**Standard Format for Requirements**:
```markdown
# Feature: [Feature Name]
**Requirement ID**: REQ-001
**Priority**: High
**Status**: Approved

## Description
[Clear description of what the feature should do]

## User Stories
As a [user type], I want to [action], so that [benefit]

## Acceptance Criteria
1. [Criterion 1]
2. [Criterion 2]
3. [Criterion 3]

## Business Rules
- [Rule 1]
- [Rule 2]

## UI/UX Requirements
[If applicable]

## Technical Constraints
[Any technical limitations]

## Dependencies
[Related features or systems]
```

### Step 1.3: Upload Sample Requirements

For testing, prepare at least 5 requirements across different types:
- [ ] Simple CRUD operation (e.g., Create User)
- [ ] Complex workflow (e.g., Approval Process)
- [ ] Integration requirement (e.g., API integration)
- [ ] Report generation
- [ ] Security feature (e.g., Authentication)

---

## Phase 2: Test Case Template Preparation

**Time**: 45 minutes

### Step 2.1: Define Test Case Format

Create standard EGAT test case template:

```markdown
## Test Case ID: TC-[Project]-[Feature]-[Number]

**Test Case Title**: [Clear, descriptive title]
**Related Requirement**: [REQ-ID]
**Priority**: [High/Medium/Low]
**Test Type**: [Functional/Integration/Regression/Security/Performance]
**Test Level**: [Unit/Integration/System/Acceptance]

### Preconditions
- [Precondition 1]
- [Precondition 2]

### Test Steps
| Step | Action | Expected Result |
|------|--------|-----------------|
| 1 | [Action to perform] | [What should happen] |
| 2 | [Action to perform] | [What should happen] |
| 3 | [Action to perform] | [What should happen] |

### Test Data
| Field | Value | Notes |
|-------|-------|-------|
| [Field name] | [Test value] | [Any notes] |

### Expected Outcome
[Overall expected result]

### Post-conditions
- [Post-condition 1]
- [Post-condition 2]

### Notes
[Any additional information]
```

### Step 2.2: Create Test Case Categories

Define standard categories:

**By Scenario Type**:
- Happy Path (positive testing)
- Negative Scenarios (error handling)
- Boundary Conditions (min/max values)
- Edge Cases (unusual but valid scenarios)
- Security Testing (vulnerabilities)
- Performance Testing (load/stress)

**By Priority**:
- P0: Critical (must pass)
- P1: High (should pass)
- P2: Medium (nice to have)
- P3: Low (optional)

### Step 2.3: Upload Template to SharePoint

1. Save template as `Test-Case-Template.md`
2. Upload to `Test-Cases/Templates/` folder
3. Make it accessible to AI agent as knowledge source

---

## Phase 3: Agent Creation

**Time**: 45 minutes

### Step 3.1: Create New Agent

1. Navigate to [Microsoft Copilot Studio](https://copilotstudio.microsoft.com/)

2. Click **+ New agent**

3. Configure:
   ```
   Name: EGAT Test Case Generator
   Description: AI assistant to generate comprehensive test cases from requirements
   Language: Thai and English
   Icon: Upload testing/QA icon
   ```

4. Click **Create**

### Step 3.2: Configure Agent Instructions

Add detailed instructions:

```
You are the EGAT Test Case Generator. Your role is to help QA engineers create
comprehensive, high-quality test cases from requirement documents.

Core Capabilities:
1. Analyze requirement documents (functional specs, user stories, flow diagrams)
2. Generate comprehensive test cases covering multiple scenarios
3. Identify happy path, negative scenarios, boundary conditions, and edge cases
4. Follow EGAT's standard test case template format
5. Suggest appropriate test data
6. Create test coverage matrices
7. Update existing test cases when requirements change

Test Case Generation Principles:
1. COMPREHENSIVE COVERAGE
   - Always include happy path scenarios (normal, expected flow)
   - Include negative scenarios (invalid inputs, error conditions)
   - Include boundary conditions (min/max values, limits)
   - Include edge cases (unusual but valid scenarios)
   - Consider security aspects (injection, unauthorized access)
   - Consider performance aspects (if applicable)

2. CLEAR AND SPECIFIC
   - Each test case should have one clear objective
   - Steps should be detailed and actionable
   - Expected results should be specific and measurable
   - Test data should be realistic and relevant

3. FOLLOW STANDARDS
   - Use EGAT test case template format
   - Include all required fields (ID, Title, Priority, Type, etc.)
   - Use consistent naming conventions
   - Proper categorization by type and priority

4. QUALITY OVER QUANTITY
   - Avoid redundant test cases
   - Each test case should add value
   - Combine similar scenarios when appropriate
   - Prioritize based on risk and impact

Test Case Structure:
For each requirement, generate:
- 2-3 Happy Path test cases (normal flow)
- 3-5 Negative Scenarios (error handling)
- 2-3 Boundary Conditions (edge values)
- 1-2 Edge Cases (unusual scenarios)
- Additional security/performance tests if relevant

When analyzing requirements:
1. Extract key functionality and acceptance criteria
2. Identify input fields and their constraints
3. Understand business rules and validation logic
4. Note dependencies and preconditions
5. Consider user roles and permissions
6. Think about integration points
7. Identify potential failure points

Response Format:
- Start with summary: "I've analyzed [Requirement] and identified X test scenarios"
- Group test cases by category (Happy Path, Negative, Boundary, etc.)
- Number test cases sequentially
- Use tables for test steps when appropriate
- Provide test data suggestions
- Offer to export in different formats

Bilingual Support:
- Accept requirements in Thai or English
- Generate test cases in the same language as requirements
- Support mixed-language scenarios
```

### Step 3.3: Add Conversation Starters

```
1. "Generate test cases for [Feature Name]"
2. "Create test cases from this requirement document"
3. "What test scenarios should I cover for [Feature]?"
4. "Update test cases for changed requirements"
5. "Generate negative test scenarios for [Feature]"
```

---

## Phase 4: Document Processing Configuration

**Time**: 1-2 hours

### Step 4.1: Add Requirements as Knowledge Source

1. Go to **Knowledge** → **+ Add knowledge**

2. Select **SharePoint**

3. Configure:
   ```
   Type: Document Library
   Site: https://egat.sharepoint.com/sites/SoftwareProjects
   Library: Requirements
   ```

4. Click **Add** and wait for indexing

### Step 4.2: Add Test Case Template

1. **+ Add knowledge** → **SharePoint**

2. Add Test Case Templates library:
   ```
   Library: Test-Cases/Templates
   ```

3. This allows agent to reference standard format

### Step 4.3: Add Azure DevOps Integration (Optional)

If EGAT uses Azure DevOps for user stories:

1. Go to **Settings** → **Connections**

2. Add **Azure DevOps** connector

3. Authenticate with Azure DevOps credentials

4. Configure access to relevant projects

5. Agent can now query user stories directly from Azure DevOps

---

## Phase 5: Test Case Generation Logic

**Time**: 2-3 hours

### Step 5.1: Create "Generate Test Cases" Topic

1. Create new topic: **Generate Test Cases from Requirement**

2. Add trigger phrases:
   ```
   - "Generate test cases for [feature/requirement]"
   - "Create tests from this requirement"
   - "I need test cases for [feature]"
   - "Build test suite for [requirement]"
   ```

3. **Flow Design**:

   **Node 1: Get Requirement**
   ```
   Question: "Please provide the requirement. You can:
   1. Upload a document
   2. Provide a requirement ID
   3. Paste the requirement text
   4. Provide a SharePoint link"

   Options: [Upload File] [Enter Text] [Requirement ID]
   ```
   Save to: `Topic.RequirementInput`

   **Node 2: Parse Requirement** (if file/ID provided)
   ```
   Use Generative Answers node:

   Prompt: "Extract the following from the requirement document:

   1. Feature/Function name
   2. Description/Purpose
   3. User stories or use cases
   4. Acceptance criteria
   5. Business rules
   6. Input fields and validation rules
   7. Expected outputs
   8. Error conditions
   9. Dependencies
   10. Any constraints or limitations

   Requirement: {Topic.RequirementInput}"

   Data sources: Requirements library
   ```
   Save to: `Topic.ParsedRequirement`

   **Node 3: Identify Test Scenarios**
   ```
   Use Generative Answers node:

   Prompt: "Based on this requirement, identify all test scenarios needed:

   HAPPY PATH (Normal Flow):
   - What are the main success scenarios?
   - What is the standard user journey?

   NEGATIVE SCENARIOS (Error Handling):
   - What can go wrong?
   - What are invalid inputs?
   - What happens if dependencies fail?

   BOUNDARY CONDITIONS:
   - What are minimum/maximum values?
   - What are limits and thresholds?

   EDGE CASES:
   - What are unusual but valid scenarios?
   - What are rare combinations?

   SECURITY:
   - What are potential security risks?
   - What unauthorized actions should be prevented?

   For each scenario, provide:
   - Scenario name
   - Description
   - Priority (P0/P1/P2/P3)
   - Test type (Functional/Security/etc.)

   Requirement: {Topic.ParsedRequirement}"
   ```
   Save to: `Topic.TestScenarios`

   **Node 4: Generate Detailed Test Cases**
   ```
   Use Generative Answers node:

   Prompt: "For each scenario identified, create a detailed test case
   following EGAT's standard template:

   For each test case include:
   - Test Case ID: TC-[Project]-[Feature]-[Number]
   - Title: Clear, descriptive
   - Related Requirement: [REQ-ID]
   - Priority: [from scenario]
   - Test Type: [from scenario]
   - Preconditions: What must be true before test
   - Test Steps: Numbered, actionable steps with expected results
   - Test Data: Specific values to use
   - Expected Outcome: Overall result
   - Post-conditions: State after test

   Generate test cases for all scenarios in this format.

   Scenarios: {Topic.TestScenarios}
   Template: [Use Test Case Template from knowledge]"
   ```
   Save to: `Topic.GeneratedTestCases`

   **Node 5: Create Coverage Matrix**
   ```
   Use Generative Answers node:

   Prompt: "Create a test coverage matrix showing:

   | Requirement | Test Case ID | Scenario Type | Priority | Status |
   |-------------|--------------|---------------|----------|--------|

   Include all generated test cases organized by requirement section.

   Then provide summary:
   - Total test cases: X
   - By type: Happy Path (X), Negative (X), Boundary (X), Edge (X), Security (X)
   - By priority: P0 (X), P1 (X), P2 (X), P3 (X)

   Test Cases: {Topic.GeneratedTestCases}"
   ```
   Save to: `Topic.CoverageMatrix`

   **Node 6: Display Results**
   ```
   Show comprehensive output:
   - Summary of test cases generated
   - Coverage matrix
   - Detailed test cases (formatted)
   - Offer to export
   ```

   **Node 7: Export Options** (optional)
   ```
   Question: "Would you like to export these test cases?"

   Options:
   - [ ] Copy to clipboard
   - [ ] Save to SharePoint
   - [ ] Download as Excel
   - [ ] Send to Azure DevOps (if integrated)
   ```

4. **Save** topic

### Step 5.2: Create "Update Existing Test Cases" Topic

For when requirements change:

1. Create topic: **Update Test Cases**

2. Flow:
   - Identify requirement that changed
   - Load existing test cases
   - Compare with new requirement
   - Suggest updates/additions/deletions
   - Generate updated test cases

### Step 5.3: Create "Generate Test Data" Topic

1. Create topic: **Generate Test Data Sets**

2. Functionality:
   - Analyze test case input requirements
   - Generate valid test data examples
   - Generate invalid test data (for negative tests)
   - Generate boundary value test data
   - Provide data in tabular format

---

## Phase 6: Testing

**Time**: 1-2 hours

See detailed [testing-guide.md](./testing-guide.md) for comprehensive test scenarios.

### Quick Testing Steps

1. **Test Simple Requirement**:
   ```
   Upload: "User Login" requirement
   Ask: "Generate test cases"
   Verify: Happy path + negative scenarios generated
   ```

2. **Test Complex Requirement**:
   ```
   Upload: "Multi-step approval workflow" requirement
   Ask: "Generate comprehensive test suite"
   Verify: All workflow paths covered
   ```

3. **Test Requirement Update**:
   ```
   Upload: Modified requirement
   Ask: "Update existing test cases for this change"
   Verify: Only affected test cases updated
   ```

4. **Test Coverage**:
   ```
   Ask: "Show test coverage matrix for [Feature]"
   Verify: All scenarios categorized correctly
   ```

---

## Phase 7: Deployment

**Time**: 30 minutes

### Step 7.1: Publish Agent

1. Click **Publish**
2. Review changes
3. Confirm publication

### Step 7.2: Deploy to Microsoft Teams

1. Go to **Channels** → **Microsoft Teams**

2. Configure:
   ```
   ☑ Show to QA team and developers
   ☑ Allow in chats
   ```

3. Share link with testing team

### Step 7.3: Create User Guide

```markdown
# EGAT Test Case Generator - Quick Guide

## How to Use

### 1. Generate Test Cases from Requirement
1. Open agent in Teams
2. Upload requirement document or paste requirement ID
3. Say: "Generate test cases"
4. Review generated test cases
5. Request modifications if needed
6. Export to your preferred format

### 2. Update Existing Test Cases
1. Upload updated requirement
2. Say: "Update test cases for [Requirement ID]"
3. Review suggested changes
4. Apply updates

### 3. Generate Specific Scenario Types
1. Say: "Generate negative test scenarios for [Feature]"
2. Or: "Create boundary condition tests for [Feature]"
3. Or: "What security tests do I need for [Feature]?"

## Best Practices
- Provide clear, complete requirements
- Review generated test cases for accuracy
- Customize test data for your environment
- Use generated test cases as starting point
- Always validate AI-generated tests

## Tips
- More detailed requirements = better test cases
- Include acceptance criteria in requirements
- Mention edge cases in requirements
- Specify any known constraints
```

---

## Phase 8: Monitoring & Optimization

**Time**: Ongoing

### Step 8.1: Track Metrics

Monitor:
- Number of test cases generated per week
- Time saved vs manual creation
- Test case quality (defects found)
- QA team satisfaction
- Coverage improvements

### Step 8.2: Continuous Improvement

Weekly for first month:
- Review generated test cases with QA lead
- Identify any missing scenarios
- Update agent instructions
- Refine test case templates
- Collect QA team feedback

Monthly:
- Analyze test effectiveness (defect detection rate)
- Update test case standards
- Add new scenario categories
- Improve test data suggestions

---

## Success Criteria Checklist

- [ ] Agent generates test cases in < 2 minutes
- [ ] Test coverage includes happy path + negative + boundary scenarios
- [ ] Generated test cases follow EGAT template format
- [ ] Test data suggestions are relevant and realistic
- [ ] Coverage matrix accurately reflects test scope
- [ ] QA team satisfaction ≥ 4.0/5.0
- [ ] Time savings ≥ 50% vs manual creation

---

## Troubleshooting

### Issue: Generated test cases miss important scenarios

**Solution**:
1. Improve requirement detail and clarity
2. Update agent instructions with specific scenario types
3. Add domain-specific examples to knowledge base
4. Train QA team to provide better requirements

### Issue: Test case format doesn't match EGAT standard

**Solution**:
1. Verify test case template is in knowledge sources
2. Update agent instructions with exact format requirements
3. Provide example test cases as reference

### Issue: Too many redundant test cases

**Solution**:
1. Update agent instructions to avoid duplication
2. Add logic to merge similar scenarios
3. Adjust generation parameters

---

**Congratulations!** You've implemented an AI-powered Test Case Generator.

**Next**: See [testing-guide.md](./testing-guide.md) for comprehensive testing procedures.
