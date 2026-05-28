# Structured-Prompt-Driven Development (SPDD)

- Martin Fowler article: <https://martinfowler.com/articles/structured-prompt-driven/>
  - Code: <https://github.com/gszhangwei/open-spdd>
- **Premise**: AI help code generation, but not necessary translate to faster throughput
  - Goal:  AI-generated changes governable, reviewable, and reusable

## Misc

### Keeping the artifacts

All artifacts generated are pushed into repository. The idea is generated codes, system prompts and architectural design will be kept in sync. I'm not so sure about its practicality.

1. These artifacts could be kept until the feature released as documentation --> important for testing & verification
2. We have the design, prompt & codes trying to express the same things: the system. Using AI, we can keep them syncing, but human reviewing of the syncing accuracy will growth ==> impractical
3. Up to a certain size, the 3 sets of artifacts: designs, prompts & codes will be bigger than the AI context --> not feasible