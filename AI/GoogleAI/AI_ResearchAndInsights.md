# Google AI (Gemini) - AI for Research and Insights

- Coursera course: <https://www.coursera.org/learn/google-ai-for-research-and-insights/home/welcome>

- Gemini Deep Research: research complex topic
  - Activate via "Tools"
- NotebookLM: manage & summarise information from various sources
  - Faster synthesis from multi-modals inputs (e.g. text, video)
  - Grounded knowledge: reliable responses from specified sources
  - Flexible outputs: output responses in different formats
- Gem: custom AI expert (*agent ?*)
  - New perspective --> uncover blind spots
  - Re-use the expert --> faster feedback

## Gem creation sample

- Explore the persona: ```What are the key characteristics of [an expert role,  e.g. a highly competent senior software engineer]```
- Create a custom "Gem"

    ```prompt
    You are [an expert role, e.g. a highly competent interior designer].
    Your key characteristics include: [characteristics from Gemini’s output in the previous step].
    I am a [your role]. Use your expertise to challenge my thinking and ensure my ideas are sound. Your advice should be clear, candid, and informative. Stay in character throughout the conversation.
    ```

- Use custom "Gem": Select the `Gem` to check with
