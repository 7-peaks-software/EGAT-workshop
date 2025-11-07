# 1. Entities in Microsoft Copilot Studio

**Completed**  
**100 XP**  
**7 minutes**

Microsoft Copilot Studio identifies and uses entities to interpret what the user is saying. For example, if the user says, "I tried to use my gift card, but it doesn't work," the agent knows to route the user to the topic that's related to gift cards not working, even if that exact phrase isn't listed as a trigger phrase.

Natural language understanding (NLU) helps the agent identify entities in a user's input. An entity represents a real-world subject, such as a phone number, zip code, city, or person's name. Your agent can recognize the relevant information from user input and then save it for later use.

For example, if the user types, "I want fifty red coffee machines," the AI can understand that:

- "Fifty" is the number "50," and it's also the number of products to purchase.
- "Red" is a color and is the color of the products to purchase.
- "Coffee machine" refers to the product that the person wants to purchase.

In Microsoft Copilot Studio, some subjects (such as numbers and colors) have already been taught to the agent. The agent author needs to specify other subjects, such as the fact that "coffee machine" is a product or that "red" is the color of a product, as demonstrated in this lab.

Two types of entities are:

- **Prebuilt** - Represent the most-used information, such as age, color, number, and name. Microsoft Copilot Studio can recognize these entities automatically. These entities are shown within the Entities list in Microsoft Copilot Studio.
- **Custom** - Are the entities that you make. While the prebuilt entities cover commonly used information types, you'll occasionally need to teach the agent's language understanding model some domain-specific knowledge. For instance, you might need to create a custom entity for your product types.

Smart match and synonyms can make your agent more intuitive:

- **Smart match** - Provides you with the flexibility to let the agent match the user's input to an entity that's a near match but not perfect. Specifically, it lets the agent autocorrect misspellings and expands the matching logic semantically, such as automatically matching "softball" to "baseball." You can turn off this feature if you need a match to be perfect, such as if the entity contains model numbers or error codes.
- **Synonyms** - Allow you to recognize that something that the user has typed matches an option that you provided. For example, for "free shipping," you can add "complimentary shipping" as a synonym. For "expedited shipping," you can add "two-day shipping" or "overnight shipping" as synonyms. If the user types any of these phrases, they're matched appropriately.

The following tasks show you how to create a custom entity and use an entity within the topic that you've already created.

## Task 1: Use entities and slot filling

In this task, you'll learn how to use entities and slot filling.

With your Contoso Customer Service agent open in Microsoft Copilot Studio, open the Check Order Status topic you created.

Select the Question node that you created to originally identify the user's full response. Select Identify, and in the slide-out menu that opens, select Create an entity.

![Screenshot of the New entity button.](new-entity-button.png)

Within the Create an entity dialog, select Closed List.

Within the Name field of the entity creation pane, enter the name **Order Action**.

Add three options within the List items called **Update**, **Check**, and **Cancel**.

You can also choose to add synonyms by selecting synonyms for each option (optional for this task).

Turn on the Smart matching toggle and then select Save.

![Screenshot of the Order Action entity with the Smart matching toggle turned on.](order-action-entity.png)

This action creates a new entity called Order Action that you can use with the Question node in your topic to replace User's entire response with Order Action.

Select the Question node you previously had selected, the Identify section should now show the Order Action entity you created.

If the Question node didn't autopopulate with the Order Action entity, select the Identify property and choose the Order Action entity.

![Screenshot of the Identify option set as Order Action.](identify-order-action.png)

Select the Select options for user option and then select all options to display to the user.

![Screenshot of entity options displayed in the agent.](entity-options-displayed.png)

You have successfully set up a custom entity for your Question node.

To test the new entity we created, save your topic and then use the test pane to test how entities work. Enter the order status trigger phrase for the Check Order Status topic.

You'll observe the process triggers the Check Order Status topic, acknowledging the user's intent to inquire about their order's status. The phrase order status is recognized as a trigger for the topic and the Question node then triggers the Order Action entity you created, which displays an option for the user to determine what they would like to do with their order. This choice is then stored in the variable Var1 that can later be used for reference.

![Screenshot of the slot filling experience.](slot-filling-experience.png)

> **Note**: The Var1 variable is by default the first variable created, if you had previously created another variable the choice would default to Var2, Var3, etc.

In the next unit, you'll make use of the variable created by your question node.

## Next Section

[Continue to Exercise 2: Variables →](2-exercise-variables.md)
