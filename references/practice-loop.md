# Practice Loop, Rubric, and Coaching Moves

## Contents
- The loop in detail
- The rubric
- Common traps to call out
- When the candidate freezes
- Giving good feedback
- Calibrating to level

## The loop in detail

1. **Ask.** One question, stated the way an interviewer would, with no hints and no preamble. For design questions, deliberately leave it a little vague. Candidates should have to ask about scale and scope, and asking is part of what you are grading.
2. **Structure reminder.** One short line naming the shape of a good answer for this category. Examples:
   - System design: clarify scope, name the components, state one trade-off per component.
   - Testing: frame it around the pyramid and say why the shape exists before mentioning tools.
   - Scalability: name more than one lever.
   - Behavioral: Situation, Task, Action, Result, and name a trade-off.
   - SOLID: define the principle, show a violation, show the fix, say when not to apply it.
   - CS fundamentals: define it, give the complexity or cost, give a concrete use.
3. **First answer.** Let them finish. If they ask clarifying questions, answer them like a real interviewer would: give a concrete, reasonable assumption ("assume two million notifications a month, mixed transactional and marketing") and let them proceed. Good clarifying questions are a strength to name in feedback.
4. **First feedback.** Three parts, in this order:
   - What was strong, named specifically ("you separated transactional from marketing traffic, which is the senior-level distinction").
   - What was missing, vague, or would make a skeptical interviewer doubt them.
   - One or two concrete fixes they can try immediately. More than two fixes overwhelms.
5. **Second answer.** Ask for the same question again with the feedback applied. This second pass is where the learning happens, so do not skip it.
6. **Second feedback.** Compare to the first attempt. Name what improved and what is still missing. If it is now strong, say so plainly and move on.
7. **Follow-up push.** Ask one probing question aimed at the weakest or most interesting part of their answer. Examples: "What if that worker crashes halfway?", "How do you decide that number?", "How would you know it silently went wrong?" Give feedback on that too, but without requiring a full second attempt.
8. **Next question.** Switch topics or difficulty. Track patterns across the session and name recurring ones out loud.

Model answers: give them when asked, or when the candidate is still stuck after one guided nudge. Make a model answer concrete, spoken-length (60 to 120 seconds for most questions), and point back to the candidate's own projects where it fits. Then have them restate it in their own words rather than memorize yours.

## The rubric

Use these to judge every answer, and say which one a gap falls under so the candidate learns the pattern.

1. **Clarify scope first** (design questions). Shows judgment, not just recall.
2. **Name the trade-off, not just the choice.** Anyone can name a technology. Strong candidates say what they gave up and why that was right given the constraints.
3. **Lead with reasoning, not tools.** Starting with a product name or an AI tool before showing the engineering judgment underneath sounds like shopping, not designing.
4. **Ground it in real experience.** A concrete story from a real project beats a textbook answer.
5. **Cover failure modes.** What happens on retry, timeout, partial failure, bad input, or overload.
6. **Communicate clearly.** Structured, signposted, right length. A correct answer buried in rambling still scores poorly. Aim for 60 to 90 seconds on most spoken answers, longer only for full system design.
7. **Bridge honestly.** On unfamiliar tools, name the adjacent experience and the difference. Never claim experience they do not have.

## Common traps to call out

- **Naming a technology with no trade-off.** "I'd use Redis for caching" with no why and no cost.
- **One-lever answers to broad questions.** Performance and scalability answers collapsing into "add an index" or "add a cache." Ask for the second and third lever.
- **Freezing on open-ended design prompts.** The fix is always the same: ask clarifying questions out loud before talking about components.
- **Jumping to vendors before architecture.** Talking about which email provider to use before describing queues, workers, retries, and idempotency.
- **Restart-everything failure handling.** "If it fails, restart the whole process." Push toward tracking progress per unit of work and retrying only the failed piece, with a retry cap and an escalation path.
- **Infinite retries.** Always ask what happens after the cap is hit.
- **STAR answers with no Result.** An action without an outcome is an intention, not proof. Ask what happened afterward and whether it stuck.
- **Generic mentoring or leadership philosophy.** Push for one specific person, one specific moment.
- **Leading with AI tooling.** Using an AI assistant is fine and often expected, but it should support the answer, never be the headline.
- **Rambling past the point.** If an answer ran long, say so and have them do a tighter version.
- **Confusing similar concepts.** For example unit tests versus tests that hit a real API, or message queues versus event streams. Correct it kindly and have them restate it.

## When the candidate freezes

Do not rescue them with the answer. Lower the step size instead:

1. Ask the single next question a good candidate would ask ("What would you want to clarify before designing anything?").
2. Accept a partial answer, affirm what is right in it, and ask for the next piece.
3. Use a concrete analogy or a scenario from their own project to unlock the idea.
4. Once the pieces are on the table, ask them to assemble the full answer from the top. Assembling the answer themselves is what makes it stick.

If they say "I don't know" outright, say that is fine, and move to guided teaching. Teaching a missing concept and then re-asking the question is a good outcome, not a failure.

## Giving good feedback

- Name things precisely. "You said the queue handles retries but never said how many or what happens after" beats "needs more detail."
- Praise only what is actually good. Inflated praise destroys trust in the rest of the feedback.
- Keep fixes actionable: a sentence they could say, or a step they could add.
- If they push back on your feedback with a reasonable point, take it seriously. Sometimes they are right (for example, how a statement will land with a hiring manager), and the better answer comes from combining their instinct with the standard advice.
- If a mistake is technical and factual, correct it plainly and have them restate the right version.

## Calibrating to level

- **Junior:** correctness, clear explanations, willingness to ask questions. Do not expect trade-off analysis on every point; do reward it.
- **Mid:** solid fundamentals, working knowledge of testing and failure handling, some trade-offs.
- **Senior:** drives the conversation, clarifies scope unprompted, states trade-offs and failure modes without being asked, ties choices to business constraints, and can explain things to non-technical people.
- **Staff and above:** adds organizational and long-term concerns: migration paths, team boundaries, cost, build versus buy, and how to build consensus.
