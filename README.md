# cld-prompt-generation

A playbook that makes Claude interview you before a big task, then write a prompt that actually captures what you mean (and leaves room for Claude to suggest something better).

I built this because starting a big project with one vague paragraph usually means Claude builds the wrong thing very confidently. So instead of writing the perfect prompt myself (hard), I let Claude scan my context, ask the questions that matter, and write the prompt with me.

## What's in here

- `.claude/skills/bigtask/`: the `/bigtask` skill (light, voice-friendly). `dist/bigtask.zip` is the same skill packaged for upload.
- `PLAYBOOK.md`: the thing Claude reads. Triage, silent context scan, interview with a question checklist, read-back for approval, final prompt template, checks, handoff.
- `KNOWLEDGE.md`: the research behind every rule, with source links and a confidence tag on each finding (some sources were blocked while researching, and those are marked).

## The /bigtask skill (the quick version)

If you don't want a long questionnaire, use the skill. Type `/bigtask` (add a line about what you're doing if you like). Claude reads what it can see, then sends ONE open-ended message with up to four broad prompts. You can answer by voice and ramble in any order. Claude fills in the gaps itself, writes the full prompt, lists the assumptions it made, and suggests a couple of ideas beyond what you asked. It builds nothing until you say so.

Install it:

- Claude Code, this repo: it's already at `.claude/skills/bigtask/`.
- Claude Code, every project: copy that folder to `~/.claude/skills/bigtask/`.
- Claude app (claude.ai), for ALL your chats: upload `dist/bigtask.zip` in your skills settings (Settings, then Capabilities or Customize, then Skills; the menu name moves around) and switch it on. After that it's available in every new chat. Say "/bigtask" or "bigtask" plus a line about what you're starting. In a plain chat it can't see your files, so it works from the conversation, attachments and project knowledge.

## The full playbook (the thorough version)

Use `PLAYBOOK.md` when the task is high-stakes or you want the detailed, multiple-choice interview.

## How to use the playbook

1. Start a new chat or Claude Code session in the project.
2. Say: `Read PLAYBOOK.md, then I want to build [thing].` (Or paste the Quick Start block at the bottom of the playbook.)
3. Claude scans what it can see, restates what it thinks you want, and asks a few multiple-choice questions.
4. You approve a short brief. Claude writes `PROMPT.md` (and `SPEC.md`).
5. Run the build in a fresh session with that prompt.

Works for software, research, content and automation. Small, clear tasks skip the interview on purpose.

## Status

v0.1, researched 2026-11-25. Well sourced, not yet battle-tested. If an interview goes badly (too many questions, wrong questions, a prompt that still missed the point), note what happened and tweak the playbook. That feedback is worth more than another round of research.
