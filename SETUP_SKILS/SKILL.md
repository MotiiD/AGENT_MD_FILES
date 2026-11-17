---
name: project-docs
description: Interview the user and write a project's agent-ready documentation set (AGENTS.md plus docs/SPEC.md, TECH_STACK.md, CODE_STANDARDS.md, PROCESS.md, BRAND.md, STATUS.md, and on request a feature brief, a phased PLAN or a review). Use when starting a new project, continuing or onboarding an existing project, adding a new feature brief, or turning a code review into rules — any time the user wants "docs like the UnderCover project" or a brief → plan → implement → review workflow for a coding agent.
---

# Project docs

You write the documents that let a coding agent build a product the way a good team does:

- the customer's requirements, in the customer's voice
- fixed tech choices and strict code standards
- a process of **brief → plan (design + phases with checkbox criteria) → human review → implement phase by phase → review by a fresh subagent → commit**
- a status file that always says what is built and how to run it

Get every fact from the user or from the repo. Never invent one. If something is unknown, ask.

## 1. Pick the mode

If the request doesn't make it clear, ask which one:

- **New project**: write the base set. `STATUS.md` says "Nothing is built yet".
- **Continue a project**: read the code first, then write the base set or fill in what's missing, so it matches what is actually built.
- **New feature brief**: the base set exists. Write `docs/<NAME>.md` and add it to the builds table in `AGENTS.md`.
- **Review into rules**: write `docs/HUMAN_REVIEW_<NAME>.md` from the user's findings.
- **Plan**: write `docs/PLAN.md` or `docs/PLAN_<NAME>.md` (section 6). Write no code.

## 2. Read before you ask

- Read any existing `AGENTS.md`, `CLAUDE.md`, `README.md`, `.github/copilot-instructions.md` and `docs/`, plus any `.md` file the user attached. If the user attached one doc as the starting point, treat it as the source of facts and of style.
- In continue mode, also read the manifests (`package.json`, `pyproject.toml`, `requirements*.txt`, `go.mod`, `*.csproj`), the lint, test, CI and pre-commit configs, the top-level folders and their entry points, and `git log --oneline -20`.
- Note what you already know and where you found it. Ask only about the gaps, and confirm what you inferred.

## 3. Interview

- Ask **one question at a time**, with the ask-user tool if you have one. Give choices where you can, with your recommended answer first, marked "(Recommended)".
- Go document by document in the order below. After each one, recap what you'll write in 3–6 bullets and ask the user to confirm.
- Skip what the repo already answers, and say what you found ("I see FastAPI and SQLite in `pyproject.toml`. I'll use those.").
- If the user says "you decide", choose the simplest option that is usual for the stack and say so in the recap.
- Dig until each answer can be checked. A rule needs a number or a condition. "Done" needs a command, a request or a page you can check.

What to find out:

**SPEC (the business)**
- Who is asking (a role, such as "head of claims operations")? What does the company do?
- How the work is done today, step by step, and what goes wrong.
- What the product should do. For each thing: the input, the action and the output. The goal in one sentence.
- Is there an AI agent? What does it decide, read and change? What always needs a human?
- How work is organized: one item or a whole queue, order, caps for trials, retries on failure and who takes over after that, what must never happen twice.
- What the team needs to see: screens, states and their labels, live progress, read-only views.
- Rules with numbers (limits, approvals, currency). For each one, must the system **enforce** it even if the agent or the user tries to break it?
- The data: where it is, what describes it, and anything held out that the agent must never see (eval answers, secrets).
- Not for now: what's out of scope.

**TECH_STACK**
- The folders and, for each one, the language, framework and storage. Versions and package managers.
- API rules (for example: every operation has an `operation_id`, a one-line docstring and no optional inputs).
- For an agent: the framework, the model provider, the default model and the env var that overrides it, timeouts and retries, frameworks that are ruled out.
- Env var and secret names (in `.env`), ports and base URLs, test tools, and the OS (must it work on Windows?).

**CODE_STANDARDS**: propose these defaults and ask what to change or add:
1. Keep it simple. 2. Comments only when necessary. 3. Short README, no emojis. 4. No overly defensive code. 5. The package-manager rule (such as "ALWAYS `uv run`, NEVER `python3`"). 6. Strict typing (TypeScript strict, never `any`).
7. One way to do each thing: each service is reached only through one client module, with one function per operation, named after it, and the base URL and paths written once. 8. Each module has one job, never borrows another's helpers, no circular imports, and shared test helpers live in their own module. 9. Write a literal once. 10. Use the framework's own way before writing your own. 11. A function does one job. 12. When you extend code, bring it up to standard first.
For an agent: keep the parts apart (such as `api.py`, `tools.py`, `harness.py`), the backend enforces the business rules, and instructions give goals rather than scripted steps. Anything that must always happen, or happen in a fixed order, is done by code.

**PROCESS**
- Install, dev and seed or reset commands. The pre-commit hook.
- The one command that runs **every** check (lint, format, types, unit tests with a coverage threshold, end-to-end tests).
- Confirm the fixed flow in section 5, and the rules: tests never call a real model (use a stub), never `--no-verify`, never lower a threshold to get green, never push, data the agent must not open, and limits on live runs.

**BRAND** (only if there is a UI)
- Fonts for headings and body, an accent font and a monospace font. Five colors with hex values and their text colors, plus the ink on light and dark backgrounds. Which colors to use for states side by side. Light and dark surfaces. Panel style. What to avoid. Tone, spelling, and how to show money and dates. If there is no brand yet, offer to propose one.

**AGENTS.md**
- Who the coding agent works with, and why. Is anyone learning from the build, so plans should explain their reasoning?
- The roadmap of builds, in order, with each brief's file name and what it adds. A briefs folder copied into `docs/`?
- Every top-level folder, with a line on each. Repo rules: never build ahead, never create cloud resources unless asked, branches, reference builds.

## 4. The documents to write

Keep each fact in one document and point to it from the others. Use the headings below, and leave out a section only when it doesn't apply.

**`AGENTS.md`** (to the coding agent, as "you")
`# <Project>` · `## The context` (who you're working with, and the OS and its shell) · `## <Project>` (the product in 2–3 sentences) · `### Start here` ("IMPORTANT: before you plan, build or change anything, read each of these files in full:", followed by a numbered list of SPEC, TECH_STACK, CODE_STANDARDS, PROCESS, BRAND, STATUS and PLAN with a line on each) · `### Your role in the build` (the docs are the customer's requirements, the user directs and reviews, you plan, build and check as PROCESS describes) · `### The builds` (the two steps "Please plan <work> in docs/<NAME>.md" → `docs/PLAN_<NAME>.md`, then "Please implement the plan for <work>"; a table of Build | Brief | Copied to | What it adds; build only what `docs/` asks for, never ahead) · `### Folders` · any other context, such as branches and guides.

**`docs/SPEC.md`** (signed by a person at the customer: "From the <role> at <company>.", written as "we" and "our")
`# <First build>` · `## How <the work> works today` · `## <The first build>` (we want X to do the work the way a <role> does; for each item it should: ... ; our goal is ...) · `## <How it organizes its work>` · `## What the team needs to see` · `## Rules` (each with a number, and "the system must ..., even if ...") · `## Our records` (the files in `data/`, and what's held out) · `## Not for now`.

**`docs/TECH_STACK.md`** (about 10 lines)
A bullet for each folder: its tech and its key rules, the default model and the env var that overrides it, and base URLs. Then one line with the languages, versions, package managers, the manifest and the secret names in `.env`.

**`docs/CODE_STANDARDS.md`**
`## General`, `## Structure`, `## <The agent or another core part>`, with one numbered list running through all three. 10–20 rules.

**`docs/PROCESS.md`** (section 5)

**`docs/BRAND.md`**
`## Fonts` · `## Colors` (**Role**: Name `#hex`, text color) · `## Surfaces` (light and dark) · `## Avoid` · `## Tone`.

**`docs/STATUS.md`**
`# Status` (which plans are built and how many phases of each are done) · `## Running it` (the commands) · `## Built` (each folder: its operations, states and pages) · `## Review of ...` (when there was one: what it found, why the checks missed it, and what the standards and the process now require) · `## Last live run` (date, command, model, time and results).

**`docs/<NAME>.md`**, a feature brief (from a stakeholder)
`# <Title>` · "From the <role>." · `## Where we're heading` (what's changing, and what's wrong today, with a pointer to STATUS) · `## What we want` (numbered, each with a **bold title**, then the behavior, why it matters and what the team sees) · `## What stays the same` (the SPEC rules, the stack, the standards) · `## What done looks like` (the check is green with tests for each new behavior, a live run within limits, and SPEC, TECH_STACK and STATUS updated) · `## Planning` ("Follow `docs/PROCESS.md`, but write this plan to `docs/PLAN_<NAME>.md`", plus the limits on live runs).

**`docs/HUMAN_REVIEW_<NAME>.md`**
A context line, then the numbered findings, each with files, counts and what it should be instead. Then "Please follow these steps": confirm the issues and find out how they got through; fix them; look for the same problems in the other parts; retest; update PROCESS, CODE_STANDARDS and STATUS so it never repeats, and report how.

## 5. The process to write into PROCESS.md

Write this flow into every project, with the project's own commands:

- **Top**: the install command (and the pre-commit hook), the dev command, and the one check command with everything it runs (lint, format, types, unit tests with N% coverage, end-to-end tests).
- **Planning**: when asked to plan, write the plan to `docs/PLAN.md` (or `docs/PLAN_<NAME>.md` for a brief) and write no code. Start with the design: the modules, what each one is for and how they depend on each other, checked against CODE_STANDARDS. Describe agent instructions by their goals. Then 5–6 phases, each building on the last, each with detailed checkbox success criteria the agent can verify alone: tests, commands and their output, API calls and their responses, and browser checks. Then stop and wait.
- **Implementing**: when asked, build every phase in order without stopping. For each one: build it with tests → check every criterion and fix → review → tick off the criteria in the plan and update STATUS → commit (the hook runs the checks). At the end, review the whole branch against main, fix, run the full check, and report what the reviews found and what changed.
- **Reviewing**: the checks show the code works, not that it's fit to merge. Before every commit, a fresh subagent that didn't write the code reviews the diff against CODE_STANDARDS as a strict senior engineer deciding whether to merge the PR. Fix everything it would block the PR for. Report any disagreement instead of skipping it quietly.
- **Rules**: add tests with each change; tests never call a real model; outside a plan, commit each change once it's checked and reviewed; run the full check before handing work over; never `--no-verify`; never lower a threshold, turn off a rule or exclude a file to get green (stop and ask instead); keep STATUS up to date; never push; don't open held-out data; respect the platform rule and the live-run limits.

## 6. Writing a plan (when asked)

1. Read every document in `AGENTS.md`'s read-first list in full.
2. Start with a one-paragraph summary: what the plan builds on, the number of phases, and that each phase leaves the check green and is committed. Then a paragraph on what works when it's done.
3. `## Design`: the flow, a states table (State | Meaning | Label on screen | Color), the database, an API table (Operation | Route | What it does) with "Rules the backend enforces" and the status codes, the agent (model, exact tools, failure handling, the report line, the CLI), the instructions as goals, the frontend, the end-to-end tests, and how the criteria are checked.
4. `## Phase N: <name>`: a paragraph on what it builds, then `- [ ]` criteria with exact commands and expected output (such as `curl -s ... returns ...`, `pytest covers ... including each 404, 409 and 422`, `Browser, /route: ...`, `grep -rn <forbidden> <dirs> finds nothing`, `Live: after reseeding, ...`). End each phase with `- [ ] <check command> passes`.
5. Stop. No code until asked.

## 7. Style

- Plain, short, specific, active sentences. Use the spelling the user prefers. No emojis, exclamation marks, marketing words or hedging.
- Name the real thing: commands, routes, env vars, file names and numbers, in backticks.
- Use numbers, not adjectives ("80% coverage", "5 retries", "every two seconds").
- Use bullets for rules, numbered lists for ordered steps and numbered standards, and tables for states, operations and roadmaps.
- No `TODO`s or `<placeholders>` left in the output. Ask instead.

## 8. Finish

- Check the set against itself: every file in the read-first list exists, every command matches a real or planned script, every folder appears in both TECH_STACK and AGENTS, and nothing contradicts anything else.
- In continue mode, merge into the existing docs and show what changed. Never overwrite them blindly.
- List the files you wrote, with a line on each, then the next step: "Please plan <the first build>" (or "Please plan <work> in docs/<NAME>.md"), then, after the review, "Please implement the plan for <the work>".
