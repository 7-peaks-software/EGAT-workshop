# Testing Multi-Turn Conversations

## Introduction

Multi-turn conversations are the hallmark of sophisticated agents. Unlike simple Q&A bots, multi-agent systems need to maintain context across multiple exchanges, transfers, and decision points. In this unit, you'll learn how to thoroughly test complex conversation scenarios.

## What is a Multi-Turn Conversation?

A multi-turn conversation involves:
- Multiple back-and-forth exchanges
- Context maintained throughout
- Different topics/agents involved
- Conditional branching based on user input
- Memory of previous answers

### Example Multi-Turn Flow:

```
Turn 1: User: "I need help"
        Bot: "What can I help you with?"

Turn 2: User: "Equipment issue"
        Bot: "What type of equipment?"

Turn 3: User: "Transformer"
        Bot: "What's the problem?"
        [Transfers to Maintenance Specialist]

Turn 4: User: "It's making loud noises"
        Bot: [Collects more info]

Turn 5: User: "Also seeing sparks"
        Bot: [Escalates to Emergency]
        [Transfers to Emergency Agent]

Turn 6: Emergency: "This is critical..."
```

Each turn builds on the previous context.

## Test Scenario 1: Complete User Journey

### Objective: Test full flow from start to emergency escalation

#### Test Script:

1. **Start conversation** in Tier 1 Agent

2. **User**: "Hello"
   - **Expected**: Welcome message from Tier 1

3. **User**: "I need help with equipment"
   - **Expected**: Tier 1 asks what kind of help

4. **User**: "Something is broken"
   - **Expected**: Transfer to Maintenance Specialist

5. **User**: (In Maintenance) [Answer equipment questions]
   - Location: "Transformer Station 5"
   - Description: "Loud banging noise and smoke"
   - Urgency: "High"

6. **Expected**:
   - Escalation score calculated
   - Transfer to Emergency Response Agent
   - Teams notification sent

7. **User**: (In Emergency) [Provide safety details]

8. **Expected**:
   - Emergency procedures activated
   - High-priority ticket created
   - Emergency team notified

### Validation Checklist:

- [ ] No conversation context lost during transfers
- [ ] User not asked same questions twice
- [ ] Appropriate agents handle each stage
- [ ] All variables passed correctly
- [ ] Tickets/notifications created successfully

## Test Scenario 2: Context Preservation

### Objective: Verify information carries across agents

#### Setup Variables to Track:

Create these variables in your test:
- UserName
- UserEmail
- IssueCategory
- Priority

#### Test Script:

1. **In Tier 1**: Collect user name and email
   - User provides: "John Smith" and "john.smith@egat.co.th"

2. **Transfer to Maintenance Specialist**

3. **Verify**: Maintenance Agent greets user by name
   - Expected: "Hello John! I've received your information..."

4. **In Maintenance**: Collect issue details
   - Issue: Equipment malfunction
   - Priority: High

5. **Transfer to Emergency**

6. **Verify**: Emergency Agent has context
   - Expected: "I see you reported a high-priority equipment malfunction..."
   - Ticket references John's email

### Validation Checklist:

- [ ] Name persists across transfers
- [ ] Email not asked again
- [ ] Previous answers referenced
- [ ] Context variables populated correctly

## Test Scenario 3: Conversation Branching

### Objective: Test different paths through the system

#### Path 1: Simple FAQ (No Transfer)

```
User: "What are your working hours?"
→ Tier 1 answers directly
→ End conversation
```

**Expected**: No transfers, quick answer, done.

#### Path 2: Standard Maintenance (One Transfer)

```
User: "I need routine maintenance"
→ Transfer to Maintenance Specialist
→ Create standard ticket
→ End conversation
```

**Expected**: One transfer, standard priority, Teams notification.

#### Path 3: Emergency Escalation (Two Transfers)

```
User: "Equipment emergency"
→ Transfer to Maintenance Specialist
→ Detects critical keywords
→ Transfer to Emergency Response
→ Emergency protocols
```

**Expected**: Two transfers, emergency procedures, urgent Teams notification.

### Validation Checklist:

- [ ] Path 1: No unnecessary transfers
- [ ] Path 2: Correct single transfer
- [ ] Path 3: Both transfers successful
- [ ] Each path reaches correct conclusion

## Test Scenario 4: Error Handling and Edge Cases

### Test Case 1: User Provides Unclear Input

```
User: "Something is wrong"
Bot: "Can you tell me more about what's wrong?"
User: "I don't know"
```

**Expected**: Agent asks clarifying questions, doesn't get stuck in loop.

### Test Case 2: User Changes Mind Mid-Conversation

```
User: "I need maintenance help"
[Transferred to Maintenance]
User: "Actually, I just have a question about working hours"
```

**Expected**: Agent gracefully handles topic change or transfers back to Tier 1.

### Test Case 3: Transfer Failure

```
[Agent tries to transfer]
[Transfer fails because target agent is unavailable]
```

**Expected**: Fallback message, human escalation option provided.

### Test Case 4: Incomplete Information

```
User: [Doesn't answer required questions]
Bot: [Prompts for information]
User: "I don't want to answer"
```

**Expected**: Agent handles gracefully, offers alternatives (phone support, etc.).

### Validation Checklist:

- [ ] Unclear inputs handled gracefully
- [ ] Topic changes don't break conversation
- [ ] Failed transfers have fallback
- [ ] Required fields validated
- [ ] Alternative support options offered

## Test Scenario 5: Generative AI Responses

### Objective: Test AI-generated responses for complex queries

#### Test Script:

1. **User**: "Can you explain the difference between preventive and corrective maintenance?"

2. **Expected**: Generative response providing clear explanation

3. **User**: "Which one should I choose for a transformer that's 10 years old?"

4. **Expected**: Context-aware response considering:
   - Previous question about maintenance types
   - Specific equipment (transformer)
   - Age factor (10 years)

### Validation Checklist:

- [ ] Responses are accurate and relevant
- [ ] AI maintains conversation context
- [ ] Responses cite knowledge sources if available
- [ ] No hallucinations or incorrect information

## Test Scenario 6: Multi-Language Support

If your agents support multiple languages (Thai/English):

#### Test Script:

1. **User** (in Thai): "สวัสดีครับ" (Hello)

2. **Expected**: Agent responds in Thai

3. **User** (switch to English): "I need help with equipment"

4. **Expected**: Agent switches to English

5. **During Transfer**: Verify language preference maintained

### Validation Checklist:

- [ ] Agent detects language correctly
- [ ] Responses in correct language
- [ ] Language preference persists across transfers
- [ ] Bilingual responses when appropriate

## Creating a Test Plan Document

### Test Matrix Template:

| Scenario | User Input | Expected Agent | Expected Response | Priority | Status |
|----------|-----------|----------------|-------------------|----------|---------|
| FAQ | "Working hours?" | Tier 1 | Direct answer | High | ✅ Pass |
| Standard Maint | "Routine checkup" | Tier 1 → Maint | Transfer, ticket | High | ✅ Pass |
| Emergency | "Fire!" | Any → Emergency | Immediate escalation | High | ✅ Pass |
| Context Test | Name provided | All | Name remembered | Medium | ⏳ Testing |
| Error Handling | Invalid input | Any | Clarifying question | Medium | ❌ Fail |

## Using Copilot Studio Analytics for Testing

### Monitor Test Conversations:

1. In Copilot Studio, go to **Analytics**

2. Navigate to **Sessions**

3. Filter by:
   - Test sessions (your test account)
   - Date range (today)
   - Outcome (escalated, resolved, abandoned)

4. Review:
   - Session duration
   - Number of turns
   - Transfer count
   - Abandonment rate

### Key Metrics to Track:

| Metric | Good Target | Your Result |
|--------|-------------|-------------|
| Avg turns to resolution | < 10 | _____ |
| Transfer success rate | > 95% | _____ |
| Abandonment rate | < 10% | _____ |
| Escalation rate | 5-15% | _____ |

## Automated Testing with Test Cases

### Creating Test Cases in Copilot Studio:

1. Go to your agent

2. Click **Test** → **Test cases**

3. Create a new test case:
   - **Name**: "Emergency Escalation Flow"
   - **User inputs**: [List of messages]
   - **Expected outputs**: [Expected responses]

4. Run the test case

5. Review results

### Example Test Case:

```yaml
Test Case: Emergency Escalation
Inputs:
  - "I need help"
  - "Equipment emergency at Station 5"
  - "There's smoke and sparks"
  - "Critical"
Assertions:
  - Contains: "Emergency Response"
  - Variable: urgencyLevel = "Critical"
  - Transfer: Emergency Response Agent
  - Notification: Teams message sent
```

## Debugging Failed Tests

### Common Issues and Solutions:

#### Issue 1: Transfer Not Occurring

**Debug Steps**:
1. Check trigger phrases match
2. Verify target agent exists and is published
3. Review condition logic
4. Check transfer node configuration

#### Issue 2: Variables Not Passing

**Debug Steps**:
1. Verify variable names match (case-sensitive)
2. Check variable scope (topic vs global)
3. Review variable mapping in transfer node
4. Add debug messages to display variable values

#### Issue 3: Infinite Loops

**Debug Steps**:
1. Add transfer counter
2. Check for circular transfers (A→B→A)
3. Review topic triggers for overlaps
4. Add max conversation turns limit

#### Issue 4: Generative Responses Incorrect

**Debug Steps**:
1. Review knowledge sources
2. Check agent instructions
3. Verify content moderation settings
4. Add specific topics for critical information

## Performance Testing

### Test Under Load:

1. **Concurrent conversations**: Test with multiple users simultaneously

2. **Long conversations**: Test 20+ turn conversations

3. **Rapid inputs**: Test quick successive messages

4. **Large data**: Test with lengthy descriptions

### Expected Performance:

- Response time: < 2 seconds per message
- Transfer time: < 3 seconds
- Flow execution: < 5 seconds
- No timeouts or errors

## User Acceptance Testing (UAT)

### Prepare for UAT:

1. **Select test users**: 3-5 EGAT maintenance staff

2. **Create scenarios**: Real-world situations they encounter

3. **Provide instructions**: How to test and what to look for

4. **Collect feedback**:
   - Was the conversation natural?
   - Did transfers make sense?
   - Any confusing moments?
   - Missing features?

### UAT Feedback Template:

```
Scenario tested: __________
Overall satisfaction: ⭐⭐⭐⭐⭐
What worked well: __________
What was confusing: __________
Suggestions: __________
Would you use this in real work? Yes/No
```

## Key Takeaways

✅ Test complete user journeys end-to-end
✅ Verify context preservation across transfers
✅ Test all conversation branches
✅ Handle edge cases and errors gracefully
✅ Use analytics to identify improvements
✅ Create automated test cases for regression testing
✅ Involve real users in UAT

---

**Previous:** [Integrating with Microsoft Teams](./6-integrating-teams.md)

**Next Unit:** [Check Your Knowledge](./8-check-your-knowledge.md)
