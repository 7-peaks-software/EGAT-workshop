# Implementation Guide: Document Inquiry Chatbot

## Table of Contents

1. [Prerequisites Check](#prerequisites-check)
2. [Phase 1: Document Preparation](#phase-1-document-preparation)
3. [Phase 2: Agent Creation](#phase-2-agent-creation)
4. [Phase 3: Knowledge Source Configuration](#phase-3-knowledge-source-configuration)
5. [Phase 4: Conversation Design](#phase-4-conversation-design)
6. [Phase 5: Testing](#phase-5-testing)
7. [Phase 6: Deployment](#phase-6-deployment)
8. [Phase 7: Monitoring & Optimization](#phase-7-monitoring--optimization)

**Estimated Total Time**: 4-6 hours

---

## Prerequisites Check

Before starting, verify you have completed:

- [ ] Reviewed [README.md](./README.md)
- [ ] Reviewed [connections-required.md](./connections-required.md)
- [ ] Access to Microsoft Copilot Studio
- [ ] Access to SharePoint sites with documents
- [ ] Completed Lab 01 and Lab 02 from this workshop
- [ ] Copilot Studio license activated

---

## Phase 1: Document Preparation

**Time**: 1-2 hours

### Step 1.1: Audit Existing Documents

1. **Identify Document Locations**

   Create a spreadsheet to track:
   ```
   | SharePoint Site | Library | Document Count | Language | Last Updated |
   |----------------|---------|----------------|----------|--------------|
   | TechnicalDocs  | Standards | 150 | EN/TH | 2025-01 |
   | Policies       | HR       | 80  | TH    | 2024-12 |
   ```

2. **Check Document Quality**

   For each library:
   - [ ] Documents are searchable (not scanned images)
   - [ ] File sizes < 512 MB
   - [ ] Proper metadata (titles, descriptions)
   - [ ] Clear file naming conventions

### Step 1.2: Organize SharePoint Libraries

Best practices for organization:

1. **Create Clear Structure**
   ```
   /sites/EGATKnowledge
     └── Shared Documents/
         ├── 01-Policies-and-Procedures/
         │   ├── HR-Policies/
         │   ├── Safety-Procedures/
         │   └── IT-Guidelines/
         ├── 02-Technical-Documentation/
         │   ├── Electrical-Standards/
         │   ├── Mechanical-Standards/
         │   └── Engineering-Specs/
         └── 03-Project-Archives/
             ├── 2023-Projects/
             ├── 2024-Projects/
             └── 2025-Projects/
   ```

2. **Add Metadata Columns**

   In SharePoint, add these columns:
   - **Document Type**: (Choice: Policy, Procedure, Standard, Guide, Reference)
   - **Department**: (Choice: HR, IT, Engineering, Safety, etc.)
   - **Language**: (Choice: Thai, English, Both)
   - **Last Reviewed**: (Date)
   - **Keywords**: (Multi-line text)

3. **Configure Permissions**

   Ensure:
   - All documents users should access have "Read" permission
   - Sensitive documents are in separate restricted libraries
   - Agent service account has appropriate access

### Step 1.3: Prepare Sample Documents

For testing, prepare at least 20 documents across different types:

- [ ] 5 policy documents (PDF)
- [ ] 5 technical standards (PDF/Word)
- [ ] 5 procedures (Word/PDF)
- [ ] 5 project documents (PowerPoint/Word)

Upload to SharePoint test library.

---

## Phase 2: Agent Creation

**Time**: 30 minutes

### Step 2.1: Create New Agent

1. Navigate to [Microsoft Copilot Studio](https://copilotstudio.microsoft.com/)

2. Sign in with your EGAT account

3. Select your Power Platform environment

4. Click **Agents** in the left navigation

5. Click **+ New agent**

6. Configure the agent:
   ```
   Name: EGAT Document Inquiry Assistant
   Description: AI assistant to help EGAT staff find documents and information
   Language: Thai and English (or select primary language)
   Icon: Upload EGAT logo or select document icon
   ```

7. Click **Create**

### Step 2.2: Configure Agent Settings

1. Click the **Settings** (gear icon)

2. Navigate to **Generative AI** section

3. Configure:
   ```
   How should your agent decide how to respond?
   ○ Classic (topic-based)
   ● Generative (AI-powered) ← SELECT THIS

   Content moderation:
   ● Medium ← Recommended for enterprise

   How strict should the agent be?
   ● Medium ← Allows some creativity while staying grounded
   ```

4. In the **Instructions** field, add:
   ```
   You are the EGAT Document Inquiry Assistant. Your role is to help EGAT employees find documents and information quickly.

   Guidelines:
   1. Always search knowledge sources before responding
   2. Cite sources with document names and links
   3. If you cannot find information, admit it and suggest alternative search terms
   4. Be concise but comprehensive
   5. Support both Thai and English queries
   6. Prioritize recent documents over older ones when relevant
   7. If multiple relevant documents exist, list top 3-5 with brief descriptions

   When users ask for documents:
   - Provide direct links
   - Summarize key points from the document
   - Mention document type, department, and last updated date
   ```

5. Click **Save**

### Step 2.3: Configure Fallback Topic

1. Go to **Topics** in left navigation

2. Find the **Fallback** topic

3. Click to edit

4. Update the message node:
   ```
   I'm sorry, I couldn't find information about that in our document library.

   You can try:
   • Rephrasing your question
   • Using different keywords
   • Specifying the department or document type
   • Contacting the Document Management team at documents@egat.co.th

   What else can I help you find?
   ```

5. **Save** the topic

---

## Phase 3: Knowledge Source Configuration

**Time**: 1-2 hours (depending on document count)

### Step 3.1: Add SharePoint as Knowledge Source

1. From your agent, click **Knowledge** in the top menu

2. Click **+ Add knowledge**

3. Select **SharePoint**

4. **Authenticate** with your credentials

5. In the SharePoint configuration:

   **Option A: Add entire site**
   - Select **Site**
   - Enter site URL: `https://egat.sharepoint.com/sites/EGATKnowledge`
   - Click **Add**

   **Option B: Add specific library**
   - Select **Document Library**
   - Enter site URL
   - Select the library (e.g., "Shared Documents")
   - Click **Add**

6. Wait for indexing to complete (progress bar appears)

### Step 3.2: Upload Additional Documents

For documents not in SharePoint:

1. Click **+ Add knowledge**

2. Select **Files** (upload option)

3. Click **Browse** or drag-and-drop files

4. Select multiple documents (up to 100 at once)

5. For each upload session, provide:
   ```
   Name: [Descriptive name, e.g., "HR Policies 2025"]
   Description: [Brief description of contents]
   ```

6. Click **Add**

7. Monitor indexing progress

### Step 3.3: Add Public Websites (Optional)

If you want to include EGAT's public website or external resources:

1. Click **+ Add knowledge**

2. Select **Websites**

3. Enter URL: `https://www.egat.co.th`

4. Configure:
   ```
   Crawl depth: 2 ← How many clicks deep to go
   Include subdomains: Yes/No
   Allowed paths: /en/, /th/ ← Specify if needed
   ```

5. Click **Add**

> **Note**: Be cautious with websites - only add trusted, relevant sources

### Step 3.4: Verify Knowledge Sources

1. Go to **Knowledge** page

2. Check status of all sources:
   ```
   Status column should show:
   ✅ Ready - Successfully indexed
   ⏳ Processing - Still indexing
   ⚠️ Needs attention - Error occurred
   ```

3. For each knowledge source, click the name to view:
   - Preview of indexed content
   - Number of documents indexed
   - Last sync time

4. **Troubleshooting**:
   - If "Needs attention": Check permissions
   - If stuck "Processing" > 1 hour: Remove and re-add
   - If document count is 0: Verify library has files

---

## Phase 4: Conversation Design

**Time**: 1 hour

### Step 4.1: Create Welcome Topic

1. Go to **Topics**

2. Click **+ Add** → **Topic** → **From blank**

3. Configure:
   ```
   Name: Welcome to Document Assistant
   Description: Greets users and explains capabilities
   ```

4. Set **Trigger**: System → **On Conversation Start**

5. Add **Message** node:
   ```
   สวัสดีค่ะ! ยินดีต้อนรับสู่ EGAT Document Inquiry Assistant

   ฉันสามารถช่วยคุณ:
   📄 ค้นหาเอกสาร นโยบาย และขั้นตอนการทำงาน
   📋 ตอบคำถามจากเอกสารทางเทคนิค
   🔍 หาข้อมูลอ้างอิงและมาตรฐาน
   📑 แนะนำเอกสารที่เกี่ยวข้อง

   ---

   Hello! Welcome to EGAT Document Inquiry Assistant

   I can help you:
   📄 Find documents, policies, and procedures
   📋 Answer questions from technical documentation
   🔍 Locate references and standards
   📑 Suggest related documents

   What would you like to find today?
   ```

6. **Save** topic

### Step 4.2: Create Common Query Topics

Create topics for frequent queries:

#### Topic 1: Search by Department

1. Create new topic: **Search by Department**

2. Add trigger phrases:
   ```
   Show me HR documents
   ขอเอกสารของฝ่ายทรัพยากรบุคคล
   I need IT policies
   เอกสารฝ่าย IT
   Engineering standards
   ```

3. Add **Question** node:
   ```
   Which department's documents are you looking for?

   Options:
   - HR (Human Resources)
   - IT (Information Technology)
   - Engineering
   - Safety
   - Finance
   - Operations
   ```

4. Save response to variable: `Topic.Department`

5. Add **Generative answers** node:
   ```
   Input: "Show me all {Topic.Department} documents"
   Data sources: [All knowledge sources]
   ```

6. **Save** topic

#### Topic 2: Search by Document Type

1. Create new topic: **Search by Document Type**

2. Add trigger phrases:
   ```
   I need a policy
   ต้องการหาระเบียบ
   Show me procedures
   ขั้นตอนการทำงาน
   Standards
   แนวปฏิบัติ
   ```

3. Add **Question** node:
   ```
   What type of document are you looking for?

   Options:
   - Policy (นโยบาย)
   - Procedure (ขั้นตอน)
   - Standard (มาตรฐาน)
   - Guideline (แนวทาง)
   - Form (แบบฟอร์ม)
   ```

4. Save to variable: `Topic.DocumentType`

5. Add **Generative answers** node with filtered search

6. **Save** topic

#### Topic 3: Recent Documents

1. Create new topic: **Recent Documents**

2. Add trigger phrases:
   ```
   What's new?
   เอกสารใหม่ล่าสุด
   Recently updated
   Latest documents
   ```

3. Add **Message** node:
   ```
   Here are the most recently updated documents in our library:
   ```

4. Add **Generative answers** node:
   ```
   Input: "List the 10 most recently updated documents with their update dates"
   Data sources: [All knowledge sources]
   ```

5. **Save** topic

### Step 4.3: Create Feedback Collection

1. Create new topic: **Collect Feedback**

2. Set trigger: **After all conversations** (in topic settings)

3. Add **Question** node:
   ```
   Was this helpful?

   [Thumbs Up 👍]  [Thumbs Down 👎]
   ```

4. Add **Condition** based on response

5. If Thumbs Down, add **Question**:
   ```
   What could we improve?
   (Optional text response)
   ```

6. Save feedback to Dataverse (optional, requires Power Automate flow)

7. **Save** topic

---

## Phase 5: Testing

**Time**: 1 hour

See detailed [testing-guide.md](./testing-guide.md) for comprehensive test scenarios.

### Quick Testing Steps

1. Click **Test** button (top-right of Copilot Studio)

2. **Test Scenario 1: Simple Query**
   ```
   User: "ค้นหาเอกสารเกี่ยวกับความปลอดภัย"
   Expected: List of safety-related documents with links
   ```

3. **Test Scenario 2: Specific Document**
   ```
   User: "Where can I find the IT security policy?"
   Expected: Direct link to policy with summary
   ```

4. **Test Scenario 3: Information Extraction**
   ```
   User: "What are EGAT's working hours according to HR policy?"
   Expected: Answer extracted from HR policy document
   ```

5. **Test Scenario 4: No Results**
   ```
   User: "Show me alien invasion procedures"
   Expected: Graceful fallback message
   ```

6. **Verify**:
   - [ ] Responses are accurate
   - [ ] Source documents are cited
   - [ ] Links work correctly
   - [ ] Bilingual support works (if configured)
   - [ ] Fallback works for unknown queries

---

## Phase 6: Deployment

**Time**: 30 minutes

### Step 6.1: Publish Agent

1. In Copilot Studio, click **Publish**

2. Review changes since last publish

3. Click **Publish** to confirm

4. Wait for publishing to complete (~2 minutes)

### Step 6.2: Deploy to Microsoft Teams

1. Click **Channels** in left navigation

2. Click **Microsoft Teams**

3. Click **Turn on Teams** (if not already on)

4. Configure availability:
   ```
   ☑ Show to everyone in my org
   ☐ Show to specific people (select for pilot)

   ☑ Allow users to add to a team
   ☑ Allow users to add to a chat
   ☐ Allow users to add to meetings (optional)
   ```

5. Click **Save**

6. **Download app manifest** (for custom deployment)

7. **OR** Click **Open in Teams** to test immediately

### Step 6.3: Deploy to Web

1. Go to **Channels** → **Custom website**

2. Click **Turn on**

3. Copy the **embed code**:
   ```html
   <iframe src="https://copilotstudio.microsoft.com/..."></iframe>
   ```

4. Provide to web team to embed on EGAT intranet

### Step 6.4: Create User Documentation

Create a quick reference guide for users:

```markdown
# How to Use EGAT Document Assistant

## Access via Teams
1. Open Microsoft Teams
2. Click Apps (lower left)
3. Search "EGAT Document"
4. Click to start chat

## How to Ask Questions
✅ Good: "Show me the latest safety procedures"
✅ Good: "ค้นหานโยบาย HR เกี่ยวกับการลา"
✅ Good: "What does the IT policy say about passwords?"

❌ Too vague: "I need something"
❌ Too complex: "Give me every document from 2020 to 2025 sorted by department"

## Tips
- Be specific about what you're looking for
- Mention department or document type if known
- Try Thai or English - both work!
- Use document name if you know it

## Need Help?
Contact: documents@egat.co.th
```

---

## Phase 7: Monitoring & Optimization

**Time**: Ongoing

### Step 7.1: Enable Analytics

1. Go to **Analytics** in Copilot Studio

2. Configure:
   - **Session tracking**: ON
   - **CSAT tracking**: ON (if you added feedback)
   - **Custom events**: Configure as needed

3. Set up regular review schedule (weekly for first month, then monthly)

### Step 7.2: Monitor Key Metrics

Track these metrics in Analytics dashboard:

| Metric | Week 1 | Week 2 | Week 3 | Week 4 |
|--------|--------|--------|--------|--------|
| Total Sessions | ___ | ___ | ___ | ___ |
| Unique Users | ___ | ___ | ___ | ___ |
| Avg Session Duration | ___ | ___ | ___ | ___ |
| Resolution Rate | ___ | ___ | ___ | ___ |
| CSAT Score | ___ | ___ | ___ | ___ |

### Step 7.3: Review Failed Queries

1. Go to **Analytics** → **Sessions**

2. Filter by: **Outcome = Escalated or Abandoned**

3. Review conversation transcripts

4. Identify patterns:
   - Are users asking for documents not in the system?
   - Are queries in unexpected formats?
   - Are there knowledge gaps?

5. **Take Action**:
   - Add missing documents to knowledge sources
   - Create topics for common patterns
   - Update agent instructions

### Step 7.4: Optimize Knowledge Sources

Monthly tasks:

1. **Review document usage**:
   - Which documents are accessed most?
   - Which are never accessed? (consider removing)

2. **Update content**:
   - Refresh SharePoint sync
   - Upload new documents
   - Remove outdated files

3. **Improve metadata**:
   - Add keywords to frequently searched docs
   - Update descriptions
   - Categorize uncategorized documents

### Step 7.5: Iterate Based on Feedback

1. **Collect user feedback**:
   - In-chat ratings (thumbs up/down)
   - User surveys
   - Helpdesk tickets

2. **Common improvements**:
   - Add more knowledge sources
   - Create specialized topics
   - Improve response formatting
   - Add related document suggestions

3. **Advanced features to consider**:
   - Integration with EGAT's document management system
   - Proactive notifications when documents are updated
   - Personalized recommendations based on user role
   - Multi-language automatic translation

---

## Success Criteria Checklist

Before considering the implementation complete:

### Functionality
- [ ] Agent responds to document queries in < 5 seconds
- [ ] At least 500 documents indexed
- [ ] Knowledge sources sync automatically (SharePoint)
- [ ] Both Thai and English queries work
- [ ] Source citations included in responses
- [ ] Fallback handling works gracefully

### Deployment
- [ ] Available in Microsoft Teams
- [ ] Available on web (if required)
- [ ] User documentation created
- [ ] Pilot group trained
- [ ] Feedback mechanism in place

### Quality
- [ ] 80%+ query success rate (based on testing)
- [ ] No hallucinated responses
- [ ] Appropriate documents cited
- [ ] Response time < 5 seconds

### Governance
- [ ] Analytics enabled and monitored
- [ ] Regular review process established
- [ ] Knowledge source update process documented
- [ ] Support contact documented

---

## Troubleshooting Guide

### Issue: Agent responds but doesn't cite sources

**Cause**: Generative mode not configured correctly or knowledge sources not indexed

**Solution**:
1. Check **Settings** → Ensure "Generative" mode is selected
2. Go to **Knowledge** → Verify sources show "Ready"
3. Test with: "What documents do you have about [topic]?"

---

### Issue: SharePoint documents not indexing

**Cause**: Permissions or file format issues

**Solution**:
1. Verify agent has read access to SharePoint library
2. Check file formats are supported (PDF, Word, etc.)
3. Ensure files are checked in (not checked out)
4. Try removing and re-adding the knowledge source

---

### Issue: Responses in wrong language

**Cause**: Agent language settings or knowledge source language mismatch

**Solution**:
1. Check **Settings** → Agent language is set correctly
2. If bilingual, add language specification in agent instructions
3. Separate Thai and English documents (if possible)

---

### Issue: Too many irrelevant documents in responses

**Cause**: Knowledge sources too broad or poor document metadata

**Solution**:
1. Improve document metadata in SharePoint
2. Create more specific topics with filtered searches
3. Update agent instructions to be more selective
4. Consider splitting into multiple specialized agents

---

## Next Steps After Implementation

1. **Week 1**: Monitor closely, fix issues immediately
2. **Week 2-4**: Gather feedback, make iterative improvements
3. **Month 2**: Expand to more document sources
4. **Month 3**: Add advanced features based on usage patterns
5. **Ongoing**: Regular monthly reviews and optimizations

---

## Additional Resources

- [Copilot Studio Documentation](https://learn.microsoft.com/en-us/microsoft-copilot-studio/)
- [Knowledge Sources Guide](https://learn.microsoft.com/en-us/microsoft-copilot-studio/knowledge-overview)
- [Generative Answers](https://learn.microsoft.com/en-us/microsoft-copilot-studio/nlu-generative-answers)
- [SharePoint Integration](https://learn.microsoft.com/en-us/microsoft-copilot-studio/knowledge-sharepoint)

---

**Congratulations!** You've completed the implementation of your Document Inquiry Chatbot.

**Need help?** Refer to [testing-guide.md](./testing-guide.md) for detailed testing procedures.
