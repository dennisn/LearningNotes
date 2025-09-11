# Build AI Agents and Chatbots with LangGraph
  - URL: https://www.linkedin.com/learning/build-ai-agents-and-chatbots-with-langgraph
    + Author: Kumaran Ponnambalam
    + Code sample: https://github.com/LinkedInLearning/build-ai-agents-and-chatbots-with-langgraph-2021112
  - Also URL: https://www.linkedin.com/learning/hands-on-ai-building-ai-agents-with-model-context-protocol-mcp-and-agent2agent-a2a
    + Code sample: https://github.com/LinkedInLearning/hands-on-ai-building-ai-agents-with-model-context-protocol-mcp-and-agent2agent-a2a-6055298

## Concepts
  - Model Context Protocol: needs to unify access to resources
    + MCP server: acts as consistent interface protocol, so AI models can access resources, even though the resources are exposed with different protocol
  - Components of MCP: Host, Server and client
    + Host: the application wants to access resources/tools
    + Server: expose resources/tools via MCP protocol
    + Client: a module inside MCP host that allows communicate with MCP server using MCP protocol
  - Resources: Read-only data
  - Tools: executable functionalities, allowing operations like create, retrieve, update, and delete (CRUD)
  - Prompt template: usually system prompts with placeholders for user queries, context, and other information --> can be listed by the MCP server itself
    + Help standardize interactions and promote reusability (by using same template for a specific function)
    + Supporting metadata and dynamic prompts based on user inputs or system conditions.

### MCP communication
  - Three types of messages: requests (client to server), responses (server to client), and notifications (server-initiated alerts to the client).
  - Two transport protocols: STDIO (for communication on the same machine) and HTTP/SSE with server-side event (i.e. SSE) support (for communication with remote or local servers).
  - Message Format: All messages are exchanged in JSON-RPC 2.0 format.
  - MCP communication SDKs: prebuilt libraries used by both client/server for communication implementation

## MCP SDKs
  - Python: FastMCP, LangChain, LlamaIndex

## Basic ReAct Agent

  - ReAct: "Reason-and-Act" --> enhanced explain-ability
    + Using a Large Language Model (LLM) to understand and reason the user request, then act upon it
    + Uses a cycle where the LLM analyzes the input (thought), executes actions (action), and validates results (observation). This cycle repeats until satisfactory results are achieved
