# cld-prompt-generation

A playbook that makes Claude interview you before a big task, then write a prompt that actually captures what you mean (and leaves room for Claude to suggest something better).

I built this because starting a big project with one vague paragraph usually means Claude builds the wrong thing very confidently. So instead of writing the perfect prompt myself (hard), I let Claude scan my context, ask the questions that matter, and write the prompt with me.

## What's in here

- `PLAYBOOK.md`: the thing Claude reads. Triage, silent context scan, interview with a question checklist, read-back for approval, final prompt template, checks, handoff.
- `KNOWLEDGE.md`: the research behind every rule, with source links and a confidence tag on each finding (some sources were blocked while researching, and those are marked).

## How to use it

1. Start a new chat or Claude Code session in the project.
2. Say: `Read PLAYBOOK.md, then I want to build [thing].` (Or paste the Quick Start block at the bottom of the playbook.)
3. Claude scans what it can see, restates what it thinks you want, and asks a few multiple-choice questions.
4. You approve a short brief. Claude writes `PROMPT.md` (and `SPEC.md`).
5. Run the build in a fresh session with that prompt.

Works for software, research, content and automation. Small, clear tasks skip the interview on purpose.

## Status

v0.1, researched 2026-11-25. Well sourced, not yet battle-tested. If an interview goes badly (too many questions, wrong questions, a prompt that still missed the point), note what happened and tweak the playbook. That feedback is worth more than another round of research.
