# Connections Required: Document Inquiry Chatbot

## Overview

This document details all technical connections, permissions, and configurations needed to implement the Document Inquiry Chatbot.

## Microsoft 365 Connections

### 1. SharePoint Online

**Purpose**: Primary document repository

**What's Needed**:
- SharePoint site URL(s) where documents are stored
- Document library names
- Read permissions for the agent

**Setup Steps**:

1. **Identify SharePoint Sites**
   ```
   Example EGAT Sites:
   - https://egat.sharepoint.com/sites/TechnicalDocs
   - https://egat.sharepoint.com/sites/Policies
   - https://egat.sharepoint.com/sites/ProjectArchive
   ```

2. **Check Permissions**
   - Agent service account needs READ access to document libraries
   - Test access by visiting each library manually
   - Document any restricted libraries

3. **Document Library Structure**
   ```
   Recommended structure:
   /sites/TechnicalDocs
     └── Documents/
         ├── Electrical/
         ├── Mechanical/
         ├── Safety/
         └── Standards/
   ```

**Connection Type**: Built-in SharePoint connector (no custom connector needed)

**Permissions Required**:
- SharePoint Site: Read
- SharePoint Library: Read
- Azure AD: User.Read

---

### 2. OneDrive for Business

**Purpose**: Personal/team document storage

**What's Needed**:
- OneDrive URLs for shared folders
- Permissions to access shared files

**Setup Steps**:

1. **Identify Shared Folders**
   - Work with teams to identify commonly shared folders
   - Get OneDrive share links

2. **Configure Sharing**
   - Ensure files are shared with "EGAT Organization"
   - Avoid person-specific shares (won't scale)

**Connection Type**: OneDrive connector (built-in)

**Permissions Required**:
- Files.Read.All (organization-wide)
- Sites.Read.All

---

### 3. Microsoft Teams (Deployment)

**Purpose**: Deploy agent to Teams for easy access

**What's Needed**:
- Teams tenant access
- Permission to add custom apps

**Setup Steps**:

1. **Teams App Registration** (done in Copilot Studio)
2. **Deployment Options**:
   - Personal app (users can add)
   - Team-specific (add to channels)
   - Organization-wide (IT admin approval)

**Recommended**: Start with personal app, expand to org-wide after validation

**Permissions Required**:
- Teams app upload permission
- Azure AD app registration (automatic)

---

## Power Platform Connections

### 4. Dataverse (Optional but Recommended)

**Purpose**: Store conversation logs, analytics, user feedback

**What's Needed**:
- Dataverse environment
- Database capacity
- Tables for logging

**Setup Steps**:

1. **Create Dataverse Tables**:
   ```
   Tables to create:
   - ConversationLog (stores queries)
   - DocumentAccess (tracks which docs accessed)
   - UserFeedback (thumbs up/down ratings)
   ```

2. **Configure Data Retention**
   - Set retention policies (e.g., 90 days)
   - Plan for data archival

**Connection Type**: Dataverse connector (built-in)

**Permissions Required**:
- Environment Maker role
- System Administrator (for table creation)

---

### 5. Power Automate (Optional - for advanced features)

**Purpose**: Enhanced functionality (notifications, logging, etc.)

**Flows to Create**:

#### Flow 1: Document Access Notification
- **Trigger**: When user accesses specific document category
- **Action**: Log to Dataverse, optional Teams notification

#### Flow 2: Failed Query Logging
- **Trigger**: When agent can't find answer
- **Action**: Log to improve knowledge base

#### Flow 3: Weekly Usage Report
- **Trigger**: Weekly schedule
- **Action**: Generate usage report, email to stakeholders

**Connection Type**: Multiple
- Office 365 Users (for user info)
- Dataverse (for logging)
- Office 365 Outlook (for emails)

**Permissions Required**:
- Power Automate Per User license
- Premium connector access

---

## Azure Services (Optional Advanced Features)

### 6. Azure OpenAI (If using custom models)

**Purpose**: Enhanced AI capabilities beyond standard Copilot Studio

**Only needed if**:
- Require custom prompts
- Need specialized document understanding
- Want to fine-tune responses

**Setup**:
1. Azure OpenAI resource in EGAT subscription
2. Deploy GPT-4 model
3. Configure in Copilot Studio as custom API

**Cost**: Pay-per-token (estimate ~$0.03 per query)

**Recommendation**: Start without Azure OpenAI, add only if needed

---

### 7. Azure AI Search (For large-scale indexing)

**Purpose**: Index 10,000+ documents with advanced search

**Only needed if**:
- Document volume > 5,000 files
- Need complex filtering/faceting
- Require custom ranking

**Setup**:
1. Create Azure AI Search resource
2. Configure indexer for SharePoint
3. Connect to Copilot Studio

**Cost**: ~$250/month for Basic tier

**Recommendation**: Start with built-in Copilot Studio indexing

---

## Document Preparation

### Supported File Types

Copilot Studio can index:
- ✅ PDF (.pdf)
- ✅ Word (.doc, .docx)
- ✅ Excel (.xls, .xlsx)
- ✅ PowerPoint (.ppt, .pptx)
- ✅ Text files (.txt, .md)
- ✅ HTML (.html, .htm)
- ✅ CSV (.csv)
- ⚠️ Scanned images (requires OCR pre-processing)
- ❌ CAD files (not supported)
- ❌ Videos (not supported)

### Document Optimization

For best results:

1. **File Size**: < 512 MB per file
2. **Text Quality**: Searchable text (not scanned images)
3. **Structure**: Use headings, sections, tables of contents
4. **Metadata**: Add titles, descriptions, tags in SharePoint
5. **Language**: Clearly indicate language (Thai/English)

### Pre-Processing Required

**For scanned documents**:
1. Use OCR tool (Azure Computer Vision, Adobe Acrobat)
2. Convert to searchable PDF
3. Upload to SharePoint

**For multilingual documents**:
1. Separate Thai and English documents (if possible)
2. Or clearly mark language in metadata
3. Configure agent for both languages

---

## Security & Compliance

### 1. Data Residency

**Requirement**: EGAT data must stay in Asia-Pacific region

**Configuration**:
- Ensure Power Platform environment is in Asia region
- Verify SharePoint region setting
- Check Copilot Studio data location

### 2. Access Control

**Principle**: Agent respects SharePoint permissions

**Implementation**:
- Agent sees only what user can see
- Row-level security via SharePoint
- No need to replicate permissions in agent

**Testing**:
- Test with different user roles
- Verify restricted documents don't appear for unauthorized users

### 3. Compliance

**Considerations**:
- GDPR/PDPA compliance (chat logs contain user data)
- Data retention policies
- Audit logging

**Required Actions**:
- Enable audit logs in Copilot Studio
- Configure data retention
- Document data handling in privacy policy

---

## Network & Firewall

### Required Endpoints

Allow outbound traffic to:
```
*.powerplatform.com
*.dynamics.com
*.microsoft.com
*.office.com
*.sharepoint.com
login.microsoftonline.com
graph.microsoft.com
```

### Ports
- HTTPS: 443
- WebSocket: 443

### Internal Access

If EGAT uses on-premises SharePoint:
- **On-Premises Data Gateway** required
- Install on server with SharePoint access
- Configure gateway in Power Platform

---

## License Summary

### Minimum Required
- ✅ Copilot Studio license (per user or tenant)
- ✅ Power Platform environment
- ✅ SharePoint Online (included in M365)

### Optional Enhanced
- ⏺️ Power Automate Premium (for flows)
- ⏺️ Azure OpenAI (for custom AI)
- ⏺️ Azure AI Search (for advanced search)
- ⏺️ Dataverse capacity (for logging)

**Estimated Monthly Cost** (50 active users):
- Minimum: ~$1,000 (Copilot Studio licenses)
- With optional: ~$2,000-3,000

---

## Setup Checklist

Use this checklist during implementation:

### Pre-Implementation
- [ ] Identify all SharePoint sites with documents
- [ ] Audit document permissions
- [ ] Clean up/organize document libraries
- [ ] Verify network access to required endpoints
- [ ] Obtain necessary licenses

### During Implementation
- [ ] Create Copilot Studio environment
- [ ] Add SharePoint as knowledge source
- [ ] Test indexing process
- [ ] Configure agent language settings
- [ ] Set up conversation logging

### Post-Implementation
- [ ] Deploy to Teams
- [ ] Train pilot users
- [ ] Monitor usage analytics
- [ ] Gather feedback
- [ ] Plan for scaling

---

## Troubleshooting Common Connection Issues

### Issue 1: "Cannot access SharePoint site"
**Cause**: Permissions not configured
**Solution**:
1. Verify agent service account has read access
2. Check SharePoint sharing settings
3. Ensure site isn't private

### Issue 2: "Documents not indexing"
**Cause**: Unsupported file format or size too large
**Solution**:
1. Check file types (must be supported)
2. Verify file size < 512 MB
3. Check if files are checked out (must be checked in)

### Issue 3: "Users can't access agent in Teams"
**Cause**: App not properly deployed
**Solution**:
1. Republish agent
2. Check Teams app permissions
3. Verify users are in correct tenant

---

## Technical Contacts

Document these for your implementation:

| Role | Name | Email | Responsibility |
|------|------|-------|----------------|
| SharePoint Admin | _______ | _______ | Document libraries |
| Teams Admin | _______ | _______ | Teams deployment |
| Power Platform Admin | _______ | _______ | Environment setup |
| Network Admin | _______ | _______ | Firewall rules |
| Compliance Officer | _______ | _______ | Data governance |

---

**Next**: Proceed to [implementation-guide.md](./implementation-guide.md) for step-by-step instructions.
