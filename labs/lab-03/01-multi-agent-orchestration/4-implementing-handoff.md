# Implementing Agent Handoff Logic

## Introduction

Now that you have three specialized agents, it's time to connect them together. In this unit, you'll implement the handoff logic that allows agents to transfer conversations to each other seamlessly.

## Understanding Handoff Mechanisms in Copilot Studio

Copilot Studio provides several ways to implement agent handoffs:

### 1. Transfer Conversation Node
The simplest method - explicitly transfer to another agent.

### 2. Redirect Node with Agents
Redirect to a specific topic in another agent.

### 3. Generative Handoff
Let the AI decide when and where to transfer based on context.

For this lab, we'll use the **Transfer Conversation** node for explicit, controlled handoffs.

## Configuring Handoffs from Tier 1 to Maintenance Specialist

### Step 1: Return to Tier 1 Support Agent

1. Navigate to **Agents** and open **EGAT Tier 1 Support**

2. Open the **Maintenance Request Guide** topic (created in the previous unit)

3. Find the section where the user says "Yes, help me now"

### Step 2: Add Transfer Node

1. After the message "I'm transferring you to our Maintenance Specialist...", click the **+** button

2. Select **Topic management** → **Transfer conversation**

3. In the **Transfer conversation** configuration:
   - **Transfer to**: Select **EGAT Maintenance Specialist** (your agent)
   - **Transfer message**: Leave the existing message or customize:
     ```
     I'm connecting you with our Maintenance Specialist who can help you create a detailed request.
     ```

4. **Map variables** (optional but recommended):
   - If you collected user name or email in Tier 1, map them here
   - Example: Map `System.User.DisplayName` to the target agent's variable

5. Click **Save**

### Step 3: Create a General Transfer Topic

Create a catch-all topic that transfers technical questions:

1. In **Tier 1 Support Agent**, create a new topic:
   - **Name**: Technical Issue Transfer
   - **Description**: Transfers technical questions to specialist

2. Add **Trigger phrases**:
   ```
   Equipment is broken
   Something is not working
   Need a technician
   Technical support
   เครื่องเสีย (equipment broken in Thai)
   ต้องการช่างซ่อม (need technician in Thai)
   ```

3. Add a **Message** node:
   ```
   I see you need technical assistance. Let me connect you with our Maintenance Specialist right away.
   ```

4. Add a **Transfer conversation** node:
   - **Transfer to**: EGAT Maintenance Specialist
   - **Message**: "Connecting you to maintenance specialist..."

5. Click **Save**

## Configuring Handoffs from Maintenance to Emergency

### Step 1: Open Maintenance Specialist Agent

1. Navigate to **Agents** and open **EGAT Maintenance Specialist**

2. Open the **Handle Maintenance Request** topic

3. Find the **Condition** node where `urgencyLevel is equal to "Critical - Emergency situation"`

### Step 2: Configure Emergency Transfer

1. In the **True** branch (when it's critical), find the message about escalation

2. After the message, add a **Transfer conversation** node:
   - **Transfer to**: EGAT Emergency Response
   - **Message**: "Escalating to Emergency Response team immediately..."

3. **Map variables** to pass context:
   - Map `issueType` → Emergency agent's variable (if you created one)
   - Map `location` → Emergency agent's location variable
   - Map `issueDescription` → Emergency agent's description variable

4. Click **Save**

### Step 3: Add Emergency Keyword Detection

Create a topic that immediately escalates emergency keywords:

1. In **Maintenance Specialist Agent**, create a new topic:
   - **Name**: Emergency Keyword Detection
   - **Description**: Immediately escalates emergency situations

2. Add **Trigger phrases**:
   ```
   Emergency
   Fire
   Electrical shock
   Someone is injured
   Danger
   ฉุกเฉิน (emergency in Thai)
   อันตราย (danger in Thai)
   ```

3. Add a **Message** node:
   ```
   🚨 I detect this is an emergency situation. Immediately connecting you to our Emergency Response team.
   ```

4. Add a **Transfer conversation** node to **EGAT Emergency Response**

5. Click **Save**

## Implementing Context Preservation

### Creating Context Variables

To maintain conversation context across agents, create global variables:

### In Tier 1 Agent:

1. Go to **Topics** → Select any topic

2. Click **Variables** in the right panel

3. Create these global variables:
   - `Global.UserName` (Text)
   - `Global.UserEmail` (Text)
   - `Global.RequestCategory` (Text)

4. Collect these early in the conversation:

```
Add Question node: "May I have your name?"
Save to: Global.UserName

Add Question node: "What's your email?"
Save to: Global.UserEmail
```

### Passing Context During Transfer:

When you add a **Transfer conversation** node, map your variables:

1. Click on the **Transfer conversation** node

2. Under **Input variables**, map:
   ```
   Source Variable         → Target Variable
   Global.UserName         → Topic.UserName
   Global.UserEmail        → Topic.UserEmail
   Global.RequestCategory  → Topic.Category
   ```

This ensures the receiving agent has context about the user.

## Creating a Transfer Confirmation Topic

Create a topic in receiving agents to handle transfers gracefully:

### In Maintenance Specialist Agent:

1. Create a new topic:
   - **Name**: Transfer Reception
   - **Description**: Greets users transferred from other agents

2. Set trigger: **System** → **On Transfer**

3. Add a **Message** node:
   ```
   Hello {Topic.UserName}! I've received your information from our Tier 1 team.
   I'm ready to help you with your maintenance request.
   ```

4. Continue with your maintenance collection flow

## Testing Handoffs

### Test Scenario 1: Tier 1 to Maintenance

1. Open **Tier 1 Support Agent**

2. Click **Test**

3. Type: "I need to submit a maintenance request"

4. Follow the flow and select "Yes, help me now"

5. **Expected Result**: You should see:
   - Transfer message from Tier 1
   - Greeting from Maintenance Specialist
   - Maintenance questions begin

### Test Scenario 2: Maintenance to Emergency

1. Open **Maintenance Specialist Agent**

2. Click **Test**

3. Go through the maintenance flow

4. When asked for urgency, select: "Critical - Emergency situation"

5. **Expected Result**: You should see:
   - Escalation message from Maintenance
   - Emergency activation message
   - Emergency questions begin

### Test Scenario 3: Direct Emergency Keywords

1. Open **Tier 1 Support Agent** (or Maintenance)

2. Click **Test**

3. Type: "Emergency! There's a fire!"

4. **Expected Result**: Immediate transfer to Emergency Response Agent

## Handling Transfer Failures

What if a transfer fails? Add error handling:

### Adding a Fallback

1. After your **Transfer conversation** node, add a **Condition** node

2. Check if transfer was successful:
   - **Condition**: `System.LastActionResult is equal to "Failed"`

3. If **True**, add fallback:
   ```
   Message: "I'm having trouble connecting to the specialist right now.
   Please call our emergency hotline: 1234
   Or email: emergency@egat.co.th"
   ```

## Advanced: Creating a Transfer History

Track how many transfers have occurred to prevent loops:

### Create a Counter Variable:

1. Create global variable: `Global.TransferCount` (Number)

2. Initialize to 0 at conversation start

3. Before each transfer, add **Set variable** node:
   ```
   Global.TransferCount = Global.TransferCount + 1
   ```

4. Add a condition:
   ```
   IF Global.TransferCount > 2 THEN
       Message: "I'm going to connect you with a human agent instead."
       [Connect to human agent or provide phone number]
   ELSE
       [Proceed with transfer]
   END IF
   ```

## Complete Handoff Flow Diagram

Here's how your complete system should work:

```
User starts conversation
         ↓
    Tier 1 Agent
         ↓
    Recognizes: FAQ? → Answers directly
         ↓
    Recognizes: Maintenance? → Transfer to Maintenance Specialist
         ↓
Maintenance Specialist collects info
         ↓
    Recognizes: Emergency? → Transfer to Emergency Response
         ↓
Emergency Response handles critical situation
```

## Testing the Complete Flow

### End-to-End Test:

1. Open **Tier 1 Support Agent** test pane

2. Simulate this conversation:
   ```
   User: "I need help with some equipment"
   Bot: [Tier 1 response]

   User: "The transformer is making loud noises and sparking"
   Bot: [Transfers to Maintenance Specialist]

   [Maintenance asks questions]

   User: [Selects "Critical - Emergency"]
   Bot: [Transfers to Emergency Response]

   [Emergency handles the situation]
   ```

3. Verify:
   - ✅ Smooth transitions between agents
   - ✅ No repeated questions
   - ✅ Context is maintained
   - ✅ Appropriate agent handles each stage

## Troubleshooting Common Issues

### Issue 1: Transfer Not Working
**Solution**: Ensure target agent exists and is published

### Issue 2: Variables Not Passing
**Solution**: Check variable names match exactly (case-sensitive)

### Issue 3: Infinite Transfer Loop
**Solution**: Implement transfer counter (shown above)

### Issue 4: Abrupt Transitions
**Solution**: Add friendly transfer messages in both source and target agents

## Key Takeaways

✅ Use Transfer Conversation nodes for explicit handoffs
✅ Map variables to preserve context
✅ Create friendly transfer messages
✅ Implement error handling for failed transfers
✅ Test end-to-end flows thoroughly
✅ Prevent transfer loops with counters

---

**Previous:** [Creating Specialized Agents](./3-creating-specialized-agents.md)

**Next Unit:** [Adding Escalation to Maintenance Agent](./5-adding-escalation.md)
