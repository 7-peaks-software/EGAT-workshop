# Adding Escalation to Maintenance Agent

## Introduction

Escalation logic is critical for ensuring the right level of support handles each request. In this unit, you'll enhance your Maintenance Specialist Agent with sophisticated escalation rules based on multiple factors: urgency, complexity, keywords, and business rules.

## Understanding Escalation Triggers

### When to Escalate?

Escalations should occur when:
1. **Urgency level is critical**
2. **Safety keywords are detected** (fire, danger, injury)
3. **Equipment criticality is high** (power stations, transformers)
4. **Multiple failures reported** (systemic issues)
5. **User explicitly requests escalation**

## Multi-Factor Escalation Logic

### Step 1: Create Escalation Scoring System

We'll implement a scoring system where different factors add points, and high scores trigger escalation.

#### In Maintenance Specialist Agent:

1. Navigate to **Topics** and open **Handle Maintenance Request**

2. Create a new variable:
   - Name: `Topic.EscalationScore`
   - Type: Number
   - Default value: 0

3. After collecting `urgencyLevel`, add a **Set variable** node:

```
IF urgencyLevel = "Critical - Emergency situation" THEN
    Topic.EscalationScore = Topic.EscalationScore + 50
ELSE IF urgencyLevel = "High - Urgent, needs immediate attention" THEN
    Topic.EscalationScore = Topic.EscalationScore + 30
ELSE IF urgencyLevel = "Medium - Needs attention within 24 hours" THEN
    Topic.EscalationScore = Topic.EscalationScore + 10
ELSE
    Topic.EscalationScore = Topic.EscalationScore + 0
END IF
```

### Step 2: Add Equipment Criticality Check

1. After collecting `location`, add a **Condition** node to check if it's critical infrastructure:

2. Add a **Condition**:
   ```
   Condition: location contains any of ["TRANS", "Station", "Substation", "Grid"]
   ```

3. If **True**, add **Set variable** node:
   ```
   Topic.EscalationScore = Topic.EscalationScore + 25
   ```

4. Add a **Message** node (in True branch):
   ```
   I notice this involves critical infrastructure. This will receive priority attention.
   ```

### Step 3: Keyword Detection for Safety

1. After collecting `issueDescription`, add a **Condition** node

2. Create a multi-condition check:
   ```
   Condition: issueDescription contains any of ["fire", "smoke", "burning", "shock", "injured", "danger", "ไฟไหม้", "ควัน", "อันตราย"]
   ```

3. If **True**, add **Set variable** node:
   ```
   Topic.EscalationScore = Topic.EscalationScore + 100
   (High score immediately triggers emergency)
   ```

## Implementing Tiered Escalation

### Step 4: Create Escalation Decision Logic

After all information is collected, add escalation decision tree:

1. Add a **Condition** node:
   ```
   Condition: Topic.EscalationScore >= 75
   ```

2. **If True** (Critical Escalation):
   - Add **Message** node:
     ```
     🚨 Based on the severity, I'm immediately escalating this to our Emergency Response team.
     ```
   - Add **Transfer conversation** to **Emergency Response Agent**
   - Map variables:
     ```
     Topic.location → Emergency.Location
     Topic.issueDescription → Emergency.Description
     Topic.EscalationScore → Emergency.Severity
     ```

3. **Else If** `Topic.EscalationScore >= 40`:
   - Add **Message** node:
     ```
     This requires senior technician attention. I'm creating a high-priority ticket.
     ```
   - Add **Power Automate flow** for high-priority ticket
   - Add **Teams notification** (we'll create in next unit)

4. **Else**:
   - Add **Message** node:
     ```
     I'm creating a standard maintenance ticket for your request.
     ```
   - Add **Power Automate flow** for standard ticket

## Creating the Power Automate Flow for Ticket Creation

### Step 1: Create the Flow

1. In **Maintenance Specialist Agent**, in the topic where you want to create tickets, click **+** → **Call an action** → **Create a flow**

2. This opens Power Automate in a new tab

3. You'll see a flow template with:
   - **Trigger**: When Copilot Studio calls the flow
   - **Response**: Return value(s) to Copilot Studio

### Step 2: Add Input Parameters

1. Click on the **Copilot Studio** trigger

2. Click **+ Add an input**

3. Add these inputs:
   - **Text** input: `UserName`
   - **Text** input: `UserEmail`
   - **Text** input: `IssueType`
   - **Text** input: `Location`
   - **Text** input: `Description`
   - **Text** input: `UrgencyLevel`
   - **Number** input: `EscalationScore`

### Step 3: Add Ticket Creation Action

For this example, we'll use Dataverse to create a ticket, but you can use any ticketing system (ServiceNow, Jira, etc.)

#### Using Dataverse:

1. Click **+ New step**

2. Search for "Dataverse"

3. Select **Add a new row**

4. Configure:
   - **Table name**: Select your tickets table (or create one)
   - Map fields:
     ```
     Title: IssueType + " at " + Location
     Description: Description
     Priority: UrgencyLevel
     Requested By: UserEmail
     Status: "New"
     Escalation Score: EscalationScore
     Created Date: utcNow()
     ```

#### Using SharePoint (Alternative):

1. Click **+ New step**

2. Search for "SharePoint"

3. Select **Create item**

4. Configure:
   - **Site Address**: Your SharePoint site
   - **List Name**: Maintenance Requests
   - Map fields similarly

### Step 4: Get Ticket Number

1. After creating the ticket, add **Compose** action

2. In the Compose action:
   ```
   Ticket Number: @{outputs('Add_a_new_row')?['body/cr123_ticketnumber']}
   ```

### Step 5: Return Value to Copilot

1. Click on **Return value(s) to Copilot Studio**

2. Click **+ Add an output**

3. Add:
   - **Text** output: `TicketNumber`
   - **Value**: Use the ticket number from previous step

4. **Save** the flow with a name like "Create EGAT Maintenance Ticket"

### Step 6: Connect Flow to Agent

1. Return to your **Maintenance Specialist Agent**

2. In the topic, click **+** → **Call an action** → select your flow

3. Map the inputs:
   ```
   UserName → Topic.UserName (or System.User.DisplayName)
   UserEmail → Topic.UserEmail
   IssueType → Topic.IssueType
   Location → Topic.Location
   Description → Topic.IssueDescription
   UrgencyLevel → Topic.UrgencyLevel
   EscalationScore → Topic.EscalationScore
   ```

4. Save the flow output to a variable:
   - Variable: `Topic.TicketNumber`

5. Add a **Message** node after the flow:
   ```
   ✅ Your maintenance ticket has been created successfully!

   Ticket Number: {Topic.TicketNumber}
   Priority: {Topic.UrgencyLevel}
   Estimated Response Time: [Based on priority]

   You'll receive updates via email at {Topic.UserEmail}
   ```

## Adding Human Escalation Option

### Allow Users to Request Escalation:

1. After creating the ticket, add a **Question** node:
   ```
   Question: "Would you like to speak with a human agent about this?"
   Options:
   - Yes, connect me to an agent
   - No, this is sufficient
   ```

2. Add a **Condition** based on the answer

3. If "Yes":
   - Add **Message** node:
     ```
     I'm connecting you with a human maintenance coordinator who can assist further.
     ```
   - Add **Transfer to agent** node (human agent handoff)
   - Alternative: Provide phone number:
     ```
     Please call our maintenance hotline: 1234
     Your ticket number is: {Topic.TicketNumber}
     ```

## Implementing Auto-Escalation for Multiple Reports

### Tracking Similar Issues:

If multiple users report the same issue, auto-escalate:

1. In your Power Automate flow, after creating the ticket, add an action

2. **Search for "Dataverse" → List rows**

3. Configure:
   - **Table**: Maintenance Tickets
   - **Filter rows**:
     ```
     Location eq '{Location}' and
     statecode eq 0 and
     createdon gt {date 24 hours ago}
     ```

4. Add a **Condition**:
   ```
   Condition: length(outputs('List_rows')?['body/value']) > 3
   (More than 3 tickets for same location in 24 hours)
   ```

5. If **True**:
   - Send high-priority notification
   - Flag as "Systemic Issue"
   - Alert management team

## Creating Escalation Notifications

### Step 1: Add Email Notification

In your Power Automate flow:

1. Add **Send an email (V2)** action

2. Configure:
   - **To**: maintenance-team@egat.co.th
   - **Subject**:
     ```
     [PRIORITY: {UrgencyLevel}] New Maintenance Ticket {TicketNumber}
     ```
   - **Body**:
     ```html
     <h2>New Maintenance Request</h2>
     <p><strong>Ticket:</strong> {TicketNumber}</p>
     <p><strong>Priority:</strong> {UrgencyLevel}</p>
     <p><strong>Escalation Score:</strong> {EscalationScore}</p>
     <p><strong>Location:</strong> {Location}</p>
     <p><strong>Issue:</strong> {Description}</p>
     <p><strong>Requested by:</strong> {UserName} ({UserEmail})</p>
     ```

3. Add a **Condition** to send different emails based on priority:
   ```
   IF EscalationScore >= 75 THEN
       To: emergency-team@egat.co.th
       Subject: 🚨 URGENT: Emergency Escalation...
   ELSE IF EscalationScore >= 40 THEN
       To: senior-technicians@egat.co.th
       Subject: High Priority Maintenance...
   ELSE
       To: maintenance-team@egat.co.th
       Subject: New Maintenance Request...
   END IF
   ```

## Testing Escalation Logic

### Test Case 1: Low Priority

1. Open **Maintenance Specialist Agent**
2. Test with:
   - Issue Type: Routine maintenance
   - Location: Office 123
   - Urgency: Low
3. **Expected**: Standard ticket, no escalation

### Test Case 2: High Priority Location

1. Test with:
   - Issue Type: Equipment malfunction
   - Location: Transformer Station 5
   - Urgency: Medium
2. **Expected**: Higher escalation score, priority ticket

### Test Case 3: Safety Keywords

1. Test with:
   - Description: "There's smoke coming from the panel"
   - Urgency: Any
2. **Expected**: Immediate escalation to Emergency Response

### Test Case 4: Critical Urgency

1. Test with:
   - Urgency: Critical
2. **Expected**: Immediate transfer to Emergency Response Agent

## Monitoring Escalations

### Create Escalation Dashboard (Optional):

Use Power BI or Dataverse views to track:
- Escalation rate over time
- Most common escalation triggers
- Average escalation score
- Response times by priority level

## Key Takeaways

✅ Use scoring systems for multi-factor escalation decisions
✅ Detect safety keywords for automatic escalation
✅ Create different ticket types based on severity
✅ Provide human escalation options
✅ Send appropriate notifications based on priority
✅ Test all escalation paths thoroughly

---

**Previous:** [Implementing Agent Handoff Logic](./4-implementing-handoff.md)

**Next Unit:** [Integrating with Microsoft Teams](./6-integrating-teams.md)
