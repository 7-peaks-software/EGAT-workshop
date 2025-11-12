# Implementation Guide: HR Recruitment Assistant

## Table of Contents

1. [Prerequisites Check](#prerequisites-check)
2. [Phase 1: Job Description Preparation](#phase-1-job-description-preparation)
3. [Phase 2: CV Storage Setup](#phase-2-cv-storage-setup)
4. [Phase 3: Agent Creation](#phase-3-agent-creation)
5. [Phase 4: Document Processing Configuration](#phase-4-document-processing-configuration)
6. [Phase 5: Matching Logic Design](#phase-5-matching-logic-design)
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
- [ ] Access to SharePoint sites for job descriptions and CVs
- [ ] Completed Lab 01 and Lab 02 from this workshop
- [ ] Completed Use Case 01 (Document Inquiry) - recommended
- [ ] Copilot Studio license activated

---

## Phase 1: Job Description Preparation

**Time**: 1 hour

### Step 1.1: Create Job Description Repository

1. **Create SharePoint Site** (or use existing HR site)
   ```
   Site name: EGAT HR Recruitment
   URL: https://egat.sharepoint.com/sites/HRRecruitment
   ```

2. **Create Document Libraries**:
   ```
   /sites/HRRecruitment
     ├── Job-Descriptions/
     │   ├── Active-Positions/
     │   ├── Closed-Positions/
     │   └── Templates/
     └── Candidate-CVs/
         ├── 2025-01-Engineer-Position/
         ├── 2025-02-Analyst-Position/
         └── [One folder per position]
   ```

3. **Add Metadata Columns** to Job-Descriptions library:
   - **Position Title** (Text)
   - **Position ID** (Text)
   - **Department** (Choice)
   - **Required Experience** (Number) - years
   - **Required Education** (Choice: Bachelor, Master, PhD)
   - **Key Skills** (Multi-line text)
   - **Status** (Choice: Active, Filled, Closed)
   - **Posted Date** (Date)
   - **Closing Date** (Date)

### Step 1.2: Prepare Job Description Template

Create standardized JD template (see [assets/sample-job-description.md](./assets/sample-job-description.md)):

```markdown
# Position Title: [Title]
**Position ID**: [ID]
**Department**: [Department]
**Posted**: [Date]

## Position Overview
[Brief description]

## Required Qualifications
### Education
- [Degree level and field]

### Experience
- [Years and type]

### Technical Skills (Required)
- [Skill 1]
- [Skill 2]
- [Skill 3]

### Technical Skills (Preferred)
- [Skill 1]
- [Skill 2]

### Language Requirements
- Thai: [Level]
- English: [Level]

### Certifications (if applicable)
- [Certification 1]
- [Certification 2]

## Responsibilities
1. [Responsibility 1]
2. [Responsibility 2]

## Evaluation Criteria
### Must Have (Knockout criteria)
- [Criteria 1]
- [Criteria 2]

### Weighted Scoring
- Education (20%)
- Experience (30%)
- Technical Skills (30%)
- Language (10%)
- Certifications (10%)
```

### Step 1.3: Upload Active Job Descriptions

1. Upload current open positions to SharePoint
2. Fill in all metadata fields
3. Ensure Status = "Active"
4. Test SharePoint search to verify indexing

---

## Phase 2: CV Storage Setup

**Time**: 30 minutes

### Step 2.1: Create CV Storage Structure

1. **For each open position**, create a folder:
   ```
   /Candidate-CVs/2025-01-Senior-Engineer/
     ├── CVs-Received/
     ├── CVs-Shortlisted/
     └── CVs-Rejected/
   ```

2. **Naming Convention** for CV files:
   ```
   Format: [ApplicantName]_[PositionID]_[Date].pdf
   Example: JohnSmith_ENG2025-01_2025-01-15.pdf
   ```

### Step 2.2: Configure Upload Process

**Option A: Email to SharePoint** (Recommended)
1. Enable email for CV library
2. Get library email address
3. Share with recruiting team
4. Recruiters forward applications to library

**Option B: Manual Upload**
1. Recruiters upload CVs directly to SharePoint
2. Or use Power Automate to sync from email attachment

**Option C: Application Form**
1. Create Power Apps form for candidates
2. Candidates upload CV directly
3. Automatically saves to SharePoint with metadata

### Step 2.3: CV Quality Checklist

Ensure CVs meet requirements:
- [ ] File format: PDF or Word (not images)
- [ ] File size: < 10 MB
- [ ] Text is searchable (not scanned images without OCR)
- [ ] Naming convention followed
- [ ] Placed in correct position folder

---

## Phase 3: Agent Creation

**Time**: 45 minutes

### Step 3.1: Create New Agent

1. Navigate to [Microsoft Copilot Studio](https://copilotstudio.microsoft.com/)

2. Click **+ New agent**

3. Configure:
   ```
   Name: EGAT HR Recruitment Assistant
   Description: AI assistant to help recruiters screen, evaluate, and match candidates
   Language: Thai and English
   Icon: Upload HR/recruitment icon
   ```

4. Click **Create**

### Step 3.2: Configure Agent Settings

1. Click **Settings** → **Generative AI**

2. Configure:
   ```
   Response mode: ● Generative (AI-powered)
   Content moderation: ● Medium
   How strict: ● Medium
   ```

3. Add **Agent Instructions**:
   ```
   You are the EGAT HR Recruitment Assistant. Your role is to help HR recruiters
   screen candidates, extract information from CVs/Resumes, and match candidates
   to job requirements.

   Core Capabilities:
   1. Extract information from CV/Resume documents (PDF, Word)
   2. Summarize candidate qualifications
   3. Match candidates to job description requirements
   4. Score candidates based on evaluation criteria
   5. Create comparison tables for shortlisting
   6. Answer questions about candidates

   Guidelines:
   1. Always extract: Name, Education, Experience (years), Skills, Languages, Certifications
   2. When matching candidates, use the job description requirements
   3. Be objective and consistent in evaluation
   4. Highlight both strengths and gaps
   5. Provide match scores with explanations
   6. Respect candidate privacy (internal use only)
   7. Support both Thai and English CVs

   Evaluation Process:
   1. Extract candidate information from CV
   2. Load job description requirements
   3. Compare candidate qualifications vs requirements
   4. Calculate match score based on criteria
   5. Provide recommendation with justification

   Response Format:
   - Use structured summaries (bullets, tables)
   - Always cite source documents
   - Provide match percentage when comparing
   - Include both qualifications and gaps
   ```

4. **Save**

### Step 3.3: Create Conversation Starters

Add these suggested prompts for recruiters:

```
1. "Analyze this CV for position [Position ID]"
2. "Compare candidates for [Position Title]"
3. "Show me top 5 candidates for [Position]"
4. "Summarize candidate qualifications"
5. "What candidates have [specific skill]?"
```

---

## Phase 4: Document Processing Configuration

**Time**: 2 hours

### Step 4.1: Add Job Descriptions as Knowledge Source

1. Go to **Knowledge** → **+ Add knowledge**

2. Select **SharePoint**

3. Configure:
   ```
   Type: Document Library
   Site: https://egat.sharepoint.com/sites/HRRecruitment
   Library: Job-Descriptions
   Filter: Status = "Active"
   ```

4. Click **Add** and wait for indexing

### Step 4.2: Add CV Document Processing

**Method 1: Upload CVs directly to agent**

1. Go to **Knowledge** → **+ Add knowledge**

2. Select **Files**

3. Upload sample CVs for testing

4. Organize by position folders

**Method 2: Link to SharePoint CV library**

1. **+ Add knowledge** → **SharePoint**

2. Add each position's CV folder:
   ```
   Library: Candidate-CVs
   Folder: 2025-01-Senior-Engineer/CVs-Received
   ```

3. Repeat for each active position

**Method 3: Azure AI Document Intelligence (Advanced)**

For better extraction from complex CVs:

1. Create Azure AI Document Intelligence resource

2. In Copilot Studio, go to **Settings** → **AI capabilities**

3. Enable **Azure AI Document Intelligence**

4. Connect to your Azure resource

5. This improves extraction from:
   - Scanned documents
   - Complex layouts
   - Multiple languages
   - Tables and structured data

### Step 4.3: Configure Document Extraction

1. Create a **Topic**: "Extract CV Information"

2. Add **Generative Answers** node:
   ```
   System prompt:
   "Extract the following information from the CV document:

   1. Full Name
   2. Contact Information (email, phone)
   3. Education (degrees, institutions, years)
   4. Work Experience (companies, positions, durations, responsibilities)
   5. Technical Skills (list all)
   6. Languages (with proficiency levels)
   7. Certifications (with issuing organizations and dates)
   8. Total Years of Experience (calculate)

   Format the output as a structured summary with clear sections."

   Data sources: [CV documents]
   ```

3. Save output to variable: `Topic.CandidateInfo`

---

## Phase 5: Matching Logic Design

**Time**: 2 hours

### Step 5.1: Create "Match Candidate to Job" Topic

1. Create new topic: **Match Candidate to Job**

2. Add trigger phrases:
   ```
   - "Match this CV to [position]"
   - "How does this candidate fit [job title]"
   - "Evaluate candidate for [position ID]"
   - "Score this CV against requirements"
   ```

3. **Flow Design**:

   **Node 1: Question** - Get Position ID
   ```
   "Which position are you evaluating this candidate for?"

   [Show list of active positions from SharePoint metadata]
   ```
   Save to: `Topic.PositionID`

   **Node 2: Question** - Get CV
   ```
   "Please upload the candidate's CV or provide the file name"

   [File upload or text input]
   ```
   Save to: `Topic.CVFile`

   **Node 3: Extract** - Get Job Requirements
   ```
   Use Generative Answers node:
   Input: "Get the requirements for position {Topic.PositionID}"
   Data source: Job-Descriptions library

   Extract:
   - Required education
   - Required experience (years)
   - Required technical skills
   - Preferred skills
   - Language requirements
   - Certifications
   ```
   Save to: `Topic.JobRequirements`

   **Node 4: Extract** - Get Candidate Info
   ```
   Use Generative Answers node:
   Input: "Extract all qualifications from {Topic.CVFile}"
   Data source: CV document
   ```
   Save to: `Topic.CandidateQuals`

   **Node 5: Compare** - Match & Score
   ```
   Use Generative Answers node with this prompt:

   "Compare the candidate qualifications against job requirements and provide:

   1. Match Score (0-100%)
   2. Breakdown by category:
      - Education Match: [Score] - [Explanation]
      - Experience Match: [Score] - [Explanation]
      - Skills Match: [Score] - [Explanation]
      - Language Match: [Score] - [Explanation]
      - Certification Match: [Score] - [Explanation]

   3. Strengths (what candidate has that matches or exceeds requirements)
   4. Gaps (what candidate lacks)
   5. Overall Recommendation: [Proceed to Interview / Maybe / Reject]
   6. Justification for recommendation

   Candidate: {Topic.CandidateQuals}
   Requirements: {Topic.JobRequirements}

   Be objective and consistent in scoring."
   ```

   **Node 6: Display Results**
   ```
   Show match results in a structured format with:
   - Match score (%)
   - Category scores
   - Strengths and gaps
   - Recommendation
   ```

4. **Save** topic

### Step 5.2: Create "Compare Multiple Candidates" Topic

1. Create new topic: **Compare Candidates**

2. Flow:
   - Ask for Position ID
   - Ask "How many candidates to compare?" (2-5)
   - For each candidate, upload CV or select from library
   - Extract qualifications for all
   - Create comparison table
   - Rank candidates
   - Show top recommendations

3. **Comparison Table Format**:
   ```markdown
   | Candidate | Match Score | Education | Experience | Skills Match | Strengths | Gaps | Recommendation |
   |-----------|-------------|-----------|------------|--------------|-----------|------|----------------|
   | John S.   | 87%         | ✓ Master  | ✓ 8 years  | 9/10         | [list]    | [list] | Proceed       |
   | Jane D.   | 72%         | ✓ Bachelor| ⚠ 4 years  | 7/10         | [list]    | [list] | Maybe         |
   ```

### Step 5.3: Create "Find Candidates with Skill" Topic

1. Create topic: **Search by Skill**

2. Trigger:
   ```
   "Find candidates with [skill name]"
   "Who knows [technology/language]?"
   "Show me all [job title] candidates"
   ```

3. Use Generative Answers to search across all CVs

4. Return list with match details

---

## Phase 6: Testing

**Time**: 1-2 hours

See detailed [testing-guide.md](./testing-guide.md) for comprehensive test scenarios.

### Quick Testing Steps

1. **Test CV Extraction**:
   ```
   Upload a sample CV
   Ask: "Extract information from this CV"
   Verify: Name, education, experience, skills extracted correctly
   ```

2. **Test Job Matching**:
   ```
   Upload CV
   Ask: "Match this candidate to position ENG-2025-01"
   Verify: Match score, breakdown, strengths/gaps shown
   ```

3. **Test Comparison**:
   ```
   Upload 3 CVs
   Ask: "Compare these candidates for Senior Engineer position"
   Verify: Comparison table created, ranked correctly
   ```

4. **Test Search**:
   ```
   Ask: "Find all candidates who know Python and have 5+ years experience"
   Verify: Relevant candidates found, match explained
   ```

---

## Phase 7: Deployment

**Time**: 30 minutes

### Step 7.1: Publish Agent

1. Click **Publish**
2. Review changes
3. Click **Publish** to confirm

### Step 7.2: Deploy to Microsoft Teams

1. Go to **Channels** → **Microsoft Teams**

2. Configure:
   ```
   ☑ Show to HR team only (or specific security group)
   ☑ Allow users to add to a chat
   ```

3. **Generate Teams app package**

4. Share link with HR recruiting team

### Step 7.3: Create User Guide for Recruiters

```markdown
# EGAT HR Recruitment Assistant - Quick Guide

## How to Use

### 1. Analyze a Single CV
1. Open agent in Teams
2. Upload CV file
3. Say: "Analyze this CV for position [Position ID]"
4. Review: Match score, strengths, gaps

### 2. Compare Multiple Candidates
1. Say: "I want to compare candidates for [Position]"
2. Upload CVs when prompted (2-5 candidates)
3. Review comparison table
4. Check ranking and recommendations

### 3. Search for Specific Skills
1. Say: "Find candidates with [skill] and [years] experience"
2. Review results
3. Ask for details: "Tell me more about [candidate name]"

## Best Practices
- Use clear, specific position IDs
- Ensure CVs are searchable PDFs (not images)
- Review AI recommendations but make final decision
- Keep job descriptions up to date
- Provide feedback to improve matching

## Need Help?
Contact: [HR Support Email]
```

---

## Phase 8: Monitoring & Optimization

**Time**: Ongoing

### Step 8.1: Enable Analytics

1. Go to **Analytics** in Copilot Studio

2. Monitor:
   - Number of CVs processed
   - Most searched skills
   - Average match scores
   - Recruiter usage patterns

### Step 8.2: Collect Recruiter Feedback

Weekly for first month:

1. **Accuracy Check**:
   - Are match scores consistent with recruiter assessment?
   - Are important qualifications being missed?
   - Are any false positives/negatives?

2. **Usability**:
   - Is the conversation flow intuitive?
   - Are responses fast enough?
   - Are outputs formatted well?

3. **Feature Requests**:
   - What additional criteria would help?
   - What other tasks could be automated?

### Step 8.3: Continuous Improvement

Monthly tasks:

1. **Update Job Descriptions**:
   - Keep requirements current
   - Add new positions
   - Archive filled positions

2. **Refine Matching Criteria**:
   - Adjust weighting based on hiring outcomes
   - Add new skills to watch list
   - Update evaluation rubric

3. **Expand Knowledge**:
   - Add more job description templates
   - Include industry salary benchmarks
   - Add interview question banks

4. **Integration Opportunities**:
   - Connect to EGAT's HRIS system
   - Integrate with calendar for interview scheduling
   - Link to background check processes

---

## Success Criteria Checklist

Before considering implementation complete:

### Functionality
- [ ] Agent extracts key info from CVs accurately (>90% accuracy)
- [ ] Match scores are consistent and explainable
- [ ] Comparison tables work for 2-5 candidates
- [ ] Search by skills returns relevant results
- [ ] Response time < 30 seconds per CV

### Data Quality
- [ ] At least 5 job descriptions in system
- [ ] Job descriptions follow standard template
- [ ] 20+ sample CVs for testing
- [ ] CV storage structure organized

### Deployment
- [ ] Available to HR team in Teams
- [ ] User guide distributed
- [ ] Training session conducted
- [ ] Feedback mechanism in place

### Quality
- [ ] Recruiter satisfaction ≥ 4.0/5.0
- [ ] Time savings ≥ 50% vs manual screening
- [ ] No critical qualification misses
- [ ] Consistent evaluation across recruiters

---

## Troubleshooting Guide

### Issue: CV information not extracted accurately

**Cause**: Scanned image instead of searchable PDF, or complex layout

**Solution**:
1. Use Azure AI Document Intelligence for better OCR
2. Ask candidates to provide text-based CVs
3. Manually review and correct extracted data
4. Update agent instructions for specific formats

---

### Issue: Match scores seem inconsistent

**Cause**: Job requirements not specific enough, or weighting unclear

**Solution**:
1. Improve job description specificity
2. Define clear evaluation rubric
3. Add knockout criteria (must-have requirements)
4. Calibrate scoring with sample CVs

---

### Issue: Agent doesn't find relevant candidates

**Cause**: Keyword mismatch or synonym issues

**Solution**:
1. Add skill synonyms to job descriptions (e.g., "JS" = "JavaScript")
2. Use broader search terms
3. Improve CV metadata/tagging
4. Update agent instructions to recognize alternatives

---

### Issue: Slow response times

**Cause**: Large number of CVs to process, or complex documents

**Solution**:
1. Process CVs in smaller batches
2. Pre-extract information during upload
3. Use indexed metadata instead of full document search
4. Upgrade to premium Copilot Studio capacity

---

## Next Steps After Implementation

1. **Week 1**: Monitor closely, fix issues immediately
2. **Week 2-4**: Gather recruiter feedback, refine matching
3. **Month 2**: Expand to more positions/departments
4. **Month 3**: Add advanced features (interview scheduling, automated emails)
5. **Ongoing**: Monthly reviews and optimizations

---

## Additional Resources

- [Microsoft Copilot Studio Documentation](https://learn.microsoft.com/en-us/microsoft-copilot-studio/)
- [Azure AI Document Intelligence](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/)
- [SharePoint Metadata Best Practices](https://learn.microsoft.com/en-us/sharepoint/metadata-and-keywords)

---

**Congratulations!** You've implemented an AI-powered HR Recruitment Assistant.

**Need help?** Refer to [testing-guide.md](./testing-guide.md) for comprehensive testing procedures.
