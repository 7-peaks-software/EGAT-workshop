# 5. Question node behavior

**Completed**  
**100 XP**  
**6 minutes**

Previously, this lab covered the basics of the Question node and built on this concept by using entities and slot filling. In addition to storing a user's response, the Question node has more behavior options that you can set up. One option is the ability to skip the asking of a question if the variable that it's linked to already contains a value. You observed this process in action in a previous exercise where the question was skipped when the agent was asked to check an order. The question was skipped because, by using entities and slot filling, you allowed Microsoft Copilot Studio to retrieve data from the sentence that the user asked and then store the data within the variable. After the Question node was reached by Microsoft Copilot Studio, it already contained data, so the question didn't need to be asked again. This approach is more efficient because, when the user or customer is talking to an agent, they won't need to answer the same question multiple times.

Within the Check Order Status topic, select the Question node. Then, select the ellipsis within the top right corner of the Question node to extend the menu, as shown in the following screenshot, then choose Properties from the menu.

![Screenshot of the Properties menu.](images/image-28.png)

Select Question behavior from the Question properties panel that appears.

![Screenshot of the Question behavior property.](images/image-29.png)

The Question node has several configurable options so that you're able to better identify what the user's response is to the question that you're asking. This component is important when you're developing conversational applications. Because regardless of the type of AI that's behind the scenes managing the natural language responses, a user might provide unexpected or unidentifiable answers. The ability to handle the agent's behavior in those circumstances helps you provide an improved customer experience. This scenario also happens in real life, when you ask a question to another person, and they don't understand the question. For the best experience and conversation, it's important to rephrase or act differently than to repeat the same question that wasn't originally understood.

The following question behavior controls are available for you to choose from in the Question behavior property window:

- **Skip behavior/Skip question** - An agent author can skip the question if the variable already has a value. The variable in the question could have a value that was set somewhere else in the topic, in another topic, or through slot filling and by using entities. This behavior allows the agent author to skip the question, or if the variable has a value, to ask the question anyway. Other available options include using Power Fx to create a condition, and if that condition is true, to skip the question.
- **Reprompt/How many reprompts** - You can set up the behavior to repeat the question a specific number of times, and you can select from the dropdown menu to not repeat, repeat once, or repeat up to two times. Similar to the skip question option, you can also use Power Fx to set the condition for this behavior to occur. You can modify the Retry Prompt option, which only occurs if you have retries selected to repeat the question. By selecting the Retry Prompt option, you can add a different message to reword the question, and you can add message validations to make the question sound more natural and be more helpful to the customer or user.
- **Additional entity validation/Condition** - This behavior is important if you have specific conditions to validate if an entity is valid to be slot filled and is dependent on the entity type. You can also use the same prompt behavior to change the prompt, if the conditions aren't met, to encourage the user to provide a different input.
- **No valid entity found/Action if no entity found** - If no entity is found, rather than skip the question, you can specify the behavior, such as leave the variable empty, set the variable to something specific or dynamic (by using Power Fx), or call the escalate system topic.
- **Interruptions** - You can indicate whether a customer should be able to switch to a different topic than the current topic that the Question node is in. This option is useful if a customer is likely to answer a question with another question and you want to continue the conversation without needing to handle all exceptions within a single question node.

Now that you're aware of the core functionality of the Question node and associated behavior, you can explore the rich text responses in the Message and Question nodes.

## Next Section

[Continue to Exercise 6: Rich text options for message and question nodes →](6-exercise-rich-text.md)
