# Understanding Multi-Agent Orchestration

## Introduction

Before building multi-agent systems, it's important to understand how agents communicate, when to use multiple agents, and the architectural patterns that make orchestration successful.

## Multi-Agent Architecture Patterns

### 1. Hub-and-Spoke Pattern

In this pattern, a central "router" agent receives all user requests and delegates to specialized agents.

```
User → Router Agent → [Specialist A, Specialist B, Specialist C]
```

**Use when:**
- You have many specialized agents
- Clear categorization of requests
- Need centralized control

**Example:**
```
User: "I need help with equipment maintenance"
Router: [Analyzes intent] → Transfers to Maintenance Agent
```

### 2. Escalation Pattern

Agents are arranged in tiers, with increasing levels of expertise. Issues escalate up the chain as needed.

```
Tier 1 (Basic) → Tier 2 (Advanced) → Tier 3 (Expert)
```

**Use when:**
- Different complexity levels
- Need to filter simple requests
- Cost optimization (basic agents handle most requests)

**Example:**
```
Tier 1: Handles FAQs
↓ (if complex)
Tier 2: Handles technical issues
↓ (if critical)
Tier 3: Handles emergencies
```

### 3. Peer-to-Peer Pattern

Specialized agents work as equals, handing off to each other as needed.

```
Agent A ↔ Agent B ↔ Agent C
```

**Use when:**
- No clear hierarchy
- Agents have equal authority
- Collaborative problem-solving

## How Agent Handoff Works

### The Handoff Process

1. **Detection**: Current agent recognizes it cannot handle the request
2. **Preparation**: Agent collects necessary context
3. **Transfer**: Conversation moves to the target agent
4. **Context Sharing**: Relevant information is passed along
5. **Continuation**: New agent continues the conversation seamlessly

### What Gets Transferred?

During handoff, the following can be shared:
- **Conversation history**: What's been discussed
- **Variables**: Collected information (name, email, issue type)
- **Entities**: Recognized data (dates, locations, IDs)
- **Intent**: What the user is trying to accomplish

## Configuring Agent Handoff in Copilot Studio

### Method 1: Transfer to Agent Topic

Use the built-in "Transfer to Agent" node:

1. Create a topic in your source agent
2. Add a "Transfer conversation" node
3. Select the target agent
4. Map variables to transfer

### Method 2: Conditional Transfer

Use conditions to determine which agent to transfer to:

```
IF urgency = "emergency" THEN
    Transfer to Emergency Agent
ELSE IF category = "technical" THEN
    Transfer to Technical Agent
ELSE
    Continue with current agent
END IF
```

### Method 3: Generative Handoff

Let the AI decide when to hand off based on conversation context:

```
System prompt: "If the user's request involves emergency situations,
suggest transferring to the Emergency Response Agent."
```

## Context Preservation Strategies

### Variables to Always Transfer

When handing off, always include:
- **User identification**: Name, email, employee ID
- **Request summary**: What they're asking for
- **Urgency level**: How critical is this?
- **Conversation stage**: How far along are they?

### Example Context Transfer

```json
{
  "userName": "Somchai Prasert",
  "userEmail": "somchai.p@egat.co.th",
  "issueType": "Equipment malfunction",
  "urgencyLevel": "High",
  "equipmentId": "TRANS-005",
  "conversationHistory": "User reported strange noise from transformer"
}
```

## Best Practices for Multi-Agent Systems

### 1. Clear Agent Boundaries

Each agent should have a well-defined purpose:
- ✅ "Maintenance Request Agent" - Clear purpose
- ❌ "General Agent" - Too broad

### 2. Smooth Handoffs

Make transfers feel natural:
- ✅ "I'm going to connect you with our maintenance specialist who can better assist with this technical issue."
- ❌ "Transferring..." (abrupt, no context)

### 3. Avoid Handoff Loops

Prevent agents from passing users back and forth:
- Set maximum transfer count
- Track transfer history
- Have a fallback human escalation

### 4. Test Transfer Scenarios

Always test:
- Happy path (successful transfer)
- Edge cases (transfer failures)
- Multiple transfers (A→B→C)
- Return transfers (A→B→A)

### 5. Monitor and Optimize

Track metrics:
- Transfer rate (% of conversations transferred)
- Transfer success rate
- User satisfaction after transfer
- Average transfers per conversation

## Common Pitfalls to Avoid

### ❌ Pitfall 1: Too Many Agents
**Problem**: 20 agents, each doing one tiny thing
**Solution**: Start with 2-3 agents, expand as needed

### ❌ Pitfall 2: No Fallback Plan
**Problem**: What if all agents fail?
**Solution**: Always have human escalation option

### ❌ Pitfall 3: Lost Context
**Problem**: User has to repeat information after transfer
**Solution**: Pass comprehensive context with each transfer

### ❌ Pitfall 4: Unclear Triggers
**Problem**: Users don't know which agent they're talking to
**Solution**: Agents clearly introduce themselves

## Planning Your Multi-Agent System

### Step 1: Identify Distinct Domains

For EGAT maintenance:
- **Domain 1**: General information and FAQs
- **Domain 2**: Standard maintenance requests
- **Domain 3**: Emergency response

### Step 2: Map User Journeys

```
User Journey 1: Simple FAQ
User → Tier 1 Agent → Answer → Done

User Journey 2: Maintenance Request
User → Tier 1 Agent → Maintenance Agent → Create Ticket → Done

User Journey 3: Emergency
User → Any Agent → Emergency Agent → Immediate Response
```

### Step 3: Define Transfer Triggers

| Trigger | Source Agent | Target Agent | Condition |
|---------|--------------|--------------|-----------|
| Technical keywords | Tier 1 | Maintenance | Contains("repair", "broken", "malfunction") |
| Urgency words | Any | Emergency | Contains("urgent", "emergency", "danger") |
| Complex question | Tier 1 | Specialist | UserConfusion > threshold |

### Step 4: Design Context Flow

What information flows between agents?
- User identity → Always
- Issue summary → Always
- Priority level → Always
- Equipment details → If available
- Location → If relevant

## Exercise: Design Your System

Take 5 minutes to sketch your multi-agent architecture:

1. **List your agents** (2-4 recommended for this lab)
   - Agent 1: _________________
   - Agent 2: _________________
   - Agent 3: _________________

2. **Define their responsibilities**
   - Agent 1 handles: _________________
   - Agent 2 handles: _________________
   - Agent 3 handles: _________________

3. **Map handoff triggers**
   - From Agent 1 to Agent 2 when: _________________
   - From Agent 2 to Agent 3 when: _________________

Keep this design handy as we build in the next units!

## Key Takeaways

✅ Multi-agent systems divide complexity among specialized agents
✅ Handoffs require careful planning and context sharing
✅ Start simple (2-3 agents) and expand as needed
✅ Always have a human escalation fallback
✅ Test transfer scenarios thoroughly

---

**Previous:** [Introduction](./1-introduction.md)

**Next Unit:** [Creating Specialized Agents](./3-creating-specialized-agents.md)
