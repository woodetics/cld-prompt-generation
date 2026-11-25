# Big-Task Prompt Interviewer (Playbook)

Claude: read this whole file before you answer. It tells you how to run an interview at the start of a big task, so the final prompt captures what the user truly wants. After that you build, or you hand the prompt to a fresh session.

The research behind every rule is in KNOWLEDGE.md. You do not need to read it to follow this file.

## How the user starts this

Any of these works:

- "Read PLAYBOOK.md, then I want to build [thing]."
- Drop this file in a repo as `.claude/skills/prompt-interviewer/SKILL.md` (add a short name and description header on top) or reference it from CLAUDE.md.
- Paste the Quick Start block at the bottom into any chat.

## Rule zero: nothing gets built until the prompt is approved

While you run this playbook, do not write code, create project files, or start the work. The only files you may write are the brief and the final prompt (Phase 6), and only after the user approves the read-back (Phase 5). If the user asks for ideas, options or a plan, give that and stop.

## Phase 0. Triage: is this actually a big task?

Run the interview only when at least two of these are true:

- It will take more than a short session, or touches many files or many steps.
- The request is ambiguous, or there is more than one reasonable reading.
- Redoing the work would be expensive, or some actions cannot be undone.
- It will run unattended (scheduled, overnight, a long agent loop).
- The result will be used by other people.

If the task is small and clear (a typo, a one-file fix, a single question), skip the interview and just do it. Four questions on a typo fix is waste. Say in one line why you are skipping.

## Phase 1. Silent scan (no questions yet)

Collect what you can find without asking. Never ask the user something their files already answer.

- Repo work: README, CLAUDE.md, package or config files, folder layout, recent commits, existing tests, conventions.
- Any attached files, links, notes, earlier chats or documents.
- Research or content work: whatever briefs, drafts, samples or past work exist.
- Tools you have (browser, search, connectors, subagents), so you know what is possible.

The test for every future question: could I find this by reading, and would I actually go look? If both are yes, read it instead of asking.

## Phase 2. Restate and expose your guesses

Write 2 to 3 sentences on what you think the user wants. Then two short lists:

- Inferred: things I worked out from the context (the user only corrects if wrong).
- Could not infer: things I need from you.

This is cheap and it gets corrections early. Keep it short. Do not turn it into a lecture.

## Phase 3. Interview

### How to ask

- Use the AskUserQuestion tool if you have it. It takes 1 to 4 questions per call, 2 to 4 options each, a short header (12 characters max), and a multiSelect flag. The user can always type their own answer. This tool is not available inside subagents, so the main agent runs the interview.
- No such tool? Ask in plain text, numbered, with lettered options so the user can reply "1A, 2C".
- Batch 3 to 4 questions per round. Two rounds is the usual maximum. Large or high-risk projects may need a third.
- Order questions by decision impact: the answer that changes the architecture, format or scope goes first.
- Every question is multiple choice with your recommended option first, labeled as recommended, plus the free-text escape. Add a one-line reason for asking in the option description when it is not obvious.
- Offer "use your defaults" as an easy exit.
- Prefer questions that are not obvious: tradeoffs, edge cases, failure modes, taste. Skip anything with one sensible answer. State that default and move on.
- Call a question blocking (the work goes the wrong way without it) or assume-and-flag (you pick a safe reversible default and record it). Only blocking questions get asked.

### When to stop asking

Stop when any of these is true:

- The remaining unknowns each have a safe, reversible default.
- More answers would not change the plan.
- The user says "just go" (or something like it).
- Two rounds are done.

If the user is vague, do not interrogate. Offer 2 to 3 concrete example options and let them pick.

### The dimension checklist

Models under-ask on their own, so walk this list against what you scanned. Ask only about the dimensions that are still open and that matter for this task.

1. Outcome and why: the observable pain, the ideal end state, what it replaces. What does the user do today and what does that cost them?
2. Users and non-users: who consumes the result, their skill level, and who it is explicitly not for.
3. Scope: must-haves for v1, nice-to-haves, explicit non-goals, and the effort budget (prototype, solid v1, production, or "surprise me").
4. Success and checks: what "done" looks like, measurably, and what check Claude can run to prove it (a command, a test, a screenshot compared to a mockup, a rubric).
5. Constraints and preferences: stack, existing patterns to follow, libraries to avoid, budget, deadline, privacy, platform, tone.
6. Inputs and access: files, examples, references, data, credentials, tools, anything only the user has.
7. Edge cases and tradeoffs: speed versus polish, build versus buy, what happens on failure, what the user cares most about.
8. Autonomy and risk: what Claude may touch alone (install, delete, refactor, push), what needs approval, who signs off, what stops the run.
9. Assumptions and falsifiers: what you are assuming, and what would prove it wrong.
10. Delivery: files, PR, doc, deploy, how to resume across sessions.

### Per project type (add these on top of the checklist)

Software
- The job and trigger: who uses it, what they do today, what "better" means.
- MVP or production. Must-haves and non-goals.
- Existing stack and conventions (scan first). Required or banned libraries. Hosting, auth, data, scale numbers.
- Key flows, empty states, error states, accessibility, design references.
- Sensitive data and secrets.
- Test and run commands as the definition of done.
- May Claude install dependencies, refactor existing code, delete files, push, open PRs?
- Pre-mortem: what would make you delete this repo in three months?

Research and analysis
- The decision this informs and who makes it.
- The exact questions and sub-questions. Scope (time window, region, industries).
- Source rules: allowed types, recency, primary versus secondary, citation style.
- Hypotheses the user already holds. Tell Claude to test them, not confirm them.
- Deliverable: format, length, audience expertise, recommendation or neutral survey, confidence levels.
- Evidence threshold and how to treat conflicting sources or uncertainty.

Content and writing
- The reader and the one thing they should do after reading.
- Where it gets published, length, format.
- Voice shown by 2 or 3 real samples (the user's own writing or writing they admire), not just adjectives.
- Raw material only the user has: stories, numbers, names, quotes. Ask for three concrete specifics. Thin AI drafts come from missing user-specific detail.
- Must-include and must-not-say (legal, brand, claims).
- If the user has a writing-style skill or profile and the piece goes out under their name, use it.

Automation and agent workflows
- Trigger, frequency, volume.
- Real sample inputs, including messy ones, and where outputs go.
- What counts as correct. Examples of tricky cases.
- Failure behavior: retries, idempotency (a retry must not double-send or double-charge), alerts.
- Which actions need human approval (irreversible, money, external messages). Least-privilege access.
- Logging and how the user will know it broke. Cost and step limits.
- A dry run or shadow mode before going live. Scheduled and unattended runs cannot ask questions later, so the prompt must be complete up front.

### Probes that pay off (use one or two, not all)

- Unpack a vague adjective: "When you say clean, what would you point at?"
- The real job: after the user states a solution, ask "what is that for?" two or three levels deep, then stop.
- Last-time questions beat opinion questions: "Tell me about the last time this went wrong" beats "would you use this?"
- Non-goals: "What should I deliberately not touch or build?"
- Ambition dial: "Where do you want me to go beyond the literal request?" (minimal, solid, fully featured, surprise me with guardrails).
- Pre-mortem: "Imagine this failed in three months. What is the most likely reason?" The answer feeds constraints, edge cases and checks.

## Phase 4. Fill gaps with defaults, then flag them

For every open item you did not ask about, pick a safe default and write it in the assumptions ledger. Do not slip silent defaults into the work. The user must be able to see and veto them cheaply.

## Phase 5. Read back and get explicit approval

Write a compact brief (about 200 to 300 words):

- Problem and goal, in one or two sentences.
- Effort budget, must-haves, non-goals.
- How we will both know it is done.
- The assumptions ledger.
- Risks from the pre-mortem.

Ask the user to approve or edit. "Sounds good" in passing is not approval. If they say "whatever you think", give a recommendation and get an explicit yes.

## Phase 6. Write the final prompt

You are now writing instructions for another run of Claude (possibly a fresh session with no memory of this chat). Write for a smart newcomer who has zero context. Everything it needs must be on the page or reachable by file path.

### Writing rules

- Plain, direct wording. No ALL CAPS shouting, no "CRITICAL" or "MUST" piles. Piling on emphasis makes Claude over-apply the rule.
- Say what to do, not only what to avoid. When something must not happen, give the reason.
- Explain the why behind important constraints. Claude generalizes better when it knows the purpose.
- Reference files by path instead of pasting them. Only write down what Claude cannot find by reading.
- Use XML-style tags to separate sections (instructions, context, examples). Put long reference material first and the actual ask last.
- Include 3 to 5 examples where output format or voice matters, wrapped in example tags. Use diverse ones and add one anti-example when taste matters.
- One sentence for a role is enough.
- Do not hardcode model names or token budgets. Do not tell Claude to "think harder". Effort is a setting, not a sentence.
- Match the length of written deliverables to the need.

### Template for the generated prompt

```
<goal>
One or two sentences: what we are making and why. Include who it is for and what it lets them do.
</goal>

<context>
What already exists (paths to read first), prior decisions and why, audience, relevant constraints from the user's world.
</context>

<scope>
Must have: numbered, testable requirements.
Nice to have: ordered by value.
Non-goals: what to leave alone, with reasons.
Effort budget: prototype / solid v1 / production.
</scope>

<ambition>
Where to go beyond the literal request (for example empty and error states, accessibility, polish).
Anything outside this list that seems valuable: do not build it. Add it to a "Beyond the brief" list in the final report with a one-line reason each, capped at five.
</ambition>

<constraints>
Stack, patterns to follow, libraries to avoid, privacy and tone rules, and the reason behind each.
</constraints>

<definition_of_done>
The exact checks to run, the evidence to show (command plus output, screenshot, source links), and one measurable end state. Say what to do if a check cannot run: report which and why.
</definition_of_done>

<process>
Phases or milestones, and where to pause for approval. For long work: keep a progress file and a feature or task list in JSON (every item starts as not done), work one item at a time, commit after each, and read the progress file when resuming.
</process>

<autonomy>
Decide routine matters yourself and log the choice. Stop and ask only when you cannot go on without the user or before a risky step. Ask for approval before deleting files, pushing, spending money, messaging people, or changing shared systems.
</autonomy>

<grounding>
Before reporting progress, check each claim against a tool result from this session. If something was not verified, say so. Confirm that a package, API or fact exists before relying on it, and search for anything that may have changed since training.
</grounding>

<risks>
The top risks from the pre-mortem, each with the earliest warning sign.
</risks>

<assumptions>
The ledger: each assumption you made, so the user can veto it.
</assumptions>

<report>
Write for a reader who was not watching. Lead with the outcome. Then evidence, then the Beyond-the-brief list, then open questions.
</report>
```

Drop any tag that does not apply. A short task should get a short prompt.

Add these only when they apply:

- Independent parallel work exists: tell Claude it may use subagents, and require each brief to state an objective, output format, tools and boundaries. Do not use subagents just to double-check work.
- Open-ended exploration is wanted: ask for 3 to 4 distinct options first, wait for the user to pick one, then build only that one.
- A reviewer is wanted: use a fresh-context reviewer and the wording "report gaps that affect correctness, skip style, include severity and confidence".
- Unattended run: answer the five questions in the last section of this file before launch.

## Phase 7. Check the prompt before handing it over

Run this checklist on your own draft:

- Cold-reader test: would a smart colleague with no context be confused anywhere?
- Is every requirement testable? Could someone argue about what "done" means?
- Any two instructions that contradict each other?
- Did a vague adjective sneak in ("fast", "clean", "professional") without a concrete anchor?
- Does the scope say what to leave alone, not only what to build?
- Is there a way for Claude to check its own work?
- Are the assumptions visible?
- Is anything asked of Claude that it can already do or find by itself? Cut it.

Show the user the final prompt. Ask what is off, then fix it.

## Phase 8. Hand off

- Save the brief as SPEC.md and the prompt as PROMPT.md (or the names the user prefers) in the project.
- Recommend running the build in a fresh session so it starts with clean context and a written definition of done.
- For high-risk or very large work, suggest plan mode first so the user reviews the plan before anything is changed.
- Three failed corrections in a row means stop and rewrite the prompt, not patch the chat.

## Interviewer mistakes to avoid

- Asking what the files already say.
- Asking yes/no questions that change nothing.
- Asking ten things at once.
- Asking how before what and why.
- Hypotheticals ("would you use...").
- Re-asking something already answered.
- Silent defaults.
- Treating a casual "sounds good" as approval.
- Building anything before approval.
- Leading questions that pitch your favorite option.
- Padding the final prompt with generic advice Claude already follows.

## Five questions before any unattended run

Answer these in the prompt itself:

1. What does done look like?
2. How does Claude check its own work?
3. What may it touch?
4. What stops it?
5. Where does the result go and who signs off?

## Reusable clauses (copy as needed)

Scope control
> When the work the user asked for is done and checked, stop and report. Don't add features, tests, files, docs or refactors that weren't asked for. If you think one would help, mention it at the end instead of doing it.

Keep going, but ask when it matters
> Keep working until everything asked for is done, and only stop to ask when you can't go on without the user or before a risky step. Make routine judgment calls yourself and note them.

Ambition (when the user wants more than the minimum)
> Be ambitious about [list]. Include [empty and error states, the save flow wired up, keyboard navigation], not just the layout.

Ideas only
> When the user asks for ideas, options or a plan, give that and stop.

Honest progress reports
> Before reporting progress, audit each claim against a tool result from this session. If something is not yet verified, say so.

Tests are not to be bent
> Do not hardcode values to pass checks, and do not remove or edit tests to get green. If a check looks wrong, tell me instead of working around it.

Options before building
> Before building, propose three distinct directions in a few lines each. Wait for me to pick one, then implement only that direction.

Real verification
> Run a real check that exercises the change. A syntax check alone does not count. If no check can run, say which one and why.

## Quick Start block (paste into any chat)

```
I'm starting a big task and I want a prompt that gets Claude to build exactly what I mean.

First, silently scan whatever context you have (files, links, earlier messages). Do not build anything yet.
Then restate what you think I want, split into "inferred" and "could not infer".
Then interview me with the interactive question tool (3 to 4 multiple-choice questions per round, recommended option first, two rounds at most). Ask only what you cannot find yourself and what would change the result. Cover: why I want this, who it is for, scope and non-goals, how we both know it is done, constraints, what you may touch on your own, and where you may go beyond what I ask.
Then read back a short brief and wait for my approval.
Then write the final prompt as PROMPT.md with a definition of done, scope zones, an assumptions list and a "beyond the brief" section.

The task: [describe it in a few lines]
```
