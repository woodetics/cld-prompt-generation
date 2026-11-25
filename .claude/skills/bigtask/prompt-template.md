# Prompt template and gap-filling checklist

Used by the bigtask skill. Condensed from a researched playbook on Anthropic's prompting guidance and community practice.

## Gap-filling checklist (decide silently, flag as assumptions)

Go through these after the user has spoken. Use what they said, what you scanned, and domain knowledge. Ask nothing here unless two readings lead to materially different work.

1. Outcome and why: what changes for the user when this works.
2. Audience: who uses the result and how skilled they are.
3. Scope: must-haves, nice-to-haves, non-goals, effort level (prototype, solid v1, production).
4. Done and how to check it: a command, test, screenshot or rubric Claude can run or apply.
5. Constraints: stack, patterns already in the project, privacy, tone, budget, deadline.
6. Inputs: files, data, references, access the work depends on.
7. Edge cases and failure behavior: empty states, errors, retries, what happens when it goes wrong.
8. Autonomy and risk: what Claude may do alone, what needs approval (deleting, pushing, spending, messaging people).
9. Biggest risk: imagine it failed in three months. What is the likely reason? Turn it into a constraint or a check.
10. Delivery: where the result goes, how to resume across sessions.

Project-type nudges:

- Software: follow existing conventions, run the real tests, no new dependencies without a reason, wire up empty and error states.
- Research: name the decision it informs, set source rules, test the user's hypotheses instead of confirming them, give confidence levels, search for anything that may have changed.
- Content: pin down the reader and the action they should take, use the user's own samples for voice, ask for concrete specifics only they know (numbers, stories, names), and use their writing-style skill if they have one.
- Automation: define the trigger, use real messy sample inputs, make retries safe (no double-sends), require approval for irreversible actions, add logging and a dry run before going live.

## Prompt structure

Drop any section that does not apply. A short task gets a short prompt.

```
<goal>
One or two sentences: what we are making, who it is for, and what it lets them do.
</goal>

<context>
What already exists (paths to read first), decisions already made and why, anything Claude cannot discover on its own.
</context>

<scope>
Must have: numbered, testable requirements.
Nice to have: ordered by value.
Non-goals: what to leave alone, with reasons.
Effort level: prototype / solid v1 / production.
</scope>

<ambition>
Where to go beyond the literal request (for example empty and error states, polish).
Anything valuable outside this list: do not build it. Put it in a "Beyond the brief" list in the final report, one line each, at most five.
</ambition>

<constraints>
Stack, patterns to follow, things to avoid, with the reason behind each.
</constraints>

<definition_of_done>
The exact checks to run and the evidence to show (command plus output, screenshot, source links). If a check cannot run, say which and why.
</definition_of_done>

<process>
Milestones and where to pause for approval. For long work: keep a progress file and a task list (every item starts not done), work one item at a time, commit after each, read the progress file when resuming.
</process>

<autonomy>
Make routine decisions yourself and log them. Stop and ask only when you cannot go on without the user or before a risky step. Ask for approval before deleting files, pushing, spending money, messaging people, or changing shared systems.
</autonomy>

<grounding>
Before reporting progress, check each claim against a tool result from this session and say what was not verified. Confirm that a package, API or fact exists before relying on it.
</grounding>

<assumptions>
Defaults chosen where the user did not specify, so they can be vetoed.
</assumptions>

<report>
Write for someone who was not watching. Lead with the outcome, then evidence, then the Beyond-the-brief list, then open questions.
</report>
```

## Writing rules for the generated prompt

- Plain, direct wording. No capital-letter shouting or piles of MUST. Emphasis makes Claude over-apply rules.
- Say what to do, not only what to avoid. Give the reason behind each constraint.
- Reference files by path. Only write what Claude cannot find by reading.
- Long reference material first, the actual ask last. Use tags to separate sections.
- Two to five examples when format or voice matters, plus one anti-example if taste matters.
- Do not hardcode model names. Do not tell Claude to "think harder".
- Cold-reader test before showing it: would a smart colleague with no context be confused? Is every requirement testable? Any contradictions? Any vague adjective without a concrete anchor?

## Add only when they apply

- Parallel independent work: allow subagents, and require each brief to state an objective, output format, tools and boundaries. Do not use subagents just to double-check.
- Open-ended design or exploration: "Before building, propose three distinct directions in a few lines each. Wait for me to pick one, then implement only that one."
- Unattended or scheduled run: the prompt must answer what done looks like, how Claude checks itself, what it may touch, what stops it, and who signs off. It cannot ask questions later.
- Review wanted: fresh-context reviewer, "report gaps that affect correctness, skip style, include severity and confidence".

## Reusable clauses

- Scope: "When the work asked for is done and checked, stop and report. Don't add features, tests, files, docs or refactors that weren't asked for. If one would help, mention it at the end instead of doing it."
- Persistence: "Keep working until everything asked for is done, and only stop to ask when you can't go on without the user or before a risky step."
- Ambition: "Be ambitious about [list], not just the minimum."
- Ideas only: "When asked for ideas, options or a plan, give that and stop."
- Honest reports: "Before reporting progress, audit each claim against a tool result from this session. If something is not verified, say so."
- Tests: "Do not hardcode values to pass checks or edit tests to get green. If a check looks wrong, tell me."
- Real checks: "Run a check that exercises the change. A syntax check alone does not count."
