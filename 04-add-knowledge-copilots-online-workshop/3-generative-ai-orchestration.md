# Configure generative AI orchestration

## Custom instructions
Prompt modification allows you to expand the capabilities of generative answers and knowledge sources, by adding custom instructions. When using custom instructions, it's important to follow best practices for prompt engineering. Here are some tips to help you get the most out of this feature:

- Be specific - Custom instructions should be clear and specific, so the agent knows exactly what to do. Avoid vague or ambiguous language that could lead to confusion or incorrect responses.
- Use examples - Provide examples to illustrate your instructions and help the agent understand your expectations. Examples help the agent generate accurate and relevant responses.
- Keep it simple - Avoid overloading your custom instructions with too many details or complex logic. Keep your instructions simple and straightforward so the agent can process them effectively.
- Give the agent an "out" - Give the agent an alternative path for when it's unable to complete the assigned task. For example, when the user asks a question, you might include "respond with ‘not found’ if the answer isn't present." This alternative path helps the agent avoid generating false responses.
- Test and refine - It's important to test your custom instructions thoroughly to ensure they're working as intended. Make adjustments as needed to improve the accuracy and effectiveness of your agent’s responses.

## Task 1: Configure custom instructions for Generative AI Orchestration
Custom instructions can be set in distinct places, depending on whether you use Generative AI Orchestration as the main intent recognition mechanism, or if you use the Classic natural language understanding approach. When Generative AI Orchestration is enabled, instructions need to be set at the agent level.

1. Let's first make sure Generative AI orchestration is enabled. From the navigation menu, navigate to the Overview page of your agent.

2. In the Details section of the overview page, make sure Use generative AI to determine... is enabled under Orchestration.

   ![Generative AI in conversations settings](image.png)

3. In the Details area, select the Edit pencil icon.

   ![Editing details of overview tab](image-1.png)

4. Replace what's currently in Instructions with the following:

   `Talk like a pirate and use pirate expressions.
   Use emojis in your responses. 
   Answer in less than 50 words.`

   ![Modified agent instructions](image-2.png)

5. Next, open the Test pane to test these new custom instructions.

6. In the test pane, ask a question that doesn't match an existing topic to trigger the websites we added at the beginning of the course, like: `What is Microsoft Copilot Studio?`

   ![Classic generation test](image-3.png)

## Task 2: AI general knowledge
In addition to knowledge sources, you can use AI general knowledge to allow your agent to find and present information in response to your customer's questions. General knowledge saves you from needing to manually author multiple topics, which might not even address all your customer's questions.

This capability allows the agent to try to answer question with its own knowledge, outside of any grounding data from your knowledge sources. Similar to using Microsoft 365 Copilot.

1. From the navigation menu, select the Settings button to navigate to the agent's settings.

2. In the Generative AI tab, enable Use general knowledge in the Knowledge section, then select Save.

   ![Enabled general knowledge](image-4.png)

3. To test this functionality, open the Test pane and ask a question that neither matches an existing or a configured knowledge source like: `Can you list the planets from closest to farthest from the sun?`

   ![Testing general knowledge](image-5.png)

## Task 3: Conversational boosting topic and generative answer node
With the built-in, default, natural language understanding model, any user utterance that doesn’t trigger a topic goes to the Conversational boosting topic (and then goes to fall back, if no answer is identified). Like any other topic, the logic in the Conversational boosting topic can be configured to further meet your scenarios.

1. From the navigation menu, go to the Topics tab.

2. Select the System topics tab.

3. Select the Conversational boosting topic.

4. In the Conversational boosting topic, go to the Properties of the Create generative answers node by selecting Edit in the Data sources section.

   ![Create generative answers properties](image-6.png)

5. From the Create generative answers properties pane, turn the Search only selected sources status flag to On.

6. Next, check all the knowledge source boxes EXCEPT the PDF source, as it's the slowest resource.

   ![Selected knowledge sources](image-7.png)

7. Now, uncheck Allow the AI to use its own general knowledge.

   ![Custom instructions cleared](image-8.png)

   Note:
   You may disregard the authentication warning as this won't apply to the tests done in this lab.

8. Save the current topic and return to the Overview tab.

9. Select the Edit pencil icon under Details and delete everything in the Instructions text box and select Save.

   ![Cleared agent instructions](image-9.png)

   Important:
   This step is important so that future labs are unaffected by these changes.

## Next unit: Check your knowledge
[Continue to Check your knowledge →](4-check.md)