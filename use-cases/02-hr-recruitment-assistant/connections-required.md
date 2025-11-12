# Connections Required: HR Recruitment Assistant

## Overview

This document details all technical connections, permissions, and configurations needed to implement the HR Recruitment Assistant.

## Microsoft 365 Connections

### 1. SharePoint Online

**Purpose**: Store job descriptions and candidate CVs

**What's Needed**:
- SharePoint site for HR Recruitment
- Document libraries for JDs and CVs
- Read/Write permissions for agent

**Setup Steps**:

1. **Create/Identify SharePoint Site**
   ```
   Site: https://egat.sharepoint.com/sites/HRRecruitment
   or use existing HR site
   ```

2. **Create Document Libraries**:
   - `Job-Descriptions` (for position requirements)
   - `Candidate-CVs` (for application materials)

3. **Check Permissions**:
   - HR team: Contribute (read/write)
   - Agent service account: Read
   - General employees: No access (confidential)

**Connection Type**: Built-in SharePoint connector

**Permissions Required**:
- SharePoint Site: Read/Write
- SharePoint Library: Read/Write
- Azure AD: User.Read

---

### 2. OneDrive for Business (Alternative)

**Purpose**: Store CVs if not using SharePoint

**What's Needed**:
- OneDrive folders for each recruiter
- Shared folders for team collaboration

**Setup Steps**:

1. Create folder structure:
   ```
   OneDrive/
   └── HR-Recruitment/
       ├── Job-Descriptions/
       └── Candidate-CVs/
           └── [Position folders]
   ```

2. Share with HR team

**Connection Type**: OneDrive connector (built-in)

**Permissions Required**:
- Files.Read.All
- Sites.Read.All

---

### 3. Microsoft Teams (Deployment)

**Purpose**: Deploy agent to Teams for easy access

**What's Needed**:
- Teams tenant access
- Permission to add custom apps
- HR Team security group

**Setup Steps**:

1. **Create Security Group** (if not exists):
   - Name: "HR Recruiters"
   - Members: All recruitment staff
   - Type: Security

2. **Deployment Options**:
   - Personal app (recruiters can add)
   - Team-specific (add to HR channel)
   - **Recommended**: Limited to HR security group only

**Permissions Required**:
- Teams app upload permission
- Azure AD app registration (automatic)

---

## Power Platform Connections

### 4. Dataverse (Recommended)

**Purpose**: Store candidate information, match scores, audit trail

**What's Needed**:
- Dataverse environment
- Tables for candidate tracking
- Connection for logging

**Setup Steps**:

1. **Create Dataverse Tables**:

   **Table 1: Candidates**
   ```
   Columns:
   - Candidate Name (Text)
   - Email (Text)
   - Phone (Text)
   - Position Applied (Lookup to Positions)
   - CV File Link (Text/URL)
   - Date Applied (Date)
   - Status (Choice: New, Screening, Interview, Offer, Rejected)
   - Match Score (Number)
   - Evaluation Summary (Multi-line text)
   ```

   **Table 2: Positions**
   ```
   Columns:
   - Position ID (Text, Primary)
   - Position Title (Text)
   - Department (Text)
   - Status (Choice: Open, Filled, Closed)
   - Posted Date (Date)
   - Closing Date (Date)
   - Number of Applicants (Number)
   ```

   **Table 3: Evaluations**
   ```
   Columns:
   - Candidate (Lookup to Candidates)
   - Position (Lookup to Positions)
   - Evaluator (Lookup to User)
   - Match Score (Number)
   - Education Score (Number)
   - Experience Score (Number)
   - Skills Score (Number)
   - Strengths (Multi-line text)
   - Gaps (Multi-line text)
   - Recommendation (Choice: Proceed, Maybe, Reject)
   - Evaluation Date (Date/Time)
   ```

2. **Configure Data Retention**:
   - Keep candidate data for 1 year after hire/reject
   - Comply with PDPA/data protection regulations

**Connection Type**: Dataverse connector (built-in)

**Permissions Required**:
- Environment Maker role
- System Administrator (for table creation)

---

### 5. Power Automate (For Advanced Features)

**Purpose**: Automate workflows, notifications, integrations

**Flows to Create**:

#### Flow 1: Email Notification on Shortlist
- **Trigger**: When candidate match score > 75%
- **Action**: Send Teams notification to hiring manager
- **Data**: Candidate summary, CV link, match details

#### Flow 2: CV Processing Automation
- **Trigger**: When CV uploaded to SharePoint
- **Actions**:
  1. Extract text from PDF (Azure AI Document Intelligence)
  2. Store in Dataverse
  3. Notify recruiter
  4. Update position applicant count

#### Flow 3: Interview Invitation Generator
- **Trigger**: When candidate status = "Shortlisted"
- **Action**: Generate personalized email template
- **Include**: Interview dates, location, preparation materials

**Connection Type**: Multiple
- Office 365 Users (for user info)
- Dataverse (for data)
- Office 365 Outlook (for emails)
- Teams (for notifications)

**Permissions Required**:
- Power Automate Per User license (for premium connectors)
- Office 365 licenses

---

## Azure Services (Optional Advanced Features)

### 6. Azure AI Document Intelligence

**Purpose**: Enhanced CV parsing, especially for scanned documents or complex layouts

**What's Needed**:
- Azure subscription
- Azure AI Document Intelligence resource
- API keys

**Setup Steps**:

1. **Create Azure Resource**:
   ```
   Resource type: Azure AI Document Intelligence
   Pricing tier: Free (5,000 pages/month) or S0 (Standard)
   Region: Southeast Asia (for EGAT)
   ```

2. **Get API Credentials**:
   - Endpoint URL
   - API Key

3. **Connect to Copilot Studio**:
   - Settings → AI Capabilities
   - Enable Document Intelligence
   - Add endpoint and key

**Benefits**:
- Better extraction from scanned CVs
- Table/structure recognition
- Multi-language support
- Handwriting recognition (if applicable)

**Cost**:
- Free tier: 5,000 pages/month (sufficient for most use cases)
- Standard: ~$1.50 per 1,000 pages

**Recommendation**: Start with Copilot Studio built-in extraction, add Azure AI if needed

---

### 7. Azure Blob Storage (Optional)

**Purpose**: Store large volumes of CVs with better performance

**Only needed if**:
- CV volume > 10,000 files
- Need faster access than SharePoint
- Want advanced versioning/archival

**Setup**:
1. Create Azure Storage Account
2. Create container for CVs
3. Configure lifecycle policies (auto-delete after X months)
4. Connect via Power Automate or custom connector

**Cost**: ~$0.02 per GB/month (very low)

**Recommendation**: Start with SharePoint, migrate to Blob only if needed

---

## Document Preparation

### Supported File Types

Copilot Studio can process:
- ✅ PDF (.pdf) - **Recommended for CVs**
- ✅ Word (.doc, .docx)
- ✅ Text files (.txt)
- ⚠️ Scanned images (requires Azure AI Document Intelligence)
- ❌ Image files (.jpg, .png) - need OCR first

### CV Optimization

For best extraction results:

1. **File Format**: Searchable PDF (not scanned image)
2. **File Size**: < 5 MB (for fast processing)
3. **Structure**: Clear sections with headings
4. **Language**: Primarily Thai or English (mixed OK)
5. **Length**: 1-5 pages optimal

### Pre-Processing for Scanned CVs

If candidates submit scanned/image PDFs:

**Option 1: Use Azure AI Document Intelligence**
- Automatically handles scanned documents
- Converts to searchable text
- Best quality extraction

**Option 2: Manual OCR**
- Use Adobe Acrobat Pro OCR
- Or online OCR tools
- Re-save as searchable PDF

**Option 3: Request Resubmission**
- Ask candidates for native PDF/Word format
- Include in application instructions

---

## Security & Compliance

### 1. Data Privacy (PDPA Compliance)

**Requirements**:
- Candidate data is personal information
- Must have consent to process
- Must delete upon request
- Must secure properly

**Implementation**:
1. **Consent Collection**:
   - Include in job application form
   - "I consent to EGAT processing my personal data for recruitment purposes"

2. **Data Retention**:
   ```
   Successful candidates: Transfer to HR system
   Unsuccessful candidates: Delete after 1 year
   Withdrawn applications: Delete immediately upon request
   ```

3. **Access Control**:
   - Only HR recruitment team can access
   - Audit log of who viewed which CVs
   - No external sharing

4. **Data Deletion**:
   - Automated deletion after retention period
   - Manual deletion process for requests
   - Permanent deletion (not just archive)

### 2. Access Control

**Principle**: Least privilege access

**Roles**:
- **HR Recruiters**: Full access (read, evaluate, compare)
- **Hiring Managers**: View shortlisted candidates only
- **Agent Service Account**: Read-only access to documents
- **Candidates**: No access (one-way submission)

**Implementation**:
1. Use Azure AD Security Groups
2. SharePoint permissions inheritance
3. Dataverse security roles

### 3. Audit Logging

**Required Logs**:
- Who accessed which candidate's CV
- When evaluations were performed
- Match score changes over time
- File access history

**Implementation**:
- Enable Dataverse audit logging
- Enable SharePoint audit logs
- Configure Power Automate to log actions
- Retain logs for 2 years (compliance)

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
*.azure.com (if using Azure AI)
login.microsoftonline.com
graph.microsoft.com
```

### Ports
- HTTPS: 443
- WebSocket: 443

---

## License Summary

### Minimum Required
- ✅ Copilot Studio license (tenant or per-user)
- ✅ Power Platform environment
- ✅ SharePoint Online (included in M365)
- ✅ Microsoft Teams (included in M365)

### Optional Enhanced
- ⏺️ Azure AI Document Intelligence ($0-100/month depending on volume)
- ⏺️ Power Automate Premium (for advanced flows)
- ⏺️ Dataverse capacity (for candidate database)

**Estimated Monthly Cost** (50-100 CVs/month):
- Minimum: ~$1,000 (Copilot Studio licenses only)
- With Azure AI: ~$1,050
- With all optional: ~$1,500

---

## Integration Checklist

### Pre-Implementation
- [ ] SharePoint site created for HR Recruitment
- [ ] Job description library set up with metadata
- [ ] CV storage structure defined
- [ ] Security groups created for HR team
- [ ] Permissions configured
- [ ] Sample job descriptions uploaded (5+)
- [ ] Sample CVs prepared for testing (20+)

### During Implementation
- [ ] Copilot Studio agent created
- [ ] SharePoint knowledge sources added
- [ ] Document extraction tested and working
- [ ] Matching logic configured and calibrated
- [ ] Dataverse tables created (if using)
- [ ] Power Automate flows configured (if using)

### Post-Implementation
- [ ] Agent deployed to HR team in Teams
- [ ] User training conducted
- [ ] Analytics enabled and monitored
- [ ] Feedback collection process established
- [ ] Data retention policies implemented
- [ ] PDPA compliance verified

---

## Troubleshooting Common Connection Issues

### Issue 1: "Cannot access SharePoint library"
**Cause**: Permissions not configured
**Solution**:
1. Verify agent service account has read access
2. Check SharePoint site is not private
3. Ensure library is not checked out
4. Test with site owner account first

---

### Issue 2: "CV text not extracting"
**Cause**: Scanned image or protected PDF
**Solution**:
1. Check if PDF is searchable (try Ctrl+F)
2. Use Adobe Acrobat to enable OCR
3. Consider Azure AI Document Intelligence
4. Ask candidate for native format

---

### Issue 3: "Match scores inconsistent"
**Cause**: Job description not specific enough
**Solution**:
1. Improve JD quality and specificity
2. Add evaluation rubric to agent instructions
3. Test with known good/bad CVs
4. Calibrate scoring thresholds

---

### Issue 4: "Slow processing with many CVs"
**Cause**: Too many documents to process at once
**Solution**:
1. Process in smaller batches
2. Pre-extract and store in Dataverse
3. Use indexed metadata for filtering
4. Consider Azure Blob Storage for large volumes

---

## Technical Contacts

Document these for your implementation:

| Role | Name | Email | Responsibility |
|------|------|-------|----------------|
| SharePoint Admin | _______ | _______ | Document libraries, permissions |
| HR Lead | _______ | _______ | Job descriptions, process owner |
| Power Platform Admin | _______ | _______ | Environment setup, Dataverse |
| Compliance Officer | _______ | _______ | PDPA compliance, data governance |
| Azure Admin | _______ | _______ | Azure AI services (if used) |

---

**Next**: Proceed to [implementation-guide.md](./implementation-guide.md) for step-by-step instructions.
