# Connections Required: Test Case Generator

## Overview

Technical connections, permissions, and configurations needed for the Test Case Generator.

## Microsoft 365 Connections

### 1. SharePoint Online
**Purpose**: Store requirements and test cases

**Setup**:
- Site: https://egat.sharepoint.com/sites/SoftwareProjects
- Libraries: Requirements, Test-Cases, Templates
- Permissions: QA team Read/Write, Agent Read

**Connection Type**: Built-in SharePoint connector

### 2. Azure DevOps (Optional)
**Purpose**: Access user stories and work items

**Setup**:
1. Install Azure DevOps connector
2. Authenticate with org credentials
3. Select relevant projects
4. Grant read access to work items

**Benefits**: Direct access to user stories, automatic sync

### 3. Microsoft Teams
**Purpose**: Deploy agent

**Setup**: Standard Teams app deployment
**Access**: QA team + Development team

## Power Platform Connections

### 4. Dataverse (Optional)
**Purpose**: Store test case metadata and history

**Tables**:
- TestCases (ID, Requirement, Status, Priority, Generated Date)
- Requirements (ID, Title, Project, Test Cases Generated)
- TestExecutions (Test Case, Result, Date, Tester)

### 5. Power Automate
**Purpose**: Automate test case export and notifications

**Flows**:
- Export test cases to Excel
- Notify QA when test cases generated
- Sync to test management tools

## Document Preparation

### Supported Input Formats
- ✅ Requirements documents (PDF, Word, Markdown)
- ✅ User stories (text, Azure DevOps)
- ✅ Flow diagrams (embedded images)
- ✅ Technical specifications

### Requirements Quality
For best results:
- Clear acceptance criteria
- Defined input/output
- Business rules documented
- Edge cases mentioned
- Dependencies listed

## License Summary

**Minimum Required**:
- Copilot Studio license
- SharePoint Online
- Microsoft Teams

**Optional**:
- Azure DevOps ($6/user/month if not owned)
- Power Automate Premium ($15/user/month)

**Est. Monthly Cost**: $1,000-1,500

---

**Next**: [implementation-guide.md](./implementation-guide.md)
