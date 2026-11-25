---
name: bigtask
description: Use whenever the user writes "/bigtask" or "bigtask", or says they are starting a big project or task (an app, a research report, a content series, an automation, anything large or ambiguous) and wants help with the prompt. Asks a few open-ended, voice-friendly questions, fills the gaps itself, and writes one complete prompt before anything gets built. Works in chats, projects and Claude Code.
---

# /bigtask

The user is about to start something big and wants help turning a rough idea into a prompt that gets built right the first time. They often answer by voice dictation, so keep questions open-ended and few. Do the heavy lifting yourself.

This works anywhere: a plain chat, a project, or Claude Code. Use whatever context exists where you are, and do not assume files or folders are there.

## Rule zero

Do not build, code, or start the work during this skill. The output is a prompt (and a short assumptions list), nothing else. If the user asks for ideas or options, give those and stop.

## Step 1. Read what you can (silently)

If the user typed something after /bigtask, treat it as the opening description. Then look at whatever context you already have: the conversation so far, attached files, project knowledge, saved memory about the user, and (in Claude Code) the working folder, README or CLAUDE.md. Do not ask about anything you can read or already know.

If the task turns out to be small and clear, say so in one line and just do it. This skill is for big, ambiguous or expensive-to-redo work.

## Step 2. One open-ended message, then stop and wait

Send a single message. Plain text, no multiple-choice, no question tool (the user is probably dictating). Say they can ramble, answer in any order, and skip whatever does not matter. Ask only what you could not infer from Step 1, using at most these four prompts, reworded to fit:

1. What are you trying to make or get done, and why does it matter to you?
2. Who is it for, and what does great look like when it is finished?
3. Anything I should know: things you have already tried, things you do not want, examples you like, limits like time, budget or tools.
4. How much freedom do I get: stick close to what you said, or go beyond it and surprise you?

Keep the whole message short enough to read aloud. If the user's opening description already answered one of these, drop it. If it answered all of them, skip to Step 3.

## Step 3. Fill the gaps yourself

Do not ask granular follow-ups. Use your knowledge of the domain and the scan to decide the rest: stack, structure, format, edge cases, sensible quality checks. Walk the checklist in prompt-template.md mentally and pick safe, reversible defaults for anything still open.

Write each default you chose into an assumptions list so the user can veto it cheaply.

Ask a follow-up only if two readings of the request would lead to materially different work and you cannot pick safely. Then ask one short open-ended question, once.

## Step 4. Write the prompt

Read prompt-template.md and use its structure. The prompt must work for a fresh Claude session with zero memory of this chat. Plain wording, no shouting, reasons attached to constraints, file paths instead of pasted content, a concrete definition of done that Claude can check by running something, and a "beyond the brief" section where extras get listed instead of silently built (only expand scope inside areas the user said you may).

Add one or two ideas of your own that would make the result better, as clearly labeled suggestions the user can accept or ignore. This is where you innovate, without changing the brief.

## Step 5. Show it, then hand off

Present:

1. The prompt, in a code block they can copy.
2. Assumptions I made (a short list).
3. Suggestions beyond what you asked (one to three).

Then ask one thing: "Does this match what you meant, or what is off?" Fix anything they point out.

When they approve, say how to start: paste the prompt into a fresh chat (or a new project) so it begins with clean context, or run it right here if they prefer. If file tools are available, offer to save it as PROMPT.md in the project. For risky or very large work in Claude Code, suggest plan mode first. Do not start building unless they say to.

## Reference

prompt-template.md has the prompt structure, the gap-filling checklist, and reusable clauses. Read it at Step 3.
