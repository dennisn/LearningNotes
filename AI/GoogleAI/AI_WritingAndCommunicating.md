# Google AI for Writing and Communicating
  - Coursera: coursera.org/learn/google-ai-for-writing-and-communicating/home/welcome

## Communication
  - Communication key elements:
    + Purpose
    + Audience
    + Tone
  - For success communication, also need "strategic judgement" (e.g. what type of communication: phone/email/None) and accountability
  - AI usages:
    + Brainstorm
    + Rapid drafting & variation
    + Refinement/polishing
    + Practice & Simulation: rehearse with "**Gemini Live**"
    + Not "**ghost-writing**" --> may miss important context, or doesn't feel authentic

## Gemini tools
  - **The meeting assistant**
    + Google meet: Can capture meeting notes --> transform them into structured action plan to share with everyone
      ```
      Acting as a senior project manager, generate a report based on the attached transcript.  The report must have the following sections: a concise summary, a bulleted list of decisions, and a detail table of action items (columns: Action Item, Owner, Deadline).
      ```
    + Can also ask Gemini to review its own report:
      ```Evaluate the report using these criteria: Brevity, Clear action items, Clarity```
  - **The reviewer**
    + Give Gemini a persona to get feedback
      ```
      I’m working on a [type of document, e.g. proposal or report] for [brief description of your project]. 
      Act as the [stakeholder you want feedback from and list their priorities], and review the document attached. 
      From your perspective, is there any information missing?
      ```
    + Explore resolutions
      ```
      You provided the following feedback about [feedback topic, e.g. the proposal is not detailed enough]. 
      I plan to resolve your feedback by [proposed resolution, e.g. including a timeline, budget, and resourcing]. 
      Does that resolve the concerns, or are you looking for more?
      ```
    + Draft a summary of your changes: 
      ```How would you write a [desired length, e.g. 2-sentence] summary of this plan for the [document, e.g. one-pager]?```
  - **The communication composer**
    + Extract key points from a source document: `Pull [number] key points from the document that I can use in my message`
    + Identify audience-specific points: 
      ```Select some points specifically for [Role(s)]. Start by thinking about their priorities based on their roles before choosing the points I should highlight for each.```
    + Draft an email in Cavas: `Draft the email to [Role]. Include the [Points from previous step] as [format (e.g. bullets)]`
    + Refine the draft: `Make this [desired change, e.g. more formal/more detail]`
  - **Rehearsal Partner**: using "Gemini Live"
    + Ask Gemini to "role-play", give it context & settings
    + Afterward, can ask for feedback