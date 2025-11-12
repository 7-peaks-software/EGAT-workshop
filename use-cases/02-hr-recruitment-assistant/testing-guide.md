# Testing Guide: HR Recruitment Assistant

## Overview

Comprehensive testing procedures to ensure the HR Recruitment Assistant works correctly before deploying to the HR team.

## Testing Phases

1. **CV Extraction Testing** - Verify information extraction accuracy
2. **Job Matching Testing** - Test candidate-to-job matching logic
3. **Comparison Testing** - Test multi-candidate comparisons
4. **Search Testing** - Test skill and criteria-based search
5. **Integration Testing** - Test SharePoint and system integration
6. **User Acceptance Testing** - Real recruiter scenarios
7. **Performance Testing** - Speed and scalability
8. **Security Testing** - Privacy and access control

---

## Phase 1: CV Extraction Testing

### Test 1.1: Basic Information Extraction

**Objective**: Verify agent extracts basic candidate information

**Test CVs Needed**: 5 sample CVs with varied formats

**Steps**:
1. Upload CV to agent
2. Ask: "Extract information from this CV"
3. Verify extracted data

**Expected Results**:
- ✅ Full name extracted correctly
- ✅ Contact information (email, phone)
- ✅ Education (degrees, institutions, graduation years)
- ✅ Work experience (companies, positions, dates)
- ✅ Technical skills listed
- ✅ Languages with proficiency levels
- ✅ Certifications (if any)
- ✅ Total years of experience calculated

**Test Matrix**:

| CV Type | Name | Education | Experience | Skills | Languages | Pass/Fail |
|---------|------|-----------|------------|--------|-----------|-----------|
| English PDF | ✓ | ✓ | ✓ | ✓ | ✓ | [ ] |
| Thai PDF | ✓ | ✓ | ✓ | ✓ | ✓ | [ ] |
| Mixed Language | ✓ | ✓ | ✓ | ✓ | ✓ | [ ] |
| Complex Layout | ✓ | ✓ | ✓ | ✓ | ✓ | [ ] |
| Scanned CV | ✓ | ✓ | ✓ | ✓ | ✓ | [ ] |

**Acceptance Criteria**: ≥ 90% accuracy on each field

---

### Test 1.2: Edge Cases

**Test with**:
1. **Very Short CV** (1 page, minimal experience)
   - Expected: Extract available info, note gaps

2. **Very Long CV** (5+ pages, extensive experience)
   - Expected: Summarize effectively, calculate total experience

3. **Non-Standard Format** (creative design, unusual structure)
   - Expected: Best effort extraction, request standard format if needed

4. **Missing Sections** (no education, or no certifications)
   - Expected: Note what's missing, don't hallucinate data

5. **Multiple Jobs Simultaneously** (overlapping dates)
   - Expected: Handle correctly, calculate experience accurately

**Status**: [ ] Pass [ ] Fail

---

## Phase 2: Job Matching Testing

### Test 2.1: Simple Match (High Match Score)

**Setup**:
- Job: "Senior Software Engineer - 5 years exp, Python, Bachelor required"
- CV: Candidate with 7 years, Python expert, Master's degree

**Steps**:
1. Upload CV
2. Ask: "Match this candidate to Senior Software Engineer position"
3. Review match results

**Expected Results**:
- ✅ Match score: 85-95%
- ✅ Education: Exceeds requirements (Master > Bachelor)
- ✅ Experience: Exceeds requirements (7 > 5 years)
- ✅ Skills: Python confirmed
- ✅ Recommendation: "Proceed to Interview"
- ✅ Strengths listed correctly
- ✅ Gaps: None or minor

**Actual Results**: _____________

**Status**: [ ] Pass [ ] Fail

---

### Test 2.2: Moderate Match (Maybe)

**Setup**:
- Job: "Electrical Engineer - 8 years exp, PMP certified, Master preferred"
- CV: Candidate with 6 years, no PMP, Bachelor only

**Expected Results**:
- ✅ Match score: 60-75%
- ✅ Experience: Slightly below (6 vs 8)
- ✅ Certification: Gap identified (no PMP)
- ✅ Education: Meets minimum but not preferred
- ✅ Recommendation: "Maybe - Consider if other qualifications strong"
- ✅ Gaps clearly listed

**Status**: [ ] Pass [ ] Fail

---

### Test 2.3: Low Match (Reject)

**Setup**:
- Job: "Senior Manager - 10+ years, MBA, Leadership experience"
- CV: Fresh graduate, 1 year exp, Bachelor only

**Expected Results**:
- ✅ Match score: < 40%
- ✅ Major gaps identified (experience, education, leadership)
- ✅ Recommendation: "Does not meet minimum requirements"
- ✅ Clear explanation of why not suitable

**Status**: [ ] Pass [ ] Fail

---

### Test 2.4: Knockout Criteria

**Setup**:
- Job has must-have: "Thai nationality required" or "Security clearance required"
- CV: Candidate doesn't meet must-have

**Expected Results**:
- ✅ Immediately identified as knockout
- ✅ Match score reflects this (very low)
- ✅ Clear statement: "Does not meet required criterion: [X]"

**Status**: [ ] Pass [ ] Fail

---

## Phase 3: Comparison Testing

### Test 3.1: Compare 3 Candidates

**Setup**: 3 CVs for same position with varying qualifications

**Steps**:
1. Upload 3 CVs
2. Ask: "Compare these candidates for [Position]"
3. Review comparison table

**Expected Results**:
- ✅ Comparison table created
- ✅ All 3 candidates listed
- ✅ Match scores calculated for each
- ✅ Side-by-side comparison of key criteria
- ✅ Ranking provided (1st, 2nd, 3rd)
- ✅ Recommendation for each (Proceed/Maybe/Reject)
- ✅ Summary of relative strengths

**Sample Expected Format**:
```
| Candidate | Match | Education | Experience | Key Skills | Strengths | Gaps | Rank |
|-----------|-------|-----------|------------|------------|-----------|------|------|
| John S.   | 87%   | Master    | 8 yrs      | 9/10       | [list]    | Minor| 1st  |
| Jane D.   | 72%   | Bachelor  | 5 yrs      | 7/10       | [list]    | [gap]| 2nd  |
| Bob T.    | 55%   | Bachelor  | 3 yrs      | 5/10       | [list]    | [gap]| 3rd  |

Recommendation: Proceed with John S. and Jane D. for interviews.
```

**Status**: [ ] Pass [ ] Fail

---

### Test 3.2: Compare Different Positions

**Setup**: Same CV evaluated against 2 different positions

**Expected Results**:
- ✅ Different match scores for each position
- ✅ Different gaps/strengths identified
- ✅ Recommendation varies based on position fit

**Status**: [ ] Pass [ ] Fail

---

## Phase 4: Search Testing

### Test 4.1: Skill-Based Search

**Test Queries**:
```
1. "Find all candidates who know Python"
2. "Show me candidates with Java and 5+ years experience"
3. "Who has PMP certification?"
4. "Find candidates with Master's degree in Electrical Engineering"
```

**Expected Results**:
- ✅ Relevant candidates found
- ✅ Match explained (where skill mentioned in CV)
- ✅ Ranked by relevance
- ✅ No false positives (candidates without the skill)

**Status**: [ ] Pass [ ] Fail

---

### Test 4.2: Complex Criteria

**Test Query**:
```
"Find candidates with:
- Bachelor degree or higher
- 3-5 years experience
- Python AND SQL
- English proficient
- Available immediately"
```

**Expected Results**:
- ✅ All criteria checked
- ✅ Only candidates meeting ALL criteria shown
- ✅ Explanation of why each matches

**Status**: [ ] Pass [ ] Fail

---

## Phase 5: Integration Testing

### Test 5.1: SharePoint Job Description Integration

**Steps**:
1. Upload new job description to SharePoint
2. Wait for indexing (15-30 minutes)
3. Ask agent: "What are the requirements for [new position]?"

**Expected Results**:
- ✅ Agent finds new job description
- ✅ Extracts requirements correctly
- ✅ Can use for matching

**Status**: [ ] Pass [ ] Fail

---

### Test 5.2: CV Folder Organization

**Steps**:
1. Upload CVs to position-specific SharePoint folders
2. Ask: "How many candidates have applied for [Position]?"

**Expected Results**:
- ✅ Agent counts CVs in correct folder
- ✅ Accurate count
- ✅ Can list candidate names

**Status**: [ ] Pass [ ] Fail

---

## Phase 6: User Acceptance Testing

### Test 6.1: Recruiter Workflow - New Position

**Persona**: HR Recruiter posting new position

**Scenario**:
1. Upload job description to SharePoint
2. Start receiving CVs
3. Use agent to screen first 10 applicants
4. Create shortlist of top 3

**Test Steps**:
1. Recruiter uploads JD
2. Uploads 10 sample CVs
3. Asks agent: "Screen all candidates for [Position ID]"
4. Reviews match scores
5. Asks: "Show me top 3 candidates"
6. Reviews comparison table

**Success Criteria**:
- [ ] Process completed in < 30 minutes (vs 2-3 hours manual)
- [ ] Shortlist quality matches recruiter's expert judgment
- [ ] No qualified candidates missed
- [ ] Recruiter satisfaction: ≥ 4/5

**Recruiter Feedback**: _____________

**Status**: [ ] Pass [ ] Fail

---

### Test 6.2: Hiring Manager Review

**Persona**: Department manager reviewing candidates

**Scenario**:
1. Recruiter shares shortlist via Teams
2. Manager reviews candidate summaries
3. Manager asks follow-up questions about candidates

**Test Steps**:
1. Share shortlist link in Teams
2. Manager asks: "Tell me more about candidate X's experience with [specific skill]"
3. Manager compares 2 candidates: "Compare John vs Jane for leadership experience"

**Success Criteria**:
- [ ] Manager can access candidate info easily
- [ ] Follow-up questions answered accurately
- [ ] Comparison helps decision-making

**Manager Feedback**: _____________

**Status**: [ ] Pass [ ] Fail

---

## Phase 7: Performance Testing

### Test 7.1: Response Time

**Objective**: Ensure fast processing

**Test**:
- Upload CV
- Measure time to complete extraction

**Acceptance Criteria**:
- Simple extraction: < 10 seconds
- Full match analysis: < 30 seconds
- Comparison of 3 CVs: < 60 seconds

**Results**:
| Task | Target | Actual | Pass/Fail |
|------|--------|--------|-----------|
| Extract CV | < 10s | _____ | [ ] |
| Match to job | < 30s | _____ | [ ] |
| Compare 3 CVs | < 60s | _____ | [ ] |

---

### Test 7.2: Volume Handling

**Objective**: Test with realistic volume

**Test**:
- Upload 50 CVs for one position
- Ask: "Screen all 50 candidates for [Position]"
- Measure processing time

**Expected**:
- ✅ All CVs processed
- ✅ No timeouts or errors
- ✅ Results provided in reasonable time (< 10 minutes)

**Status**: [ ] Pass [ ] Fail

---

## Phase 8: Security Testing

### Test 8.1: Access Control

**Objective**: Verify only authorized users can access

**Test**:
1. Test with HR recruiter account → Should work
2. Test with non-HR employee → Should be denied
3. Test with external email → Should be denied

**Status**: [ ] Pass [ ] Fail

---

### Test 8.2: Candidate Privacy

**Objective**: Ensure candidate data is protected

**Checks**:
- [ ] CVs not visible in general SharePoint search
- [ ] Agent doesn't expose candidate data to unauthorized users
- [ ] No candidate info in agent responses to non-HR users
- [ ] Conversation history is private

**Status**: [ ] Pass [ ] Fail

---

### Test 8.3: Data Retention

**Objective**: Verify data deletion policies work

**Test**:
1. Mark candidate as "Rejected" with date 1 year ago
2. Run retention policy
3. Verify CV and data deleted

**Status**: [ ] Pass [ ] Fail

---

## Test Result Summary

### Overall Statistics

Total Tests: _____ / 20+

**By Category**:
- CV Extraction: _____ / 2
- Job Matching: _____ / 4
- Comparison: _____ / 2
- Search: _____ / 2
- Integration: _____ / 2
- UAT: _____ / 2
- Performance: _____ / 2
- Security: _____ / 3

**Pass Rate**: _____%

**Target**: > 95% pass rate

---

## Critical Issues Found

| Issue # | Description | Severity | Status |
|---------|-------------|----------|--------|
| 1 | | [ ] Critical [ ] High [ ] Medium [ ] Low | [ ] Open [ ] Fixed |
| 2 | | [ ] Critical [ ] High [ ] Medium [ ] Low | [ ] Open [ ] Fixed |
| 3 | | [ ] Critical [ ] High [ ] Medium [ ] Low | [ ] Open [ ] Fixed |

**Severity Definitions**:
- **Critical**: Data extraction completely wrong, security breach
- **High**: Major matching errors, poor user experience
- **Medium**: Minor inaccuracies, workaround available
- **Low**: Cosmetic issues, nice-to-have improvements

---

## Go/No-Go Decision Criteria

**GO** if:
- [ ] Pass rate ≥ 95%
- [ ] No critical issues open
- [ ] No more than 2 high-severity issues open
- [ ] CV extraction accuracy ≥ 90%
- [ ] Match score consistency verified by recruiters
- [ ] Security tests all pass
- [ ] Recruiter satisfaction ≥ 4.0/5.0

**NO-GO** if:
- [ ] Critical issues exist
- [ ] Pass rate < 90%
- [ ] Security tests fail
- [ ] CV extraction unreliable
- [ ] Recruiters don't trust match scores

**Decision**: [ ] GO [ ] NO-GO

**Sign-off**:
- HR Lead: ______________ Date: ______
- Technical Lead: ______________ Date: ______
- Compliance Officer: ______________ Date: ______

---

## Post-Production Monitoring

After deployment, monitor weekly for first month:

### Week 1 Checklist
- [ ] Monday: Test with 5 real CVs
- [ ] Wednesday: Check match score accuracy with recruiter feedback
- [ ] Friday: Review analytics for any issues

### Week 2-4 Checklist
- [ ] Process 20+ real CVs
- [ ] Gather recruiter feedback (survey)
- [ ] Monitor error rates
- [ ] Check for any privacy issues
- [ ] Verify data retention working

---

## Sample Test Data Requirements

Prepare these sample CVs for testing:

### Required CV Profiles

1. **Perfect Match**: Exceeds all requirements
2. **Good Match**: Meets all requirements, some extras
3. **Moderate Match**: Meets most requirements, few gaps
4. **Poor Match**: Meets minimum only
5. **Unqualified**: Doesn't meet minimum requirements
6. **English CV**: Professional English format
7. **Thai CV**: Professional Thai format
8. **Mixed Language**: Thai and English sections
9. **Scanned CV**: Image-based PDF
10. **Complex Layout**: Creative/design-heavy format

### Required Job Descriptions

1. **Entry Level**: Minimal requirements
2. **Mid Level**: Moderate experience required
3. **Senior Level**: Extensive experience required
4. **Technical Role**: Heavy technical skills focus
5. **Management Role**: Leadership emphasis

---

## Automated Testing (Advanced)

For recurring tests, consider automation:

### Test Cases in Copilot Studio

Create these automated test cases:

```yaml
Test Case 1: Basic CV Extraction
Input: Upload sample CV
Expected: Extract name, education, experience
Pass Criteria: All fields extracted with >90% accuracy

Test Case 2: Match Score Consistency
Input: Same CV, same JD, run 5 times
Expected: Match score variance < 5%
Pass Criteria: Consistent scoring

Test Case 3: Comparison Ranking
Input: 3 CVs (good, moderate, poor)
Expected: Ranked correctly (good > moderate > poor)
Pass Criteria: Correct ranking
```

---

## Calibration Process

Before production use:

### Step 1: Baseline with Expert Recruiters

1. Have 3 expert recruiters manually screen 20 CVs
2. Record their match scores and rankings
3. Run same CVs through agent
4. Compare results

**Target Agreement**: ≥ 80% agreement on top 5 candidates

### Step 2: Adjust Matching Criteria

If agreement < 80%:
1. Review disagreements
2. Understand recruiter reasoning
3. Update agent instructions
4. Re-test

### Step 3: Ongoing Calibration

Monthly:
1. Sample 10 agent evaluations
2. Have recruiter review
3. Calculate agreement rate
4. Adjust as needed

---

**Testing Complete!**

Once all tests pass, proceed with production deployment as outlined in [implementation-guide.md](./implementation-guide.md#phase-7-deployment).
