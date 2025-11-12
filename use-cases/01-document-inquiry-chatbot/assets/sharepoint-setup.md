# SharePoint Setup Guide

## Overview

This guide helps you prepare your SharePoint environment for the Document Inquiry Chatbot.

---

## Pre-Setup Checklist

Before configuring SharePoint:
- [ ] You have SharePoint admin or site owner permissions
- [ ] You know which documents should be searchable
- [ ] You understand EGAT's information governance policies
- [ ] You have identified document owners to consult

---

## Step 1: Plan Your Document Structure

### 1.1 Audit Current State

Create a spreadsheet to document your current SharePoint structure:

| Site URL | Library Name | Document Count | Owner | Access Level | Include in Agent? |
|----------|--------------|----------------|-------|--------------|-------------------|
| /sites/Engineering | Technical Docs | 200 | Engineering Dept | All Staff | Yes |
| /sites/HR | Policies | 50 | HR Dept | All Staff | Yes |
| /sites/Projects | Project Archives | 500 | PMO | Project Members | Maybe |
| /sites/Executive | Board Minutes | 30 | Executive Office | Executives Only | No |

### 1.2 Determine Scope

Decide which sites/libraries to include:

**Include**:
- Public policies and procedures
- Technical documentation for all staff
- General reference materials
- Training resources

**Exclude**:
- Confidential/restricted documents
- Personal OneDrive files (unless specifically shared)
- Draft documents
- Archived/outdated content

---

## Step 2: Organize Document Libraries

### 2.1 Recommended Structure

For best results, organize documents by:

```
/sites/EGATKnowledge (Main knowledge hub)
├── Policies/
│   ├── HR-Policies/
│   ├── IT-Policies/
│   ├── Safety-Policies/
│   └── Finance-Policies/
├── Procedures/
│   ├── Operational-Procedures/
│   ├── Safety-Procedures/
│   └── Administrative-Procedures/
├── Technical-Documentation/
│   ├── Engineering-Standards/
│   ├── Equipment-Manuals/
│   └── Technical-Specifications/
└── Reference-Materials/
    ├── Forms-Templates/
    ├── Guidelines/
    └── Quick-Reference-Guides/
```

### 2.2 Consolidation vs Federated Approach

**Option A: Consolidate** (Recommended for <2,000 documents)
- Move/copy all searchable documents to one central site
- Easier to manage and maintain
- Single permission model
- Faster indexing

**Option B: Federated** (For larger deployments)
- Keep documents in existing sites
- Add multiple sites to agent knowledge sources
- Maintains existing workflows
- More complex permission management

---

## Step 3: Configure Metadata

### 3.1 Add Metadata Columns

In SharePoint, add these columns to document libraries:

#### Required Columns

1. **Document Type** (Choice)
   - Options: Policy, Procedure, Standard, Guideline, Form, Manual, Reference
   - Purpose: Helps users filter by document type

2. **Department** (Choice)
   - Options: HR, IT, Engineering, Safety, Finance, Operations, Executive, Legal
   - Purpose: Departmental filtering

3. **Status** (Choice)
   - Options: Draft, Under Review, Approved, Archived
   - Purpose: Only "Approved" documents should be searchable

4. **Last Reviewed** (Date)
   - Purpose: Track document currency

#### Optional but Recommended

5. **Language** (Choice)
   - Options: Thai, English, Both
   - Purpose: Language filtering

6. **Keywords** (Multi-line text)
   - Purpose: Additional search terms

7. **Document Owner** (Person)
   - Purpose: Contact for questions

8. **Version Number** (Text)
   - Purpose: Track document versions

### 3.2 Apply Metadata to Existing Documents

**Manual Method** (Small libraries):
1. Open document library
2. Edit properties for each document
3. Fill in metadata fields

**Bulk Method** (Large libraries):
1. Open library in Grid view
2. Select multiple documents
3. Edit properties in bulk
4. Or use PowerShell script (see below)

#### PowerShell Script for Bulk Metadata

```powershell
# Connect to SharePoint
Connect-PnPOnline -Url "https://egat.sharepoint.com/sites/EGATKnowledge" -Interactive

# Get all documents in library
$docs = Get-PnPListItem -List "Shared Documents"

# Update metadata for documents in specific folder
foreach ($doc in $docs) {
    if ($doc.FieldValues.FileDirRef -like "*/Policies/HR-Policies*") {
        Set-PnPListItem -List "Shared Documents" -Identity $doc.Id -Values @{
            "DocumentType" = "Policy"
            "Department" = "HR"
            "Status" = "Approved"
        }
    }
}
```

---

## Step 4: Set Up Permissions

### 4.1 Permission Best Practices

**Principle**: Agent respects SharePoint permissions
- Users only see documents they have permission to access
- No need to replicate permissions in the agent
- Test with different user roles

### 4.2 Common Permission Models

**Model 1: Open Access**
- All approved documents visible to all EGAT staff
- Simple to manage
- Good for public policies and procedures

**Model 2: Department-Based**
- Documents inherit department SharePoint permissions
- Users see documents from their department + public docs
- Good for mixed content

**Model 3: Role-Based**
- Permissions based on role (Engineer, Manager, etc.)
- Complex but most secure
- Good for sensitive content

### 4.3 Configure Permissions

1. **For entire library**:
   - Library Settings → Permissions
   - Grant "Read" access to appropriate groups

2. **For specific folders**:
   - Folder → ... → Manage Access
   - Break inheritance if needed
   - Grant "Read" to specific users/groups

3. **Test with different users**:
   - Use "Check Permissions" feature
   - Verify users see appropriate content

---

## Step 5: Prepare Documents for Indexing

### 5.1 Document Quality Checklist

For each document:
- [ ] File format is supported (PDF, Word, Excel, PowerPoint)
- [ ] Document is not password-protected
- [ ] File size < 512 MB
- [ ] Text is searchable (not scanned image)
- [ ] Document is checked in (not checked out)
- [ ] Metadata is complete
- [ ] Status = "Approved"

### 5.2 Handle Scanned Documents

If you have scanned PDFs without searchable text:

**Option 1: OCR with Adobe Acrobat**
1. Open PDF in Adobe Acrobat Pro
2. Tools → Enhance Scans → Recognize Text
3. Save and re-upload to SharePoint

**Option 2: Azure Form Recognizer/Computer Vision**
1. Use Azure AI services for batch OCR
2. Replace scanned PDFs with searchable versions

**Option 3: Manual re-creation**
- For critical documents, recreate from scratch
- Ensures best quality and searchability

### 5.3 Clean Up Old Documents

**Archive outdated content**:
1. Create "Archive" library
2. Move superseded documents
3. Update links in new documents
4. Keep for compliance but exclude from search

**Delete drafts and duplicates**:
- Search for "copy of", "draft", "old", "backup"
- Review and remove unnecessary versions

---

## Step 6: Configure SharePoint Search Settings

### 6.1 Enable Search Crawling

Ensure documents are discoverable:

1. Site Settings → Search and offline availability
2. Enable: "Allow this site to appear in search results"
3. Ensure: "Always show all navigation items in search results" is appropriate

### 6.2 Create Managed Properties (Advanced)

For advanced filtering in Copilot Studio:

1. Go to SharePoint Admin Center
2. Search → Manage Search Schema
3. Map custom columns to managed properties
4. Example: Map "Department" to "DepartmentOWSCHCS"

---

## Step 7: Test SharePoint Setup

### 7.1 Manual Test

1. **Search test**:
   - Go to SharePoint search box
   - Search for known documents
   - Verify they appear in results

2. **Permission test**:
   - Log in as different users
   - Verify they see appropriate documents
   - Test with restricted documents

3. **Metadata test**:
   - Filter by Department
   - Filter by Document Type
   - Verify filters work correctly

### 7.2 Pre-Agent Validation

Before connecting to Copilot Studio:
- [ ] All target documents are indexed in SharePoint search
- [ ] Permissions work as expected
- [ ] Metadata is complete and accurate
- [ ] No duplicate documents
- [ ] All documents are approved status

---

## Step 8: Document Your Configuration

Create a configuration document recording:

**SharePoint Sites Included**:
| Site URL | Library | Document Count | Last Updated |
|----------|---------|----------------|--------------|
| https://egat.sharepoint.com/sites/Knowledge | Policies | 150 | 2025-11-12 |
| https://egat.sharepoint.com/sites/Knowledge | Procedures | 200 | 2025-11-12 |

**Metadata Schema**:
- List all custom columns and their purpose
- Document choice values
- Note any special configurations

**Permission Model**:
- Describe who can access what
- Document any exceptions
- List user groups and their access

**Maintenance Plan**:
- Who updates documents?
- How often is metadata reviewed?
- Process for adding new documents
- Archival schedule

---

## Common Issues and Solutions

### Issue: Documents not appearing in search

**Possible causes**:
- Document library not set to appear in search
- Document is checked out
- Document is in Draft status
- Permissions too restrictive

**Solutions**:
1. Check library search settings
2. Check in all documents
3. Update Status to Approved
4. Review permissions

### Issue: Scanned PDFs return no results

**Cause**: PDFs are images, not searchable text

**Solution**: Apply OCR or recreate documents

### Issue: Too many irrelevant results

**Cause**: Poor metadata or outdated documents

**Solution**:
- Improve metadata quality
- Archive old documents
- Use Status field to exclude drafts

### Issue: Permission denied errors

**Cause**: Users lack access to documents

**Solution**:
- Review SharePoint permissions
- Create appropriate user groups
- Test with different user accounts

---

## Maintenance Schedule

### Weekly
- [ ] Review new documents added
- [ ] Check that metadata is complete
- [ ] Verify Status fields are correct

### Monthly
- [ ] Review analytics on searched documents
- [ ] Archive outdated content
- [ ] Update document ownership

### Quarterly
- [ ] Audit all metadata
- [ ] Review permission model
- [ ] Clean up duplicates
- [ ] Update this guide

---

## Resources

- [SharePoint Online documentation](https://learn.microsoft.com/en-us/sharepoint/introduction)
- [SharePoint metadata best practices](https://learn.microsoft.com/en-us/sharepoint/metadata-and-keywords)
- [SharePoint permissions overview](https://learn.microsoft.com/en-us/sharepoint/understanding-permission-levels)
- [PnP PowerShell documentation](https://pnp.github.io/powershell/)

---

## Next Steps

Once SharePoint is configured:
1. ✅ Complete this setup
2. ⏳ Return to [implementation-guide.md](../implementation-guide.md#phase-3-knowledge-source-configuration)
3. ⏳ Add SharePoint as knowledge source in Copilot Studio
4. ⏳ Test document indexing

---

**Last Updated**: 2025-11-12
**Maintained by**: EGAT Copilot Studio Team
