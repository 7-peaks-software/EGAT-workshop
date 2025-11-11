# Build custom agents in Agent Builder

The Electricity Generating Authority of Thailand (EGAT) receives numerous inquiries from customers regarding electricity services, billing, power outages, and transmission line safety. The EGAT Call Center (1416) handles these inquiries, but there's an opportunity to provide faster, more consistent responses through an intelligent agent. Having recently learned about building agents, you decide to create a reliable agent to help resolve common customer questions and provide standardized responses about EGAT's services.

Agents are a simple and powerful way to provide support to users. In this exercise, you'll build a customer service agent for EGAT (Electricity Generating Authority of Thailand). You'll learn how to configure the agent's basic settings, define clear instructions, create helpful suggested prompts, and adjust sharing options to control who can access your agent.

> **Note**
> 
> For this exercise, you'll create an agent that can answer questions about EGAT's services. In a real-world scenario, you would upload EGAT's official documentation, service policies, or FAQ documents as knowledge sources.

## Configure a custom agent

1. Select the **Create agent** option from the left navigation menu under Agents in the Chat tab.

   ![Create agent](image-16.png)

2. Navigate to the **Configure** tab in the Agent Builder pane that opens to the right.

   ![Configure tab](image-17.png)

3. In the **Details** section, set the agent's **Name** to: `EGAT Customer Service`

4. The agent needs a proper **Description** so users understand the purpose of the agent. Add the following description to the Description box:

   ```
   This agent provides customer support for EGAT (Electricity Generating Authority of Thailand), helping customers with inquiries about electricity services, billing, power outages, transmission lines, and general information about EGAT's operations.
   ```

   ![Custom agent details](image-18.png)

5. The **Instructions** section tells the agent how it's intended to respond to chat responses. It's important to provide clear and concise instructions so the agent understands its purpose. Fill in the text box with the following instructions:

```
You are EGAT's (Electricity Generating Authority of Thailand) virtual customer service agent. Your role is to assist customers with information about EGAT's electricity services, operations, and customer support.

When responding:

- Greet the customer warmly and professionally.
- Provide accurate information about EGAT's services, including electricity generation, transmission, billing, and customer support.
- For emergency situations involving high voltage transmission lines or towers, direct customers to call EGAT Call Center 1416 and press "1" immediately.
- For general inquiries, provide helpful information and direct customers to EGAT Call Center 1416 (press "2" for officer assistance) or email EGATCALLCENTER@egat.co.th if needed.
- If a question requires technical expertise or is outside your knowledge, politely inform the customer and direct them to contact EGAT Call Center 1416 or visit www.egat.co.th.
- Provide information in both Thai and English when appropriate.
- Offer additional help before ending the conversation.

Always aim to make the customer feel heard, valued, and supported while maintaining professionalism and accuracy.
```

Now that you've given your agent a series of tasks to follow, the agent will provide consistent and helpful responses to customer inquiries.

6. In the **Knowledge** section, you can optionally add the EGAT website as a knowledge source by enabling **Web search** or uploading relevant EGAT documentation if available.

   ![Knowledge](image-19.png)

> **Note**
> 
> The upload file option will not appear for users without the appropriate licensing. This feature requires either a Microsoft 365 Copilot license or related pay-as-you-go billing plan. Base access is limited to public website Knowledge. More information can be found at [Using agents in Microsoft 365 Copilot Chat](https://learn.microsoft.com/en-us/copilot/agents).

7. For this exercise, you can enable **Web search** to allow the agent to access public information about EGAT from their official website (www.egat.co.th). Alternatively, if you have EGAT documentation or FAQ files, you can upload them as knowledge sources.

   ![File explorer](image-20.png)

> **Tip**
> 
> In a production environment, you would work with EGAT to obtain official documentation such as service policies, FAQ documents, billing guidelines, or safety procedures to upload as knowledge sources. This ensures the agent provides accurate and authorized information.

Knowledge sources act as guidance and additional information the agent wouldn't normally have access to. By providing relevant documents or enabling web search, your agent can access information to answer user requests accurately.

8. Now scroll down to the **Suggested prompts** section and add a few pairs of Titles and Messages. These are provided to users as suggestions for how to prompt the agent and are reflected in the test pane to the right.

9. These suggested prompts should be directed toward providing users quick access to common EGAT customer service inquiries. Add the following suggested prompts:
   - **EGAT Services** - What services does EGAT provide?
   - **Emergency Contact** - How do I report an emergency with transmission lines or towers?
   - **Customer Support** - How can I contact EGAT Call Center?
   - **Billing Inquiry** - Where can I get information about my electricity bill?
   - **Power Outage** - What should I do if there's a power outage in my area?

   ![Starter prompts](image-21.png)

10. Select the **Create** button in the top-right corner of the Agent Builder window to finish creating your agent.

After selecting Create, a success screen will pop up. This screen provides you with a link to the agent. By default, this link only works for you, but you can modify the sharing settings so that others in your organization can interact with your agent.

11. Select **Change sharing settings** to view additional sharing settings.

   ![Change sharing settings](image-22.png)

The additional sharing settings for Copilot Chat agents can be changed to three different options:
- Anyone in your organization
- Specific users in your organization
- Only you

12. Save your choice or select cancel to return to the success menu then select **Go to agent** to view the finalized version of your agent.

13. After navigating to the EGAT Customer Service agent, select one of the suggested prompts such as **Emergency Contact**, and select the send button in the chat window with the prefilled suggested prompt.

   ![Emergency contact response](image-23.png)

Your agent will then craft a response providing information about EGAT's emergency contact procedures, including the EGAT Call Center 1416 number and instructions for reporting emergencies involving high voltage transmission lines or towers.

   ![Emergency contact response](image-24.png)

Try using the other suggested prompts or craft your own questions about EGAT's services, such as:
- "What is EGAT's main mission?"
- "How does EGAT generate electricity?"
- "What areas does EGAT serve?"
- "How can I contact EGAT for billing questions?"

By following these steps, you've successfully built and configured an EGAT Customer Service agent in Copilot Chat. Clear instructions, thoughtful suggested prompts, and knowledge sources are all essential to creating an effective agent. In the next module, you'll create an agent with additional features available when building agents in Microsoft Copilot Studio.

---

**Previous:** [Use agent templates in Agent Builder](./3-use-agent-templates-in-agent-builder.md)

**Next Unit:** [Check your knowledge](./5-check-your-knowledge.md)
