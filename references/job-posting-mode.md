# Job Posting Mode

Use this when the candidate wants to practice for a specific role. The goal is to turn a job posting into a targeted practice plan and an honest picture of where they stand.

## 1. Get the posting
- If they give a URL, fetch it with WebFetch. If the fetch fails or the page needs a login, ask them to paste the text.
- If they paste text, use it directly.
- Read the whole posting before saying anything. Do not guess at what a company does; if the posting is thin, say so.

## 2. Extract what matters
Pull out and keep in mind:
- **Role, level, and location or remote policy.**
- **Named technologies** (languages, frameworks, databases, infrastructure, testing tools, workflow or messaging tools).
- **Responsibilities** that imply interview topics (mentoring, code review, on-call, cross-team work, communicating with non-technical people).
- **Process or methodology keywords** (TDD, spec-driven development, CI/CD, observability, multi-tenancy, compliance).
- **Domain** (healthcare, legal, finance, e-commerce). Domain shapes the best design scenarios and the reliability, audit, and privacy concerns an interviewer will care about.
- **Compensation range**, if listed.

## 3. Give an honest fit assessment
Compare the posting to the candidate's profile (collect it first if you have not). Present three short lists:

- **Strong matches:** where their real experience maps directly. Say which project to lead with for each.
- **Adjacent experience:** where they have done the same kind of thing with a different tool. These need the honest-bridge phrasing: "I have not used X, but I have built the same kind of system with Y, and here is what I expect to be different."
- **Real gaps:** named requirements with no direct or adjacent experience. Do not hide these. A real interviewer will probe them, so practice names them calmly and shows a credible plan to close the gap.

Be fair in both directions. Do not talk them out of applying and do not hype the fit. Tell them what an interviewer is likely to focus on.

## 4. Build the practice plan
Offer a plan with three parts, and let them choose the order:
1. **Technical rounds:** pick questions from the question banks that match the posting's stack and responsibilities, then add 3 to 5 custom questions written for this role. For example, if the posting names a workflow engine, ask what a durable workflow is and why it beats a plain queue with retries; if it names snapshot testing, ask what snapshot testing is and when it beats assertion tests; if it names multi-tenancy, ask how to isolate customer data.
2. **Stack-gap drills:** one question per real gap, phrased the way an interviewer would probe it ("You have not used X. How confident are you that your experience transfers?").
3. **Behavioral and communication:** match the posting's soft requirements (mentoring, working with non-technical stakeholders, remote or distributed teams).

If the posting mentions an HR screen, read `hr-screen.md` and offer that too.

## 5. Teach on demand
When a candidate does not know a tool or concept from the posting, teach it at the level an interviewer would expect: what problem it solves, how it differs from the simpler alternative, and one example from their own experience that is similar. Then quiz them on it. Keep the "why would you use it instead of the simpler thing" angle front and center, since that is the question interviewers actually ask.

## 6. Saving the plan
If you can write files, offer to save a one-page summary of the role snapshot, fit assessment, and question plan (named after the company) so they can come back to it.
