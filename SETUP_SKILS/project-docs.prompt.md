---
description: Interview me and write (or update) the project's AGENTS.md and docs/ set for brief → plan → implement → review development
argument-hint: new | continue | brief <NAME> | review <NAME> | plan <NAME>
agent: agent
---

Use the `project-docs` skill. If it isn't available, follow the rules below.

Mode: ${input:mode:new | continue | brief <NAME> | review <NAME> | plan <NAME>}

1. Read everything that already exists first: `AGENTS.md`, `README.md`, `docs/`, the manifests, the configs and any `.md` file I attached. If I attached a doc, use it as the source of facts and of style.
2. Tell me in a few bullets what you already know, and where it came from.
3. Interview me **one question at a time**, with choices and your recommended answer first. Go in this order: the business (SPEC), the tech stack, the code standards, the process and its commands, the brand (only if there's a UI), and then AGENTS.md (my role, the roadmap of builds and the folders). Skip what the repo already answers. After each document, recap what you'll write and get my confirmation.
4. Write the files:
   - `AGENTS.md`, with a read-first list, your role, the builds table and the folders
   - `docs/SPEC.md`, in the customer's voice, with the rules the system must enforce and what's not for now
   - `docs/TECH_STACK.md`
   - `docs/CODE_STANDARDS.md`, as numbered rules
   - `docs/PROCESS.md`, with the plan → implement → fresh-subagent review → commit flow and one check command
   - `docs/BRAND.md`
   - `docs/STATUS.md`

   Write a brief, a review or a plan only in that mode. Leave no placeholders. In continue mode, merge into the existing docs rather than overwrite them.
5. Check that the docs agree with each other, list the files you wrote, and tell me the next command, such as "Please plan <the first build>".

Write no product code.
