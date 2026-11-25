# Knowledge Base: Why the Playbook Works

Research notes behind PLAYBOOK.md. Researched 2026-11-25 by four parallel research passes (official guidance, community workflows, interviewing technique, innovation and pitfalls).

Confidence tags:

- [Read] the page was fetched and read in full during this research.
- [Snippet] only a search excerpt was seen (the page or paper was blocked). Treat numbers as unverified until you check the source.
- [Recalled] came from memory and was not confirmed. Verify at the source before quoting.

Model behavior changes fast. Nothing here hardcodes a model name, and you should re-check the model-specific pages when you move to a newer model.

## 1. Anthropic documents an "interview me" pattern

- Claude Code best practices, "Brainstorm with Claude first": "Interview me until you could write the spec, then write it to SPEC.md." Claude comes back with multiple-choice questions about the parts you have not pinned down and keeps asking until the answers add up to a spec. Then start a fresh session to build from it, so the build begins with clean context and a written definition of done. [Read] https://code.claude.com/docs/en/best-practices
- A shorter docs template: "I want to build [x]. Interview me in detail using the AskUserQuestion tool." [Read] same page.
- AskUserQuestion mechanics: 1 to 4 questions per call, 2 to 4 options each (label plus description), multiSelect flag, header capped at 12 characters, free-text "Other". Most common in plan mode. Not available in subagents, so the main agent runs the interview. [Read] https://code.claude.com/docs/en/agent-sdk/user-input
- Auto mode discourages asking, so invoke the interview explicitly. [Read] Claude Code best practices.
- "Recommended option first" ordering is a convention, not documented behavior. [Read, absence of source]

Used in the playbook: Phases 3 to 8 follow this shape (ask, write a spec file, build clean).

## 2. Ask only what Claude cannot see, and only when it changes the work

- "Only write down what Claude can't find by reading the repo." Test: could it find this by reading the code, and would it actually go look? If both are yes, leave it out. [Read] Claude Code best practices.
- Opus 5 prompting page: check in only when different readings of the request would lead to materially different work. [Read] https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5
- Sonnet 5.5 page: only stop to ask when you cannot go on without the user or before a risky step. It names the failure of asking a question the model could answer itself (seen at low and medium effort). [Read] https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-sonnet-5-5
- The claude.ai chat default is to avoid more than one question per response. The playbook overrides that on purpose for big tasks. [Read] https://platform.claude.com/docs/en/release-notes/system-prompts/claude-sonnet-5-5

Research on the other side (models under-ask by default):

- Ambig-SWE: interaction on underspecified coding tasks gave gains of up to 74%, and models default to not asking unless told to. [Snippet] https://arxiv.org/abs/2502.13069
- ClarifyCoder reports that unneeded questions on clear tasks cut pass rates sharply (65% to 27% in one setup). [Snippet] https://arxiv.org/html/2504.16331v2
- INTENT-SIM: ask only when plausible user intents diverge, and count the interaction cost. [Snippet] https://aclanthology.org/2025.findings-naacl.306.pdf
- CLAMBER: models detect ambiguity but struggle to decide which one to resolve. A checklist of dimensions helps more than "ask if unclear". [Snippet] https://arxiv.org/abs/2405.12063
- "What Prompts Don't Say": models infer unstated requirements only around 41% of the time, and underspecified prompts regress more across model updates. [Snippet] https://arxiv.org/abs/2505.13360v1
- Another snippet-level claim says models ask clarifying questions in under 5% of ambiguous cases unprompted. [Snippet] arXiv 2605.25284

Takeaway: ask less than a nervous assistant would, but not zero. Use a fixed dimension checklist, then ask only about open, high-impact dimensions. This is why Phase 3 has both a checklist and stop rules.

There is no validated optimal question count. "3 to 4 per round, two rounds" is a practitioner convention that matches the tool's limits. [no source, flagged]

## 3. Practitioner workflows people actually use

- GitHub Spec Kit: constitution, specify, clarify, plan, tasks, implement. Specify covers what and why with no tech stack. Rules include "don't guess", a cap of 3 clarification markers, informed defaults recorded under Assumptions, and measurable success criteria. The clarify step scans 10 categories and asks at most 5 questions, one at a time, each with a recommended answer. [Read, raw repo files] https://github.com/github/spec-kit . Critics call it waterfall-like for small work. [Snippet]
- superpowers brainstorming skill: read the project before asking, ask only when the answer changes the build, otherwise state a default for the user to correct, play understanding back in 200 to 300 word chunks, and hard-gate building until the design is approved. Named anti-patterns: a menu of guessed wants, silent defaults, treating "sounds good" as approval. [Read] https://raw.githubusercontent.com/obra/superpowers/main/skills/brainstorming/SKILL.md
- Anthropic feature-dev plugin: seven phases with a critical clarifying-questions phase and an approval gate. If the user says "whatever you think", give a recommendation and get explicit confirmation. [Read]
- PRD-style skills: describe the observable pain, not the assumed need; ask "why now?"; write the job-to-be-done sentence; name who is not the target; list non-goals. Another uses 3 to 5 questions with lettered options so the user can reply "1A, 2C". [Read, GitHub skill files]
- Ralph Playbook: break the idea into jobs to be done, one spec per topic, test each topic with "can I describe it in one sentence without 'and'?", then a gap-analysis planning pass and one task per loop with fresh context. [Read]
- Harper Reed's workflow: idea honing by a one-question-at-a-time interview into spec.md, then a test-driven prompt plan and todo list, then step-by-step execution. Only the first line of his prompt ("Ask me one question at a time so we can develop a thorough, step-by-step spec for this idea.") was confirmed. The rest is [Recalled]; verify at the source.
- Thariq's interview prompt (secondhand): interview via AskUserQuestion on implementation, UI/UX, concerns and tradeoffs; ask non-obvious questions; keep going until complete; write the spec; execute in a new session. [Read as a gist, author attribution secondhand] https://gist.github.com/robzolkos/40b70ed2dd045603149c6b3eed4649ad
- Kiro-style requirements, design, tasks with approval at each phase. [Snippet]
- BMAD: described as a clarify, plan, build, verify, learn loop sized to the work. The older analyst, PM, architect flow is [Snippet].
- Boris Cherny reportedly starts in plan mode, iterates the plan, then lets Claude one-shot the work. Simon Willison: plan first, red/green TDD, do not submit code you have not read. [Snippet] Reddit and Hacker News threads were not readable from the research sandbox.
- Counterweight: asking four questions for a typo-size fix wastes time. The playbook's Phase 0 triage exists for that reason. [Snippet]

## 4. Interview technique from requirements work

All of these are [Snippet] unless noted. The mapping to prompts is this playbook's synthesis.

- Jobs-to-be-done and forces of progress: "When ___ I want ___ so I can ___", what they do today and what it costs, what worries them about relying on the new thing. https://jobstobedone.org/switch-interview/
- The Mom Test: ask about the last time something happened, ask for real samples, avoid opinion and "would you use" questions. https://blog.uxtweak.com/the-mom-test/
- 5 Whys: use two or three levels on a stated solution to reach the real goal. It assumes one linear cause, so stop early. https://www.imd.org/blog/strategy/the-5-whys-technique/
- Socratic questioning (six types): unpack vague words and probe assumptions. https://www.criticalthinking.org/TGS_files/SocraticQuestioning2006.pdf
- Pre-mortem: "it failed in three months, why?" Answers become constraints, edge cases and checks. https://www.psychologytoday.com/ie/blog/seeing-what-others-dont/202101/the-pre-mortem-method
- User stories and Given/When/Then acceptance criteria make behavior testable. https://www.atlassian.com/work-management/project-management/acceptance-criteria
- Shape Up pitch: appetite (effort budget), no-gos and rabbit holes, plus MoSCoW and explicit non-goals, to stop gold-plating. https://basecamp.com/shapeup/1.5-chapter-06
- Anthropic Interviewer (research tool): builds a flexible rubric, has a human review the plan, runs adaptive interviews, then returns answers with quotes. The playbook mirrors that: rubric from the scan, adaptive questions, echo-back for correction. [Read] https://www.anthropic.com/research/anthropic-interviewer
- Anthropic's page does not list what a good clarifying question looks like. The "good versus annoying question" rules in the playbook are a synthesis from the sources above. [flagged]

## 5. What a strong big-task prompt contains

- Be clear and direct. Treat Claude like a brilliant new employee who lacks context. Colleague test: show the prompt to a colleague with minimal context; if they would be confused, Claude will be too. [Read] https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices
- Give the reason behind rules. Use 3 to 5 relevant, diverse examples in tags. Use XML tags to separate instructions, context, examples and inputs. Put long documents first and the query last. One sentence for a role. [Read] same page.
- State what to do rather than only what not to do. [Read] same page.
- Success criteria first: start with a clear definition of success. [Read] https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview
- Context engineering: the smallest set of high-signal tokens, at "the right altitude" (not brittle if-else, not vague), with just-in-time loading through file paths and notes kept outside the window. [Read] https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- Prompt generators and improvers exist. The improver reported about +30% accuracy on one classification test. The metaprompt cookbook frames the target as "an eager, helpful, but inexperienced and unworldly AI assistant", puts long inputs before instructions, and says its output is not guaranteed optimal. [Read] https://claude.com/blog/prompt-improver , https://platform.claude.com/cookbook/misc-metaprompt
- Skill authoring guidance: keep the body under 500 lines, assume Claude is already smart, match specificity to how fragile the task is. The playbook keeps the long research out of PLAYBOOK.md for this reason. [Read] https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices
- Subagent briefs need an objective, an output format, guidance on tools and sources, and clear task boundaries, or agents duplicate work and leave gaps. [Read] https://www.anthropic.com/engineering/multi-agent-research-system

## 6. Getting innovation without drift

- Newer models read requests literally. Claude Code docs say Claude tends to build the smallest thing that satisfies a request, so say where you want more: "be ambitious: empty and error states, the save flow wired up, keyboard navigation, not just the layout." [Read] https://code.claude.com/docs/en/best-practices
- Explicit extras: "Include as many relevant features and interactions as possible. Go beyond the basics." [Read] best-practices page.
- Mention extras at the end instead of building them, and "when the user asks for ideas, options, or a plan, give them that and stop." [Read] Sonnet 5.5 page. This is the basis of the playbook's "Beyond the brief" list.
- Options before building: "Before building, propose 4 distinct visual directions... Ask the user to pick one, then implement only that direction." Generic negations ("don't use X") tend to swap in another fixed default, so give a concrete spec or have the model propose options. [Read] Sonnet 5 and Opus 4.8 pages under https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/
- Give the reason: "I'm working on [larger task] for [who]. They need [what it enables]. With that in mind: [request]." [Read] Fable 5 page.
- Pre-mortem as a prompt phase: frame the failure as already happened, ask for 5 to 8 project-specific reasons each with an earliest warning sign and a cheap check. Vague prompts give generic risks. [Read, a GitHub skill]
- Divergent then convergent: research support is real but modest. One paper reports 1.6 to 2.1 times more diversity from asking for several candidates, and explicit "think outside the box" cues help only a little. The reliable levers are options-then-choose, concrete specs and review loops. [Snippet]
- An ownership sentence ("you are the owner and long-term maintainer") raised consolidation of duplicated code in one test, with 70 to 80 percent more output tokens. Useful for refactors, costly otherwise. [Read] Fable 5.2 page.

## 7. Verification and "done"

- The top lever is a pass/fail check Claude can run. Ask for evidence (command plus output, or a screenshot), not "all tests pass". [Read] Claude Code best practices.
- A good goal condition has one measurable end state, a stated proof, constraints, and a turn cap. [Read] https://code.claude.com/docs/en/goal
- Tests first in a structured file (JSON, because models overwrite Markdown more readily), and never remove or edit tests. Anti-hardcoding wording lives in the prompting best practices page. [Read]
- Test end-to-end as a human user would, not just unit tests or curl. [Read] https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
- Ground progress reports: audit each claim against a tool result from this session. Reported to have nearly eliminated fabricated status reports on one model. [Read] Fable 5 page.
- Fresh-context reviewers beat self-critique. A reviewer told to find problems will always find something, so say "report gaps that affect correctness, skip style" and review against the spec. [Read]
- Over-verification trap: newer models already self-verify, so ritual "double-check" or "verify twice" adds cost. Name the specific check instead. One cap ("Don't start extra rounds of review or hardening on your own") cut cost by about a third in one report. [Read] Opus 5 and Sonnet 5.5 pages.
- Review prompts that say "only report high-severity" can lower recall. Ask for every issue with confidence and severity, and filter later. [Read] Sonnet 5 and Opus 4.8 pages.
- Evals: start with 20 to 50 tasks from real failures, grade the outcome rather than the path, read transcripts. [Read] https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents
- Rules that cannot have exceptions belong in hooks or deny rules, not prose. [Read] Claude Code best practices.

## 8. Long and unattended tasks

- Five questions before an unattended run: what done looks like, how it checks itself, what it may touch, what stops it, who signs off. "You find out the next morning that it spent the whole night building on the wrong assumption." Scheduled tasks cannot ask clarifying questions. [Read] Claude Code best practices.
- Front-load the whole spec in the first turn. Trickling in requirements wastes tokens on newer models. [Read] Sonnet 5, Opus 4.8 and Opus 5 pages.
- Long-running harness: an initializer pass builds init.sh, a progress file, a git commit and a JSON feature list (200+ items, all marked failing). Then one feature per session, commit, update progress. Named failures: trying to one-shot the app, declaring victory early, marking done without end-to-end tests. [Read] harness post above.
- Context: very large windows and auto-compaction reduce the need for aggressive management. Clearing between unrelated tasks still helps. Keep CLAUDE.md short. [Read] Claude Code best practices. Chroma's "context rot" report is background. [Snippet] https://research.trychroma.com/context-rot
- Subagents are worth it for parallel independent work, noisy output, and a fresh reviewer. Not for "a smarter answer". Multi-agent used roughly 15 times the tokens of a chat in one report. A known failure was spawning dozens of subagents for simple queries. [Read] multi-agent research system post and Claude Code docs.
- Effort is a setting, not a prompt phrase. At low or medium effort models stop early to check in. [Read] Sonnet 5.5 page.
- Say whether the user is watching. Autonomous-run wording: before ending a turn, if the last paragraph is a plan or a promise, do the work now. [Read] Fable 5 page.
- Write approvals as "ask for approval before deleting files", not "never delete files", because "never" is treated as absolute. [Read] Fable 5.2 page.

## 9. Pitfalls and what changed with newer models

- Over-engineering: use the anti-overengineering block ("The right amount of complexity is the minimum needed. Don't add features, refactor, or make 'improvements' beyond what was asked"). One 2026 study reports overeager changes rising from 0% to 17.1% when the explicit scope statement was removed. [Read the block; study is Snippet]
- Stopping early: "Keep working until everything the user asked for is done, and only stop to ask when you can't go on without the user or before a risky step." [Read]
- Overconfident completion and reward hacking (tampering with tests): reported by METR and others. [Snippet] Mitigation is in section 7.
- Hallucinated APIs or packages: require confirming that a package or fact exists. A study of earlier models found about 19.7% of suggested packages did not exist. [Snippet]
- Sycophancy: Anthropic reported 9% in personal-guidance conversations, 18% when users pushed back. That is not coding data. State hypotheses neutrally and ask for disagreement with evidence. [Read] https://www.anthropic.com/research/claude-personal-guidance
- Prompt bloat and shouting: soften "CRITICAL: You MUST" to plain wording. "IMPORTANT or ALL CAPS" is listed as outdated advice because emphasis makes Claude over-apply rules. Prompts written for older models can degrade output on newer ones. Do not ask the model to echo its reasoning. [Read] best practices and model pages.
- Thinking-out-loud messages should get an assessment, not edits. [Read] Fable 5 page.
- Verbose deliverables on newer models: add "match the length of written documents to what the task needs". [Read] Opus 5 page.
- Showing a token countdown can trigger early wrap-ups or injection suspicion. [Read] Fable 5 and Sonnet 5.5 pages.

## 10. Open questions and weak spots

- Paper figures marked [Snippet] came from search summaries because arXiv and ACL pages were blocked in the research sandbox. Re-check before citing.
- "ProactiveBench" was searched for as a named benchmark and not found. The closest matches are Ambig-SWE, QuestBench, CLAMBER, HumanAgencyBench and a proactive-information-gathering paper (arXiv 2507.21389).
- A small study claiming checklists beat open clarifying questions (7.5 vs 6.7 out of 8) has unclear provenance. Weak evidence. It is consistent with the playbook's checklist-then-residual-questions design.
- Reddit, Hacker News, Simon Willison's and Harper Reed's own pages could not be read. Their entries are secondhand.
- Whether plan mode helps or just hurries people is disputed in community write-ups only.
- No source gives a proven best number of questions or rounds.
- The playbook as a whole has not been tested against real tasks yet. Treat v0.1 as a well-sourced draft and improve it from real interviews (see the README).
