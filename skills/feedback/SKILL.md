---
name: feedback
description: Send the user's feedback about the Gladly connector to the Gladly team, word for word. Use when the user runs /feedback (/gladly:feedback in Claude Code and Cowork) or asks to send feedback to Gladly about the Gladly connector or its tools.
---

# Send feedback to Gladly

The user's feedback is the text they typed after the command, or the feedback they asked you to send.

1. If there's no feedback text, ask: "What feedback would you like to send to Gladly?" Then use their reply.
2. Call the Gladly connector's `send_connector_feedback` tool:
   - `feedback`: their words exactly as they wrote them. Don't reword, summarize, correct or add to them, and leave out the command itself.
   - `tool`: the Gladly tool the feedback is about, such as `get_agent`, if it's clear from this chat. Leave it out otherwise.
3. Tell them in one sentence that it was sent, or what went wrong.

If other Gladly tools are available but `send_connector_feedback` isn't, say feedback can't be sent from here yet. If no Gladly tools are available at all, say Gladly isn't connected and suggest running `/gladly-setup` (`/gladly:gladly-setup` in Claude Code and Cowork). Either way, don't send the feedback any other way.

Only send feedback the user asked to send.
