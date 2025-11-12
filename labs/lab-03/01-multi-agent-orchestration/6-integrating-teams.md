# Integrating with Microsoft Teams

## Introduction

Microsoft Teams integration enables real-time notifications and collaboration for your maintenance team. In this unit, you'll add Teams notifications that alert the appropriate teams when tickets are created, escalated, or require immediate attention.

## Types of Teams Integration

### 1. Adaptive Cards to Channels
Send rich, interactive cards to specific Teams channels

### 2. Direct Messages to Users
Notify specific team members directly

### 3. Agent in Teams
Deploy your agent directly in Teams for user conversations

For this lab, we'll focus on **sending adaptive cards to channels** for maintenance notifications.

## Prerequisites

Before you begin:
- [ ] Microsoft Teams access
- [ ] Permission to post to Teams channels
- [ ] Teams channel(s) created for notifications (e.g., "Maintenance Alerts")
- [ ] Webhook URL or Teams connector configured

## Method 1: Using Power Automate Teams Connector

### Step 1: Add Teams Action to Your Flow

1. Open the Power Automate flow you created in the previous unit ("Create EGAT Maintenance Ticket")

2. After the ticket creation step, click **+ New step**

3. Search for "Microsoft Teams"

4. Select **Post adaptive card in a chat or channel**

### Step 2: Configure Teams Connection

1. **Post as**: Flow bot
2. **Post in**: Channel
3. **Team**: Select your EGAT team
4. **Channel**: Select your maintenance channel (e.g., "Maintenance Alerts")

### Step 3: Design the Adaptive Card

Click on the **Adaptive Card** field and paste this JSON:

```json
{
  "type": "AdaptiveCard",
  "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
  "version": "1.4",
  "body": [
    {
      "type": "Container",
      "style": "emphasis",
      "items": [
        {
          "type": "ColumnSet",
          "columns": [
            {
              "type": "Column",
              "width": "auto",
              "items": [
                {
                  "type": "Image",
                  "url": "https://img.icons8.com/color/96/000000/maintenance.png",
                  "size": "Small"
                }
              ]
            },
            {
              "type": "Column",
              "width": "stretch",
              "items": [
                {
                  "type": "TextBlock",
                  "text": "New Maintenance Request",
                  "weight": "Bolder",
                  "size": "Large"
                },
                {
                  "type": "TextBlock",
                  "text": "Ticket #@{outputs('Add_a_new_row')?['body/cr123_ticketnumber']}",
                  "spacing": "None",
                  "isSubtle": true
                }
              ]
            }
          ]
        }
      ]
    },
    {
      "type": "FactSet",
      "facts": [
        {
          "title": "Priority:",
          "value": "@{triggerBody()?['text_1']}"
        },
        {
          "title": "Location:",
          "value": "@{triggerBody()?['text_2']}"
        },
        {
          "title": "Issue Type:",
          "value": "@{triggerBody()?['text']}"
        },
        {
          "title": "Requested By:",
          "value": "@{triggerBody()?['text_3']} (@{triggerBody()?['text_4']})"
        },
        {
          "title": "Created:",
          "value": "@{utcNow()}"
        }
      ]
    },
    {
      "type": "TextBlock",
      "text": "**Description:**",
      "weight": "Bolder",
      "separator": true
    },
    {
      "type": "TextBlock",
      "text": "@{triggerBody()?['text_5']}",
      "wrap": true
    },
    {
      "type": "ActionSet",
      "actions": [
        {
          "type": "Action.OpenUrl",
          "title": "View Ticket",
          "url": "https://your-ticketing-system.com/ticket/@{outputs('Add_a_new_row')?['body/cr123_ticketnumber']}"
        },
        {
          "type": "Action.OpenUrl",
          "title": "Assign to Me",
          "url": "https://your-ticketing-system.com/assign/@{outputs('Add_a_new_row')?['body/cr123_ticketnumber']}"
        }
      ]
    }
  ]
}
```

### Step 4: Replace Dynamic Content

In the JSON above, replace the placeholder values with actual Power Automate dynamic content:

1. Click in a value field (e.g., where it says `@{triggerBody()?['text_1']}`)
2. Delete the placeholder
3. Click **Add dynamic content**
4. Select the appropriate field:
   - `text` → IssueType
   - `text_1` → UrgencyLevel
   - `text_2` → Location
   - `text_3` → UserName
   - `text_4` → UserEmail
   - `text_5` → Description

> **Tip**: It's easier to build the card in the [Adaptive Cards Designer](https://adaptivecards.io/designer/), then copy the JSON into Power Automate.

### Step 5: Add Priority-Based Styling

Add a condition before the Teams action to use different cards for different priorities:

1. Before the **Post adaptive card** action, add a **Condition**

2. **Condition**: `EscalationScore is greater than or equal to 75`

3. **If True** (High Priority):
   - Use a red-themed adaptive card
   - Change title to "🚨 URGENT: High Priority Request"
   - Post to "#emergency-maintenance" channel

4. **If False** (Normal Priority):
   - Use standard blue-themed card
   - Post to "#maintenance-alerts" channel

## Method 2: Using Incoming Webhook (Alternative)

### Step 1: Create Incoming Webhook in Teams

1. Open Microsoft Teams

2. Navigate to your maintenance channel

3. Click the **•••** (More options) next to the channel name

4. Select **Connectors** (or **Workflows** in new Teams)

5. Search for "Incoming Webhook"

6. Click **Configure** next to "Incoming Webhook"

7. Provide a name: "EGAT Maintenance Notifications"

8. Optionally upload an image/icon

9. Click **Create**

10. **Copy the webhook URL** - you'll need this!

### Step 2: Use Webhook in Power Automate

1. In your Power Automate flow, add **HTTP** action

2. Configure:
   - **Method**: POST
   - **URI**: Paste your webhook URL
   - **Headers**:
     ```
     Content-Type: application/json
     ```
   - **Body**:
     ```json
     {
       "@type": "MessageCard",
       "@context": "https://schema.org/extensions",
       "summary": "New Maintenance Request",
       "themeColor": "0078D7",
       "title": "New Maintenance Ticket",
       "sections": [
         {
           "activityTitle": "Ticket #@{outputs('Add_a_new_row')?['body/cr123_ticketnumber']}",
           "facts": [
             {
               "name": "Priority:",
               "value": "@{triggerBody()?['text_1']}"
             },
             {
               "name": "Location:",
               "value": "@{triggerBody()?['text_2']}"
             },
             {
               "name": "Requested By:",
               "value": "@{triggerBody()?['text_3']}"
             }
           ],
           "text": "@{triggerBody()?['text_5']}"
         }
       ],
       "potentialAction": [
         {
           "@type": "OpenUri",
           "name": "View Ticket",
           "targets": [
             {
               "os": "default",
               "uri": "https://your-system.com/ticket/@{outputs('Add_a_new_row')?['body/cr123_ticketnumber']}"
             }
           ]
         }
       ]
     }
     ```

## Advanced: Mentioning Users in Teams

### Step 1: Get User Information

To mention specific users (e.g., on-call technician):

1. In Power Automate, add **Get user profile (V2)** action

2. **User (UPN)**: oncall-tech@egat.co.th (or from a variable)

### Step 2: Modify Adaptive Card

Add a mention in your adaptive card body:

```json
{
  "type": "TextBlock",
  "text": "<at>@{outputs('Get_user_profile')?['body/displayName']}</at>, please review this urgent request",
  "wrap": true
}
```

And add to msteams section:

```json
"msteams": {
  "entities": [
    {
      "type": "mention",
      "text": "<at>@{outputs('Get_user_profile')?['body/displayName']}</at>",
      "mentioned": {
        "id": "@{outputs('Get_user_profile')?['body/id']}",
        "name": "@{outputs('Get_user_profile')?['body/displayName']}"
      }
    }
  ]
}
```

## Implementing Smart Routing to Teams

### Route to Different Channels Based on Priority:

1. Create a **Switch** control in Power Automate

2. **On**: `UrgencyLevel`

3. **Cases**:

   **Case 1**: "Critical - Emergency situation"
   - Channel: #emergency-response
   - Mention: @emergency-team
   - Theme Color: Red (#FF0000)

   **Case 2**: "High - Urgent, needs immediate attention"
   - Channel: #urgent-maintenance
   - Mention: @senior-technicians
   - Theme Color: Orange (#FF8C00)

   **Case 3**: "Medium" or "Low"
   - Channel: #maintenance-alerts
   - No mention
   - Theme Color: Blue (#0078D7)

## Adding Interactive Buttons to Teams Cards

### Quick Actions in Teams:

Enhance your adaptive card with action buttons:

```json
"actions": [
  {
    "type": "Action.OpenUrl",
    "title": "View Full Details",
    "url": "https://your-portal.egat.co.th/tickets/@{TicketNumber}"
  },
  {
    "type": "Action.OpenUrl",
    "title": "Assign to Me",
    "url": "https://your-portal.egat.co.th/assign/@{TicketNumber}"
  },
  {
    "type": "Action.OpenUrl",
    "title": "Start Chat with User",
    "url": "https://teams.microsoft.com/l/chat/0/0?users=@{UserEmail}"
  },
  {
    "type": "Action.OpenUrl",
    "title": "Call Emergency Hotline",
    "url": "tel:+6621234567"
  }
]
```

## Deploying Your Agent to Teams

### Making Your Agent Available in Teams:

1. In Copilot Studio, open your agent

2. Click **Channels** in the left navigation

3. Click **Microsoft Teams**

4. Click **Turn on Teams** (if not already enabled)

5. Choose deployment options:
   - **Show to everyone in my org**: Makes it available to all EGAT employees
   - **Submit for admin approval**: Requires IT approval first

6. **Availability options**:
   - [ ] Show in Teams search
   - [ ] Show in Teams app store
   - [ ] Pin for users

7. Click **Submit**

### Creating a Teams App Package:

1. After enabling Teams channel, click **Download manifest**

2. This downloads a .zip file containing:
   - manifest.json
   - Icons (color and outline)

3. Share this with your IT admin or upload to Teams:
   - Teams → Apps → Upload a custom app
   - Select the .zip file
   - Install to a team or use personally

## Testing Teams Integration

### Test Scenario 1: Standard Notification

1. Open your **Maintenance Specialist Agent**

2. Go through the maintenance request flow

3. Select normal priority

4. Check your Teams channel for the notification card

5. **Verify**:
   - ✅ Card appears in correct channel
   - ✅ All information is displayed correctly
   - ✅ Action buttons work

### Test Scenario 2: Emergency Notification

1. Create a critical urgency request

2. Check the emergency Teams channel

3. **Verify**:
   - ✅ Posted to emergency channel
   - ✅ Red theme/urgent styling
   - ✅ Appropriate team members mentioned

### Test Scenario 3: Agent in Teams

1. Open Microsoft Teams

2. Search for your agent (e.g., "EGAT Maintenance")

3. Start a conversation

4. **Verify**:
   - ✅ Agent responds correctly
   - ✅ Can complete full maintenance flow
   - ✅ Notifications still work

## Monitoring Teams Notifications

### Create a Notification Log:

1. In Power Automate, after sending Teams notification, add **Add a new row** (Dataverse)

2. Log:
   - Notification ID
   - Ticket Number
   - Channel Posted To
   - Time Sent
   - Priority Level

3. Use this for analytics:
   - Response time after notification
   - Which channels are most active
   - Notification effectiveness

## Troubleshooting

### Issue: Card Not Appearing
**Solution**: Check webhook URL is correct, Teams connector is enabled

### Issue: Dynamic Content Not Showing
**Solution**: Ensure dynamic content placeholders are correctly mapped

### Issue: Mentions Not Working
**Solution**: Verify user UPN is correct and msteams entities are properly configured

### Issue: Actions Not Clickable
**Solution**: Ensure URLs are valid and accessible

## Best Practices

✅ Use adaptive cards for rich, interactive notifications
✅ Route to appropriate channels based on urgency
✅ Include actionable buttons
✅ Test notifications in development channel first
✅ Don't spam - consolidate similar notifications
✅ Provide direct links to ticket systems
✅ Use consistent styling across all cards

## Key Takeaways

✅ Teams integration enables real-time collaboration
✅ Adaptive cards provide rich, interactive notifications
✅ Route notifications based on priority and type
✅ Deploy agents directly in Teams for easy access
✅ Monitor and optimize notification effectiveness

---

**Previous:** [Adding Escalation to Maintenance Agent](./5-adding-escalation.md)

**Next Unit:** [Testing Multi-Turn Conversations](./7-testing-multi-turn.md)
