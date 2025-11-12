# Check Your Knowledge

## Introduction

Test your understanding of multi-agent orchestration concepts and the techniques you've learned in this lab.

## Quiz

### Question 1: Multi-Agent Architecture

**Which pattern is best for a system with multiple specialized agents of equal authority?**

A) Hub-and-Spoke Pattern
B) Escalation Pattern
C) Peer-to-Peer Pattern
D) Waterfall Pattern

<details>
<summary>Show Answer</summary>

**Answer: C) Peer-to-Peer Pattern**

Explanation: The Peer-to-Peer pattern is ideal when agents have equal authority and can hand off to each other as needed, without a clear hierarchy.
</details>

---

### Question 2: Context Preservation

**What is the primary benefit of preserving context during agent transfers?**

A) Faster response times
B) Users don't have to repeat information
C) Reduced computational costs
D) Better agent analytics

<details>
<summary>Show Answer</summary>

**Answer: B) Users don't have to repeat information**

Explanation: Context preservation ensures that information collected by one agent (like user name, issue details) is available to the next agent, creating a seamless experience where users don't need to re-provide the same information.
</details>

---

### Question 3: Escalation Logic

**In the escalation scoring system we built, which scenario should trigger immediate emergency escalation?**

A) Escalation Score = 40
B) Escalation Score = 75
C) UrgencyLevel = "Medium"
D) Location contains "Office"

<details>
<summary>Show Answer</summary>

**Answer: B) Escalation Score = 75**

Explanation: In our implementation, an escalation score of 75 or higher triggers immediate transfer to the Emergency Response Agent. This score can be achieved through critical urgency level, safety keywords, or combination of high-risk factors.
</details>

---

### Question 4: Teams Integration

**What is an Adaptive Card in Microsoft Teams?**

A) A credit card for Microsoft services
B) A rich, interactive notification format
C) A Teams user profile
D) A permission system

<details>
<summary>Show Answer</summary>

**Answer: B) A rich, interactive notification format**

Explanation: Adaptive Cards are a platform-agnostic format for exchanging content in a structured way. They allow you to create rich, interactive notifications with buttons, images, and formatted text.
</details>

---

### Question 5: Transfer Nodes

**What happens if you don't map variables when using a Transfer Conversation node?**

A) The transfer will fail
B) The conversation will start fresh with no context
C) The system will auto-map matching variable names
D) An error will be logged

<details>
<summary>Show Answer</summary>

**Answer: B) The conversation will start fresh with no context**

Explanation: If you don't explicitly map variables during transfer, the receiving agent won't have access to information collected by the source agent. The transfer will still succeed, but context will be lost.
</details>

---

### Question 6: Best Practices

**How many agents should you typically start with when building a multi-agent system?**

A) 1 agent
B) 2-3 agents
C) 10+ agents for complete coverage
D) As many as possible

<details>
<summary>Show Answer</summary>

**Answer: B) 2-3 agents**

Explanation: It's best to start simple with 2-3 specialized agents, validate the approach works well, then expand as needed. Starting with too many agents increases complexity and maintenance burden.
</details>

---

### Question 7: Generative AI

**In Copilot Studio, what does setting orchestration mode to "Generative" enable?**

A) Automatic code generation
B) AI-powered responses using knowledge sources
C) Automated agent creation
D) Graphics and image generation

<details>
<summary>Show Answer</summary>

**Answer: B) AI-powered responses using knowledge sources**

Explanation: Generative orchestration allows the agent to use AI to formulate responses by drawing on configured knowledge sources, rather than only following predefined topic paths.
</details>

---

### Question 8: Error Handling

**What should you implement to prevent infinite transfer loops between agents?**

A) Longer timeout periods
B) More agents
C) Transfer counter with maximum limit
D) Stronger AI models

<details>
<summary>Show Answer</summary>

**Answer: C) Transfer counter with maximum limit**

Explanation: Implementing a transfer counter that tracks how many times a conversation has been transferred, combined with a maximum limit (e.g., 3 transfers), prevents infinite loops and provides a fallback to human support.
</details>

---

### Question 9: Teams Notifications

**Which of the following is NOT a valid method for sending Teams notifications from Copilot Studio?**

A) Power Automate Teams connector
B) Incoming Webhook
C) Direct SMS to Teams phone numbers
D) Adaptive Cards

<details>
<summary>Show Answer</summary>

**Answer: C) Direct SMS to Teams phone numbers**

Explanation: While you can integrate with Teams via Power Automate connectors, Incoming Webhooks, and Adaptive Cards, there's no direct SMS-to-Teams integration for notifications from Copilot Studio.
</details>

---

### Question 10: Testing

**What is a multi-turn conversation?**

A) A conversation with multiple users
B) A conversation with multiple exchanges maintaining context
C) A conversation that changes direction multiple times
D) A conversation in multiple languages

<details>
<summary>Show Answer</summary>

**Answer: B) A conversation with multiple exchanges maintaining context**

Explanation: A multi-turn conversation involves multiple back-and-forth exchanges where context is maintained throughout. Each turn builds on previous exchanges rather than starting fresh.
</details>

---

## Practical Challenge

### Challenge: Design Your Own Multi-Agent System

**Scenario**: EGAT wants to create an IT helpdesk multi-agent system for:
- Password resets
- Software installation requests
- Hardware repairs
- Network connectivity issues

**Your Task**: Design a multi-agent architecture for this scenario

**Questions to Answer**:

1. **How many agents would you create?**
   - List each agent and its purpose

2. **What would be your handoff triggers?**
   - When should Agent A transfer to Agent B?

3. **What context should be preserved across transfers?**
   - List key variables to pass between agents

4. **What escalation logic would you implement?**
   - When should issues escalate to higher-tier support?

5. **How would you integrate with Teams?**
   - Which channels would get notifications for different issue types?

### Sample Solution

<details>
<summary>Show Sample Solution</summary>

**Agents**:
1. **IT Tier 1 Agent**: Handles FAQs, basic troubleshooting, routes complex issues
2. **Password & Access Agent**: Handles password resets, account unlocks
3. **Hardware Support Agent**: Handles hardware repairs, replacements
4. **Network Specialist Agent**: Handles network and connectivity issues
5. **IT Escalation Agent**: Handles complex/urgent IT emergencies

**Handoff Triggers**:
- Password keywords → Transfer to Password & Access Agent
- Hardware keywords (laptop, printer, monitor) → Transfer to Hardware Support Agent
- Network keywords (wifi, vpn, connection) → Transfer to Network Specialist Agent
- Urgent/critical keywords → Transfer to IT Escalation Agent

**Context to Preserve**:
- User name and employee ID
- Issue category
- Priority/urgency level
- Device/equipment information
- Previous troubleshooting steps attempted

**Escalation Logic**:
- Business-critical systems (score +50)
- Affecting multiple users (score +30)
- Urgent urgency level (score +30)
- Score ≥ 75 → Escalate to IT Escalation Agent

**Teams Integration**:
- #it-helpdesk channel: Standard requests
- #urgent-it channel: High priority
- #it-emergency channel: Critical issues
- Mention @it-oncall for emergencies
</details>

---

## Reflection Questions

Take a moment to reflect on what you've learned:

1. **What was the most challenging part of this lab?**

2. **How will multi-agent orchestration help EGAT's maintenance department?**

3. **What additional features would you add to your multi-agent system?**

4. **How would you measure the success of your multi-agent deployment?**

---

## Additional Resources

Want to learn more? Check out these resources:

- [Multi-agent orchestration documentation](https://learn.microsoft.com/en-us/microsoft-copilot-studio/advanced-multi-agent)
- [Transfer conversation best practices](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-send-message#transfer-conversation)
- [Adaptive Cards documentation](https://adaptivecards.io/)
- [Power Automate Teams connector](https://learn.microsoft.com/en-us/connectors/teams/)
- [Copilot Studio analytics guide](https://learn.microsoft.com/en-us/microsoft-copilot-studio/analytics-overview)

---

**Previous:** [Testing Multi-Turn Conversations](./7-testing-multi-turn.md)

**Next Unit:** [Summary](./9-summary.md)
