# Model Context Protocol (MCP)

## MCP server introduction
  - From https://www.linkedin.com/learning/unboxing-ai-build-a-remote-mcp-server-from-zero-to-deployed-with-oauth
  - MCP: Protocol to provide context to model (i.e. AI Engine)
    + can tell client (i.e. AI Engine) what is available, and how to get it
    + Services: 
      - Tools: actions that the server can perform on behalf of the AI (i.e. custom functions)
      - Resources: information, data that the server can provide (i.e. data retrieval)
      - Prompts: extra instruction from MCP server to AI engine (i.e. prompt templates)
      - Future: Inter-server processing: can tell AI engine to get more data from somewhere else, combine with the server returned data, do more transformation, etc., before return to user
  - MCP is an open protocol (e.g. similar to HTTP)
    + Anyone can create MCP server, but quality & safety will be different per server (e.g. bad content)

## Extra learning resources

Presentation: https://www.canva.com/design/DAGvYAPQTK8/2wISjmbYEqadOFXvm9wrbg/edit?utm_content=DAGvYAPQTK8&utm_campaign=designshare&utm_medium=link2&utm_source=sharebutton

Complete the Recommeneded Training
https://www.youtube.com/watch?v=6P9pY80S0fo&ab_channel=OfficialElasticCommunity
https://www.elastic.co/what-is/large-language-models
LinkedIn Learning(1 month free - For access beyond September, you’ll need to register again in mid-September registrations. Communications will be shared closer to the date.)
Prompt Engineering: https://www.linkedin.com/learning/introduction-to-prompt-engineering-for-generative-ai-24636124/joining-the-nlp-revolution?u=75714794
MCP Server: https://www.linkedin.com/learning/unboxing-ai-build-a-remote-mcp-server-from-zero-to-deployed-with-oauth/why-mcp-changes-everything?u=75714794
AI Agents (MCP + A2A): https://www.linkedin.com/learning/hands-on-ai-building-ai-agents-with-model-context-protocol-mcp-and-agent2agent-a2a/why-mcp-a2a?u=75714794
Context Engineering: https://www.linkedin.com/learning/context-engineering-for-developers/introduction?u=75714794
Free alternative if you run out of access: https://www.youtube.com/watch?si=4EEmy7ke0b206_MD&v=kMiqtTbBbzI&feature=youtu.be
Internal Resource: https://github.com/WiseTechGlobal/InternationalLogistics.Content/blob/HEAD/Core/ILC-Nanjing/AI/Copilot-Quick-Start-Guide.md
  
## MCP Security concerns
  - Vibe coding: Good for prototype, but better start off from scratch for production
    + Many concerns may be overlooked, especially security
  - Excessive permissions: MCP is between LLM & API --> resfrain from expose API through MCP, especially "destructive" abilities
    + LLM is not human equivalent: may mis-understand instruction, or intention
  - Agent Error: LLM model, while working, may ends up in error/dead-end, so will discard those errors. However, MCP server provide "actuator" for LLM agent --> the errors will result in impacts in real-life
  - Promp injection: current integration of MCP server --> put prompt from MCP server to LLM agent ==> theoretically, MCP server may include "prompts" that lead the agent astray !
  - Confused deputy (man-in-the-middle attack ?): best practice to use official systems, as they have built in mechanism to try to avoid this
    + Session hi-jacking - malicious MPC server can give authorization session to others
    
