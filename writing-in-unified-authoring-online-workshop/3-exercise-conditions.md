# 3. Use variables in conditions

**Completed**  
**100 XP**  
**4 minutes**

Your topic has currently been updated from the second lab with entities and slot filling capabilities, and it's using dynamic data to store variables and reuse those variables in messages to provide a dynamic authoring experience. Now, you'll use the same variable in a condition statement in Microsoft Copilot Studio.

With condition statements in Microsoft Copilot Studio, an agent author can determine behavior under certain conditions that can be true, false, or something else (for example, if it's blank). Condition statements allow and promote flexibility in the authoring canvas, allowing you to provide great customer or user experiences based on their needs while limiting the need to create several similar topics. After you begin to use conditions, you'll create branches, which create separate flows that the person talking to the agent can be directed to. These branches can have their own conditions, depending on what behavior you want to create.

For more information on conditions, see Authoring Using Conditions.

## Task: Create a condition by using variables

In this task, you'll create a condition based on the three variable options that were used in the custom CustomerAction entity from the first exercise.

In your authoring canvas, under the Message node that you modified in the previous task, select the plus (+) icon to add a new node and then select Add a condition.

![Screenshot of the Add a condition option.](add-condition-option.png)

Two new nodes will appear, one is your Condition and the other is an exception for All other conditions.

In your Condition node, select the Select a variable option and then select your Customer Action global variable.

![Screenshot of the view after the condition is added to the topic.](condition-added-view.png)

Keep the condition operator as is equal to and then select the empty box beneath to display the three available options from the selected variable. Select the update option.

![Screenshot of the condition for when the CustomerAction variable is update.](condition-customeraction-update.png)

A completed condition should now show: if the CustomerAction is equal to update.

![Screenshot of the completed condition.](completed-condition.png)

Create two more conditions in this branch for the two other options for the Customer Action variable (check and cancel). Select the plus (+) icon to add a node above the condition and then select Add a condition to add another conditional branch.

![Screenshot of the Add a condition option to add another condition.](add-condition-another.png)

Repeat the previous steps by selecting your Global.CustomerAction variable and then select the check and cancel options in two other conditions so that you'll have a conditional branch with three options (including All Other Conditions), as shown in the following screenshot.

![Screenshot of three conditions.](three-conditions.png)

Under each condition node, add a Message node that will display different text depending on the condition, as shown in the following example.

![Screenshot of the final step with a Message node under each condition.](message-node-conditions.png)

Save your topic and then select the Test your agent option to explore the different trigger phrases and conditions that lead the user to view different message outcomes.

Conditions are foundational tools that help you create tailored experiences based on what the user has selected or answered in previous questions. You can nest conditions within other conditions for more complex logic.

Congratulations, you've now completed the basics of using conditions and using variables as parameters within them.

## Next Section

[Continue to Exercise 4: Use topic nodes →](4-exercise-topic-nodes.md)
