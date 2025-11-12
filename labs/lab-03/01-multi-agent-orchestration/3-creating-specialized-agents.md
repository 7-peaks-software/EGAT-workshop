# Creating Specialized Agents

## Introduction

Now that you understand multi-agent architecture, it's time to build the specialized agents for EGAT's maintenance system. In this unit, you'll create three agents, each with a specific purpose.

## Agent 1: Tier 1 Support Agent (FAQ Agent)

### Purpose
Handle common questions and route complex requests to specialists.

### Step 1: Create the Agent

1. Navigate to [Microsoft Copilot Studio](https://copilotstudio.microsoft.com/)

2. Select **Agents** from the left navigation

3. Click **+ New agent**

4. Configure the agent:
   - **Name**: EGAT Tier 1 Support
   - **Description**: Handles FAQs and routes complex issues to specialists
   - **Language**: Thai and English (or your preferred language)

5. Click **Create**

### Step 2: Configure Agent Settings

1. In your newly created agent, click the **Settings** icon (gear icon)

2. Navigate to **Generative AI** settings

3. Configure:
   - **How should your agent decide how to respond?**: Select **Generative**
   - **How strict should the content moderation be?**: Select **Medium**

4. Click **Save**

### Step 3: Create FAQ Topics

Create topics for common questions:

#### Topic 1: Working Hours

1. Click **Topics** in the left navigation

2. Click **+ Add** → **Topic** → **From blank**

3. Configure the topic:
   - **Name**: Working Hours Inquiry
   - **Description**: Provides EGAT working hours information

4. Add **Trigger phrases**:
   ```
   What are EGAT's working hours?
   เวลาทำงานของ กฟผ. คือเท่าไหร่
   What time does EGAT open?
   Office hours
   ```

5. In the authoring canvas, add a **Message** node:
   ```
   EGAT's working hours are:
   Monday - Friday: 8:00 AM - 5:00 PM
   Saturday - Sunday: Closed

   For emergency situations, our 24/7 hotline is available at: 1234
   ```

6. Click **Save**

#### Topic 2: How to Submit Maintenance Request

1. Create another topic:
   - **Name**: Maintenance Request Guide
   - **Description**: Explains how to submit maintenance requests

2. Add **Trigger phrases**:
   ```
   How do I submit a maintenance request?
   จะส่งคำขอการบำรุงรักษาได้อย่างไร
   Request maintenance
   Submit a repair request
   ```

3. Add a **Message** node:
   ```
   To submit a maintenance request, you can:

   1. Use this chat - I can help you create a request right now
   2. Call our maintenance hotline: 1234
   3. Email: maintenance@egat.co.th

   Would you like me to help you submit a request now?
   ```

4. Add a **Question** node:
   - **Question**: (Use the message above)
   - **Identify**: Multiple choice options
   - **Options**:
     - Yes, help me now
     - No, thanks

5. Add a **Condition** node based on the answer

6. If "Yes", add a **Message** node:
   ```
   I'm transferring you to our Maintenance Specialist who can help you with your request.
   ```

7. Add a **Transfer conversation** node (we'll configure this after creating the target agent)

8. Click **Save**

## Agent 2: Maintenance Specialist Agent

### Purpose
Handle technical maintenance requests, collect details, and create tickets.

### Step 1: Create the Agent

1. Return to the **Agents** page

2. Click **+ New agent**

3. Configure:
   - **Name**: EGAT Maintenance Specialist
   - **Description**: Handles technical maintenance requests and creates service tickets
   - **Language**: Thai and English

4. Click **Create**

### Step 2: Create Maintenance Request Topic

1. In the Maintenance Specialist agent, click **Topics** → **+ Add** → **Topic** → **From blank**

2. Configure:
   - **Name**: Handle Maintenance Request
   - **Description**: Collects information and creates maintenance ticket

3. This topic will be triggered by transfer, so we'll add a **greeting message**:

4. Add a **Message** node:
   ```
   Hello! I'm the EGAT Maintenance Specialist. I'll help you with your maintenance request.
   ```

5. Add a **Question** node to collect the issue type:
   - **Enter a message**: What type of issue are you experiencing?
   - **Identify**: Multiple choice options
   - **Options**:
     - Equipment malfunction
     - Routine maintenance
     - Safety concern
     - Other

6. **Save** the response to a variable: `issueType`

7. Add a **Question** node to collect location:
   - **Enter a message**: What is the location or equipment ID?
   - **Identify**: User's entire response

8. Save to variable: `location`

9. Add a **Question** node for description:
   - **Enter a message**: Please describe the issue in detail
   - **Identify**: User's entire response

10. Save to variable: `issueDescription`

11. Add a **Question** node for urgency:
    - **Enter a message**: How urgent is this issue?
    - **Identify**: Multiple choice options
    - **Options**:
      - Low - Can wait a few days
      - Medium - Needs attention within 24 hours
      - High - Urgent, needs immediate attention
      - Critical - Emergency situation

12. Save to variable: `urgencyLevel`

### Step 3: Add Escalation Logic

1. Add a **Condition** node:
   - **Condition**: `urgencyLevel is equal to "Critical - Emergency situation"`

2. If **True**:
   - Add a **Message** node:
     ```
     This is a critical situation. I'm immediately escalating to our Emergency Response team.
     ```
   - Add a **Transfer conversation** node to Emergency Agent (we'll configure after creating it)

3. If **False**:
   - Continue with ticket creation
   - Add a **Message** node:
     ```
     Thank you for providing the information. I'm creating a maintenance ticket for you.
     ```
   - Add a **Power Automate flow** node (we'll create this flow in a later unit)

4. Click **Save**

## Agent 3: Emergency Response Agent

### Purpose
Handle critical/emergency situations with highest priority.

### Step 1: Create the Agent

1. Return to **Agents** page

2. Click **+ New agent**

3. Configure:
   - **Name**: EGAT Emergency Response
   - **Description**: Handles emergency and critical situations with immediate response
   - **Language**: Thai and English

4. Click **Create**

### Step 2: Configure High-Priority Behavior

1. Click **Settings** → **Generative AI**

2. In the **Instructions** field, add:
   ```
   You are an emergency response specialist for EGAT. Your priorities are:
   1. Ensure safety first
   2. Gather critical information quickly
   3. Dispatch emergency teams immediately
   4. Keep the user informed

   Always maintain a calm, professional tone while acting with urgency.
   ```

3. Click **Save**

### Step 3: Create Emergency Handling Topic

1. Click **Topics** → **+ Add** → **Topic** → **From blank**

2. Configure:
   - **Name**: Emergency Situation Handler
   - **Description**: Manages critical emergency situations

3. Add a **Message** node:
   ```
   🚨 EMERGENCY RESPONSE ACTIVATED 🚨

   I'm here to help. Your safety is our top priority.
   ```

4. Add a **Question** node:
   - **Message**: What is the nature of the emergency?
   - **Identify**: User's entire response

5. Save to variable: `emergencyDescription`

6. Add a **Question** node:
   - **Message**: What is your exact location?
   - **Identify**: User's entire response

7. Save to variable: `emergencyLocation`

8. Add a **Question** node:
   - **Message**: Are there any immediate safety risks? (injuries, fire, exposed electrical hazards)
   - **Identify**: User's entire response

9. Save to variable: `safetyRisks`

10. Add a **Message** node:
    ```
    Emergency team has been notified and is being dispatched to your location.
    Estimated arrival: 15 minutes

    Emergency hotline: 1234 (call if situation changes)

    Please stay safe and follow these safety protocols:
    - Stay away from electrical hazards
    - Do not attempt repairs yourself
    - Evacuate if instructed
    ```

11. Add a **Power Automate flow** to send Teams notification (we'll create this later)

12. Click **Save**

## Testing Your Agents

### Test Each Agent Individually

1. For **Tier 1 Support Agent**:
   - Click **Test** in the top-right corner
   - Try: "What are EGAT's working hours?"
   - Try: "How do I submit a maintenance request?"

2. For **Maintenance Specialist Agent**:
   - Test the data collection flow
   - Ensure all questions appear correctly
   - Verify variables are being saved

3. For **Emergency Response Agent**:
   - Test the emergency flow
   - Check that the urgent tone is appropriate

> **Note**: Agent handoffs won't work yet - we'll configure those in the next unit!

## Review What You've Built

You now have three specialized agents:

✅ **Tier 1 Support**: Handles FAQs and basic routing
✅ **Maintenance Specialist**: Collects detailed information
✅ **Emergency Response**: Manages critical situations

Each agent has a clear purpose and specialized topics. In the next unit, you'll connect them together with handoff logic.

---

**Previous:** [Understanding Multi-Agent Orchestration](./2-understanding-multi-agent.md)

**Next Unit:** [Implementing Agent Handoff Logic](./4-implementing-handoff.md)
