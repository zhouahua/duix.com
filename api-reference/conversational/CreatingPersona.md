# Creating a Persona

Personas are the ‘character’ or ‘AI agent personality’ and contain all of the settings and configuration for that character or agent. For example, you can create a persona for ‘Loving girlfriend’ or ‘Dr. Ava’. Personas are where you can customize the layers for CVI as well as prompt the LLM to give it a personality and context.

A persona consists of:

*   **Persona Name** - This is the name that is shown when a replica using your Persona joins the call.
    
*   **System Prompt** - This is the system prompt that the LLM uses for its instructions. Use this to include instructions on who the persona is and how you want them to behave.
    
*   **Knowledge/Context** - This is the knowledge-base that will be fed into the LLM model for your persona. You can dump documentation, background, writing etc here.
    
*   **Layers** - Optionally, you can customize different layers of CVI or use different modes, including selecting which LLM you want to use.
    
*   **LLM** - By default personas use a DUIX optimized variation of Qianwen.
    
*   **Replica ID** (optional) - Optionally you can specify a default replica you’d like this persona to use. You can always override during conversation creation time to use a different replica.
    

# How to Create a Persona

### Via the UI

> Dashboard has limited options You cannot currently customize all layers via the dashboard UI

Navigate to the [Duix.com **Platform**](https://platform.duix.com/). On the sidebar click on Persona Library. Finally, click Create Persona.

> Limits for system prompt or knowledge are different depending on the LLM model being utilized.

A good system prompt and context base is key to have your persona act the way you want it to during a conversation. Here are some things to keep in mind:

### System Prompt

The system prompt should inform who the persona is and how they should act. These are the persona’s ‘instructions’.

For the system prompt:

*   Assume a character
    
*   Provide clear instructions
    
*   Keep it concise
    
*   Keep knowledge in the knowledge prompt
    

Remember that CVI has vision capabilities, you can use this as well to prompt behavior and responses. Here’s an example of a simple, good system prompt:

> You are Tim, a replica created using Tavus. You are taking on the personality of Hassaan Raza, the CEO and Co-Founder of Tavus. You will be talking to strangers and your job is to be conversational, ask them questions about themselves. Be witty and charming. If you don’t know something, just say you’ll get back to them on that.

### Context / Knowledge-base

The context is the persona’s ‘knowledge base’. This is where you can feed in information the persona needs to know, including more extensive background about itself, your companies docs, sales decks etc. Currently we only allow you to pass in text, so you’ll need to convert any documents (like PDFs or slide decks) into text.

For the knowledge/context:

*   Make sure not to accidentally override the system prompt with instructions that may be hidden in your context/knowledge
    
*   Keep the knowledge-base clean and filtered
    
*   You do not need to include participant or conversation/specific context, you can pass that in during conversation creation time
    

The Duix orchestration system will automatically attempt to optimize and align with the selected LLM to optimize your persona for natural conversation.