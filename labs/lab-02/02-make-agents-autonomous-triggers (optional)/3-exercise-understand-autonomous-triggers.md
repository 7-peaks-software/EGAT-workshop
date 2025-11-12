# 1. Understand autonomous triggers

Triggers are a core feature that enable agents in Microsoft Copilot Studio to act autonomously, without requiring direct user interaction through a conversational interface. Unlike topics, tools, or knowledge, which respond to user input, triggers allow agents to respond to external events, such as updates in a Dataverse table, incoming emails, or submitted approvals.

> **Important**: Triggers are only available for agents that have generative orchestration enabled.

## How triggers work

Triggers begin with an external event, such as a row being added in Dataverse or a message being received in Outlook. This event sends a trigger payload to the agent using a connector. The payload contains dynamic information about the event, which the agent uses to determine how to respond.

When a trigger is activated, the agent executes instructions defined by the agent author. These can include both high-level instructions set in the agent's configuration and event-specific instructions included in the trigger payload.

Agents always act within the bounds of the logic and tools provided by the author. For example, a trigger could listen for when a new team member is added to a system, and respond by automatically sending a welcome message and onboarding resources using integrated tools.

## Define trigger behavior

You can define trigger behavior using two components:

- **The trigger definition** - Specifies the event to listen for and what data to include in the payload.
- **Agent instructions** - Determines how the agent interprets and responds to the event, and can be set in both the trigger and the agent's Overview configuration.

Together, these define the agent's autonomous response to external input.

## Extend triggers with tools

Triggers allow agents to use tools independently of user-initiated conversations. For example:

- **Without triggers** - An agent only sends a feedback summary email when prompted by a user.
- **With triggers** - An agent can monitor a shared Outlook inbox and, when a feedback email is received, automatically send a summary message to the agent author in Microsoft Teams.

This capability enables agents to operate proactively, improving responsiveness and automation in business workflows.

## Summary

Triggers expand the capabilities of Copilot Studio agents by enabling them to act independently in response to external events. By combining trigger definitions with well-crafted agent instructions and available tools, you can create agents that not only respond to users, but also proactively support your business processes.

In the next unit, you'll create your first trigger and orchestrate it with tools and instructions that move your agent beyond conversation and into actionable autonomy.

## Next Section

[Continue to Exercise 2: Make your agent autonomous →](4-exercise-make-agent-autonomous.md)
