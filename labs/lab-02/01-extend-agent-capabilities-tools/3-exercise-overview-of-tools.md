# 1. Overview of tools

**Completed**  
**100 XP**  
**8 minutes**

A tool is a reusable piece of code that can perform a specific task or provide specific functionality for an agent. For example, a tool can help an agent answer a natural language query, execute a workflow, connect to an external system, or provide topic-specific guidance. An agent is a conversational or UX-based assistant that helps users accomplish their tasks and goals in a specific domain or application.

## Understand tools

A tool, in this context, is created in the tool authoring experience in Microsoft Copilot Studio. With this feature, users can create and edit tools using a graphical user interface and publish them to a tools registry.

The tools registry helps you create a tool once and use it in multiple agents. The registry provides storage and management for metadata and execution information for tools. Users can apply the power and flexibility of tools to enhance the capabilities of agents without writing code for each agent separately. The various agents interact with the tools registry to discover tools and execution information available for a user. This capability enables tools to be created once and reused many times.

Tools are based on the following core types:

- **Prebuilt** - Prebuilt tools are similar to actions in Power Automate flows that make use of Power Platform connectors like Microsoft Teams, Office 365 Outlook, and SharePoint.
- **Prompt** - Analyze and transform text, documents, images, and data, with natural language and AI reasoning.
- **Agent flow** - These automations utilize Power Automate cloud flows to automate back-end tasks that extend your agent's capabilities to common tasks you would perform like initiating an approval and sending Teams notifications.
- **Custom connector** - Connect your agent to external services and data sources outside the Power Platform and Dataverse architectures.
- **REST API** - Flexible and scalable ways to connect your agent to custom internal programming logic.
- **Model Context Protocol (MCP)** - Connect your agent to an MCP server that handles LLM tooling.
- **Computer Use** - Give your agent access to its own Virtual Environment to perform Robotic Process Automations (RPA) for legacy apps and web browsing.

Tools can generate a contextual response to a user's query, using the results of the action performed by the tool. Alternatively, you can explicitly author a response for the tool to follow. Through tools you can effectively extend your agent beyond the conversational experience and into actionable functions that help relieve burden from repetitive tasks.

## Understand orchestration

When using tools and calling them using the instructions from your agent's Overview page, your agent makes use of generative orchestration. Copilot Studio has two orchestration modes available: Generative and Classic.

When an agent is configured to use generative orchestration, it can select one or more tools or topics, or choose from its knowledge sources, to handle user queries (including multi-intent queries), or to autonomously respond to events. If multiple tools or topics are selected, the agent calls them in sequence, after generating any questions to ask the user for missing information.

When a user sends a message, your agent selects one or more tools, topics, or knowledge sources to prepare its response. Multiple factors determine the selection. The most important factor is the description of the topics, tools, and knowledge sources. Other factors include the name of a topic, tool, knowledge source, input or output parameters, and their descriptions. Descriptions make it possible for your agent to be more accurate when it associates the user intent with tools and topics. You don't need to predict every way a user might indicate what they need.

All newly created agents default to generative orchestration, and due to the manual configurations required for classic orchestration, you'll typically only see classic orchestration in older agents built for the Power Virtual Agents architecture.

Classic orchestration requires every conversation topic, tool, and knowledge source to be manually developed for a robotic if-then response. Topics are selected based on matching a user query with trigger phrases. Tools can only be called explicitly from within a topic. Knowledge can be used as a fallback when no topics match a user's query (or called explicitly from within a topic). You must use question nodes in topics to author messages prompting the user for any required information. You must use message nodes in topics to author messages responding to the user (or call a tool from a topic). To learn more about the orchestration modes in Copilot Studio, read the [Orchestrate agent behavior with generative AI](https://learn.microsoft.com/en-us/microsoft-copilot-studio/orchestration) documentation.

In the next unit, you'll create a new prompt tool for your Contoso Support Agent to handle situations where customers require a more empathetic response beyond the instructions initially provided.

## Next Section

[Continue to Exercise 2: Build a custom prompt tool →](4-exercise-build-custom-prompt-tool.md)
