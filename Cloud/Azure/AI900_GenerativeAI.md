# (AI-900): Generative AI Workloads on Azure
  - Generative AI couse from Pluralsight (https://app.pluralsight.com/ilx/microsoft-azure-ai-fundamentals-(ai-900)-generative-ai-workloads-on-azure)

## Fundamentals
  - GenAI: create text/code/image from prompt
  - Large language model structure
    + Encoder:
      - Perform tokenization 
      - Embedding: create relationship vectors (i.e. assigns numerical values & maps them)
      - Atteention/Self-attention: use surrounding token to calculate "attention score" for a specific token
    + Decoder: Generate answers/prediction
  - Specific data: aka "**grounding**" --> provided to the LLM for generate content
  
## Generating Content
  - We can create OpenAI services (i.e service deployed using OpenAI model) on Azure --> generate text/code/image using these services
  - Azure AI Studio:
    + NLP sample: can use `Chat` playground + `Prompt catalog` to have deployed service act as specific agents (e.g. travel agent, hiking agent, real-estate agent)
    + Generate code: update "System message" in Chat
    + Generate image --> using DALL-E model
      - Use Azure OpenAI studio for now, as it has more features
      - Also has sample code to connect to service
  - Before deployment --> need to consider possible "harm" the AI system can causes & various mitigation plans, and/or compliance
    + Identiy harms, measureing them, mitigating & follow operation & readiness plans