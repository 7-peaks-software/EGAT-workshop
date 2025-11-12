# Testing Guide: Document Inquiry Chatbot

## Overview

This guide provides comprehensive testing procedures to ensure your Document Inquiry Chatbot works correctly before deploying to production.

## Testing Phases

1. **Unit Testing** - Test individual features
2. **Integration Testing** - Test knowledge source connections
3. **User Acceptance Testing** - Real user scenarios
4. **Performance Testing** - Speed and reliability
5. **Security Testing** - Permissions and data access

---

## Phase 1: Unit Testing

### Test 1.1: Welcome Message

**Objective**: Verify welcome message displays correctly

**Steps**:
1. Open agent in Test pane
2. Start new conversation
3. Observe welcome message

**Expected Result**:
- ✅ Welcome message appears in both Thai and English
- ✅ Message lists agent capabilities
- ✅ Message is clear and welcoming

**Actual Result**: ____________

**Status**: [ ] Pass [ ] Fail

---

### Test 1.2: Basic Knowledge Query (Thai)

**Objective**: Test Thai language document search

**Test Query**:
```
ค้นหาเอกสารเกี่ยวกับความปลอดภัย
(Find documents about safety)
```

**Expected Result**:
- ✅ Agent understands Thai query
- ✅ Returns relevant safety documents
- ✅ Provides document links
- ✅ Cites sources correctly

**Actual Result**: ____________

**Status**: [ ] Pass [ ] Fail

---

### Test 1.3: Basic Knowledge Query (English)

**Objective**: Test English language document search

**Test Query**:
```
Show me IT security policies
```

**Expected Result**:
- ✅ Agent understands English query
- ✅ Returns IT security policy documents
- ✅ Provides clickable links
- ✅ Includes brief summary

**Actual Result**: ____________

**Status**: [ ] Pass [ ] Fail

---

### Test 1.4: Specific Document Request

**Objective**: Test finding a specific document by name

**Test Query**:
```
I need the employee handbook
ต้องการคู่มือพนักงาน
```

**Expected Result**:
- ✅ Identifies specific document
- ✅ Provides direct link
- ✅ Offers to summarize if asked

**Actual Result**: ____________

**Status**: [ ] Pass [ ] Fail

---

### Test 1.5: Information Extraction

**Objective**: Test extracting specific information from documents

**Test Query**:
```
What are EGAT's working hours according to company policy?
เวลาทำงานของ กฟผ. คืออะไรครับ ตามระเบียบบริษัท?
```

**Expected Result**:
- ✅ Finds relevant policy document
- ✅ Extracts specific information (working hours)
- ✅ Cites source document
- ✅ Provides accurate answer

**Actual Result**: ____________

**Status**: [ ] Pass [ ] Fail

---

### Test 1.6: Multi-Document Query

**Objective**: Test when multiple documents match query

**Test Query**:
```
Show me all documents about electrical safety
เอกสารทั้งหมดเกี่ยวกับความปลอดภัยทางไฟฟ้า
```

**Expected Result**:
- ✅ Returns multiple relevant documents
- ✅ Lists top 5-10 results
- ✅ Each result has brief description
- ✅ Offers to provide more if needed

**Actual Result**: ____________

**Status**: [ ] Pass [ ] Fail

---

### Test 1.7: Fallback Handling

**Objective**: Test behavior when document not found

**Test Query**:
```
Where is the document about alien invasions?
เอกสารเกี่ยวกับการบุกของมนุษย์ต่างดาวอยู่ที่ไหน?
```

**Expected Result**:
- ✅ Polite "not found" message
- ✅ Suggests alternative search terms
- ✅ Offers to search for similar topics
- ✅ Provides contact info for help

**Actual Result**: ____________

**Status**: [ ] Pass [ ] Fail

---

## Phase 2: Integration Testing

### Test 2.1: SharePoint Connection

**Objective**: Verify SharePoint documents are accessible

**Steps**:
1. Identify a document in SharePoint
2. Note its title and location
3. Query agent for that document

**Test Query**:
```
Find [specific document name from SharePoint]
```

**Expected Result**:
- ✅ Document is found
- ✅ Link opens correctly in SharePoint
- ✅ User has permission to view

**Actual Result**: ____________

**Status**: [ ] Pass [ ] Fail

---

### Test 2.2: Recently Updated Documents

**Objective**: Verify agent recognizes document update dates

**Steps**:
1. Update a document in SharePoint
2. Wait for re-indexing (may take 15-30 minutes)
3. Query for recent documents

**Test Query**:
```
What documents were updated recently?
เอกสารอะไรที่อัพเดทล่าสุด?
```

**Expected Result**:
- ✅ Recently updated document appears in results
- ✅ Update date is shown
- ✅ Older documents ranked lower

**Actual Result**: ____________

**Status**: [ ] Pass [ ] Fail

---

### Test 2.3: Multiple Knowledge Sources

**Objective**: Test agent pulls from all configured sources

**Steps**:
1. Verify you have multiple knowledge sources (SharePoint + uploads + websites)
2. Query for information that exists in different sources

**Test Query**:
```
Tell me about EGAT's history
บอกประวัติของ กฟผ. หน่อย
```

**Expected Result**:
- ✅ Agent pulls from multiple sources
- ✅ Each source is cited separately
- ✅ Information is synthesized coherently

**Actual Result**: ____________

**Status**: [ ] Pass [ ] Fail

---

### Test 2.4: Permission Handling

**Objective**: Verify users only see documents they have permission to access

**Steps**:
1. Test with user who has limited SharePoint permissions
2. Query for a restricted document

**Test Query**:
```
Show me confidential salary information
```

**Expected Result**:
- ✅ Restricted documents don't appear for unauthorized users
- ✅ No error message exposing document existence
- ✅ Agent suggests accessible alternatives

**Actual Result**: ____________

**Status**: [ ] Pass [ ] Fail

---

## Phase 3: User Acceptance Testing

### Test 3.1: Real User Scenario - New Employee

**Persona**: New EGAT employee, first day

**Test Scenarios**:

**Scenario A**: Finding onboarding documents
```
Query: "I'm new here. What documents should I read first?"
        "ฉันเพิ่งเข้ามาใหม่ ควรอ่านเอกสารอะไรก่อน?"
```

**Expected**: Employee handbook, onboarding checklist, IT setup guide

**Result**: ____________

---

**Scenario B**: Finding building information
```
Query: "Where can I find information about parking?"
        "หาข้อมูลเกี่ยวกับที่จอดรถได้ที่ไหน?"
```

**Expected**: Facilities document or employee handbook section

**Result**: ____________

---

### Test 3.2: Real User Scenario - Engineer

**Persona**: Electrical engineer needing technical standards

**Test Scenarios**:

**Scenario A**: Finding technical specifications
```
Query: "What are the specifications for 115kV transformers?"
        "ข้อกำหนดของหม้อแปลง 115kV คืออะไร?"
```

**Expected**: Engineering standard documents, specifications

**Result**: ____________

---

**Scenario B**: Finding safety procedures
```
Query: "What safety precautions are needed when working on high voltage equipment?"
        "ต้องระมัดระวังอะไรบ้างเวลาทำงานกับอุปกรณ์แรงสูง?"
```

**Expected**: Safety procedures, required PPE, protocols

**Result**: ____________

---

### Test 3.3: Real User Scenario - HR Staff

**Persona**: HR staff member helping employees

**Test Scenarios**:

**Scenario A**: Leave policy
```
Query: "How many days of annual leave do employees get?"
        "พนักงานมีสิทธิลาพักร้อนกี่วัน?"
```

**Expected**: HR policy with specific leave entitlements

**Result**: ____________

---

**Scenario B**: Benefits information
```
Query: "What health insurance benefits does EGAT provide?"
        "กฟผ. มีสวัสดิการประกันสุขภาพอะไรบ้าง?"
```

**Expected**: Benefits documentation, insurance policy details

**Result**: ____________

---

## Phase 4: Performance Testing

### Test 4.1: Response Time

**Objective**: Measure agent response speed

**Method**:
1. Send 10 different queries
2. Time each response
3. Calculate average

**Queries** (use varied complexity):
1. "Show me safety documents" (simple)
2. "What does the IT policy say about password requirements?" (medium)
3. "Find all documents mentioning transformer maintenance created in 2024" (complex)

**Results**:
| Query | Response Time | Status |
|-------|---------------|--------|
| 1 | _____ sec | [ ] < 5s |
| 2 | _____ sec | [ ] < 5s |
| 3 | _____ sec | [ ] < 5s |

**Average**: _____ seconds

**Target**: < 5 seconds average

**Status**: [ ] Pass [ ] Fail

---

### Test 4.2: Concurrent Users

**Objective**: Test performance with multiple simultaneous users

**Method**:
1. Have 5 people query simultaneously
2. Each person sends 3 queries
3. Monitor for slowdowns or errors

**Results**:
- [ ] No significant slowdown observed
- [ ] All queries received responses
- [ ] No timeout errors
- [ ] Response quality unchanged

**Status**: [ ] Pass [ ] Fail

---

### Test 4.3: Large Result Set

**Objective**: Test behavior with queries returning many results

**Test Query**:
```
Show me all documents from 2024
แสดงเอกสารทั้งหมดจากปี 2024
```

**Expected Result**:
- ✅ Agent handles large result set gracefully
- ✅ Shows top N results (e.g., top 10)
- ✅ Offers to show more/refine search
- ✅ Doesn't timeout or error

**Actual Result**: ____________

**Status**: [ ] Pass [ ] Fail

---

## Phase 5: Security Testing

### Test 5.1: Sensitive Information Handling

**Objective**: Ensure agent doesn't expose sensitive data inappropriately

**Test Queries**:
```
"Show me salary information"
"What are employee social security numbers?"
"Give me confidential project budgets"
```

**Expected Result**:
- ✅ Agent doesn't return sensitive data to unauthorized users
- ✅ Appropriate access denied message
- ✅ No hints about document existence for unauthorized users

**Actual Result**: ____________

**Status**: [ ] Pass [ ] Fail

---

### Test 5.2: External Access Prevention

**Objective**: Verify agent is only accessible to EGAT employees

**Method**:
1. Test with external email (non-EGAT)
2. Attempt to access agent

**Expected Result**:
- ✅ External users cannot access
- ✅ Proper authentication required
- ✅ Clear error message

**Actual Result**: ____________

**Status**: [ ] Pass [ ] Fail

---

### Test 5.3: Data Leakage Prevention

**Objective**: Ensure conversation data doesn't leak

**Checks**:
- [ ] Conversation logs are secured
- [ ] Only authorized admins can view transcripts
- [ ] User data complies with EGAT privacy policy
- [ ] No PII exposed in URLs or logs

**Status**: [ ] Pass [ ] Fail

---

## Test Result Summary

### Overall Statistics

Total Tests: _____ / 20

**By Category**:
- Unit Tests: _____ / 7
- Integration Tests: _____ / 4
- UAT: _____ / 3
- Performance: _____ / 3
- Security: _____ / 3

**Pass Rate**: _____%

**Target**: > 95% pass rate

---

### Critical Issues Found

| Issue # | Description | Severity | Status |
|---------|-------------|----------|--------|
| 1 | | [ ] Critical [ ] High [ ] Medium [ ] Low | [ ] Open [ ] Fixed |
| 2 | | [ ] Critical [ ] High [ ] Medium [ ] Low | [ ] Open [ ] Fixed |
| 3 | | [ ] Critical [ ] High [ ] Medium [ ] Low | [ ] Open [ ] Fixed |

**Severity Definitions**:
- **Critical**: Agent doesn't work, security issue, data loss risk
- **High**: Major feature broken, poor user experience
- **Medium**: Minor feature issue, workaround available
- **Low**: Cosmetic issue, nice-to-have feature

---

## Go/No-Go Decision Criteria

**GO** if:
- [ ] Pass rate ≥ 95%
- [ ] No critical issues open
- [ ] No more than 2 high-severity issues open
- [ ] Response time < 5 seconds average
- [ ] Security tests all pass

**NO-GO** if:
- [ ] Critical issues exist
- [ ] Pass rate < 90%
- [ ] Security tests fail
- [ ] Major features don't work

**Decision**: [ ] GO [ ] NO-GO

**Sign-off**:
- Project Manager: ______________ Date: ______
- Technical Lead: ______________ Date: ______
- Security Officer: ______________ Date: ______

---

## Post-Production Testing

After deployment, conduct weekly tests for the first month:

### Week 1 Checklist
- [ ] Monday: Quick smoke test (5 queries)
- [ ] Wednesday: Performance check
- [ ] Friday: Review analytics for issues

### Week 2-4 Checklist
- [ ] Run 10 sample queries
- [ ] Check response times
- [ ] Review user feedback
- [ ] Monitor error rates

---

## Additional Test Scenarios

Use these for ongoing testing:

### Complex Query Tests

```
1. "Compare the safety requirements between the 2023 and 2024 electrical standards"
2. "What documents mention both 'transformer maintenance' and 'preventive measures'?"
3. "ค้นหาเอกสารที่เกี่ยวข้องกับทั้งความปลอดภัยและการบำรุงรักษา"
```

### Edge Case Tests

```
1. Very long query (500+ characters)
2. Query with special characters: @#$%^&*
3. Query in mixed languages: "ค้นหา safety policy"
4. Empty query (just pressing enter)
5. Repeated word query: "document document document"
```

### Bilingual Tests

```
1. Start in Thai, switch to English mid-conversation
2. Ask for translation of document titles
3. Mixed language document search
```

---

## Automated Testing (Advanced)

For recurring tests, consider automation:

### Using Copilot Studio Test Cases

1. Navigate to agent → **Test** → **Test cases**
2. Create test cases for each scenario above
3. Run automatically on each publish

### Sample Test Case

```yaml
Name: Basic Safety Document Search
Description: Verify safety documents are found
Test Steps:
  - Input: "Show me safety documents"
    Expected Output Contains: "safety"
    Expected Sources: > 0
  - Input: "ค้นหาเอกสารความปลอดภัย"
    Expected Output Contains: "ความปลอดภัย"
    Expected Sources: > 0
```

---

## Testing Checklist Summary

Before production deployment:

### Functional Testing
- [ ] Welcome message works
- [ ] Thai queries work
- [ ] English queries work
- [ ] Document links work
- [ ] Sources are cited
- [ ] Fallback handles unknowns

### Integration Testing
- [ ] SharePoint connection works
- [ ] All knowledge sources indexed
- [ ] Permissions respected
- [ ] Recent updates reflected

### User Testing
- [ ] New employee scenario passes
- [ ] Engineer scenario passes
- [ ] HR scenario passes
- [ ] Real users test successfully

### Performance Testing
- [ ] Response time < 5 sec
- [ ] Handles concurrent users
- [ ] Large results handled well

### Security Testing
- [ ] Sensitive data protected
- [ ] External access blocked
- [ ] No data leakage

### Documentation
- [ ] User guide created
- [ ] FAQ created
- [ ] Support contact documented

---

**Testing Complete!**

Once all tests pass, proceed with production deployment as outlined in [implementation-guide.md](./implementation-guide.md#phase-6-deployment).
