# project-docs: a skill for spec-driven projects

`project-docs` sets up a project the way the UnderCover course project is set up. It interviews you one question at a time, then writes the documents a coding agent needs to plan and build the project:

| File | What it holds |
|---|---|
| `AGENTS.md` | Instructions for the coding agent: what to read first, its role, the planned builds and the folders |
| `docs/SPEC.md` | What the business needs, in the customer's words, with the rules and what's out of scope |
| `docs/TECH_STACK.md` | Each folder and its tech, versions, environment variables and models |
| `docs/CODE_STANDARDS.md` | Numbered coding rules |
| `docs/PROCESS.md` | How to install, run and check the project, and how to plan, build and review |
| `docs/BRAND.md` | Fonts, colors and tone (only if there is a UI) |
| `docs/STATUS.md` | What is built and how to run it |

When you ask for them, it also writes a feature brief (`docs/<NAME>.md`), a plan (`docs/PLAN_<NAME>.md`) or a review (`docs/HUMAN_REVIEW_<NAME>.md`).

## The files

- `SKILL.md`: the skill itself. All the logic is in this one file: the modes, the interview, how each document is laid out, the process, the plan format and the style.
- `project-docs.prompt.md`: a short prompt that starts the skill. Use it as is in GitHub Copilot in VS Code. Anywhere else, paste the text below its `---` header.

## How the work goes

1. **Docs.** Start the skill with one of these modes:
   - `new`: a new project
   - `continue`: an existing project
   - `brief <NAME>`: the next feature
   - `review <NAME>`: turn a code review into rules

   It reads the repo first, then asks only what it still needs to know.
2. **Plan.** Ask "Please plan <the first build>". The agent writes `docs/PLAN.md`: a design, then 5–6 phases, each with checkbox success criteria. It writes no code.
3. **Review.** Read the plan and ask for changes.
4. **Implement.** Ask "Please implement the plan for <the first build>". The agent builds each phase, checks its criteria, has the code reviewed by a fresh subagent, updates `STATUS.md` and commits.
5. **Next feature.** Run the skill in `brief <NAME>` mode, then "Please plan <the work> in docs/<NAME>.md", and go back to step 3.

## Claude Code (VS Code extension or desktop app)

Claude Code reads skills from `~/.claude/skills/` for every project, or from `.claude/skills/` for one project.

**Install for every project (Windows PowerShell):**

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\project-docs" | Out-Null
Copy-Item .\SKILL.md "$env:USERPROFILE\.claude\skills\project-docs\SKILL.md"
```

**Install for one project only:** copy `SKILL.md` to `<project>\.claude\skills\project-docs\SKILL.md`.

**Use it:**

- Open the project folder and start a new Claude Code session.
- Type `/project-docs new`, or `/project-docs continue`. You can also just ask "Use the project-docs skill to set up docs for this project".
- To start from an existing doc, mention it: `/project-docs continue, start from @AGENTS.md`.

**Important:** Claude Code reads `CLAUDE.md`, not `AGENTS.md`. Once the docs exist, create a `CLAUDE.md` at the project root containing this one line:

```
@AGENTS.md
```

## Claude CLI (Claude Code in the terminal)

The CLI uses the same skill folders as Claude Code, so the install above covers both. On Mac or Linux:

```bash
mkdir -p ~/.claude/skills/project-docs
cp SKILL.md ~/.claude/skills/project-docs/SKILL.md
```

**Use it:**

```bash
cd my-project
claude
```

Then, in the session:

```
/project-docs new
```

- Run it interactively, not with `claude -p`, because the skill asks you questions and waits for your answers.
- Check that the skill is installed by asking "What skills do you have?".
- Add `CLAUDE.md` with `@AGENTS.md`, as described for Claude Code.

## Claude on the web (claude.ai)

Claude on the web can't see your files, so it gives you the documents to copy into your project. Use either option.

**Option A: upload it as a skill.** Needs a paid plan with code execution turned on.

1. Make a folder named `project-docs` with `SKILL.md` inside, and zip the folder:
   ```powershell
   New-Item -ItemType Directory -Force .\project-docs | Out-Null
   Copy-Item .\SKILL.md .\project-docs\SKILL.md
   Compress-Archive .\project-docs .\project-docs.zip -Force
   ```
2. In claude.ai, go to **Settings → Capabilities → Skills**, upload `project-docs.zip` and turn it on.
3. In a new chat, write "Use the project-docs skill, mode: new".

**Option B: use a Project.**

1. Create a Project and add `SKILL.md` to its knowledge.
2. In the project instructions, paste: "Follow SKILL.md. Ask one question at a time. At the end, give each document as a separate markdown file."
3. Start a chat: "Mode: new. My project is ...".

**For an existing project:** attach your `README.md`, `AGENTS.md` or the manifests (`package.json`, `pyproject.toml`), and paste the folder tree, so Claude can read them.

**When it's done:** download each file and save it in your project, keeping the paths it gives (`AGENTS.md` at the root, the rest in `docs/`). Plan and build with Claude Code or Copilot, which can edit your files and run commands.

## GitHub Copilot in VS Code

VS Code finds skills in `~/.copilot/skills/` (personal) or `.github/skills/` (project), and prompt files in the user prompts folder or `.github/prompts/`.

**Install for every project (Windows PowerShell):**

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.copilot\skills\project-docs" | Out-Null
Copy-Item .\SKILL.md "$env:USERPROFILE\.copilot\skills\project-docs\SKILL.md"
New-Item -ItemType Directory -Force "$env:APPDATA\Code\User\prompts" | Out-Null
Copy-Item .\project-docs.prompt.md "$env:APPDATA\Code\User\prompts\project-docs.prompt.md"
```

This is already installed on this machine.

**Install for one project only:** copy the skill to `<project>\.github\skills\project-docs\SKILL.md` and the prompt to `<project>\.github\prompts\project-docs.prompt.md`. Commit both and your whole team can use them.

**Use it:**

1. Open the project folder in VS Code, then open Chat and pick **Agent** mode.
2. Type `/project-docs` and choose either the prompt or the skill. Give the mode when it asks (`new`, `continue`, `brief HARNESS`, ...).
3. To start from an existing doc, attach it with **Add Context** or `#file:AGENTS.md`.
4. If `/project-docs` doesn't appear, run **Chat: Open Customizations** from the Command Palette and check **Skills** and **Prompts**.

Copilot reads `AGENTS.md` by itself, so you don't need a `CLAUDE.md`.

## GitHub Copilot CLI

The Copilot CLI reads skills from `~/.copilot/skills/` and from `.github/skills/` in the repo, so the VS Code install above covers it too. It doesn't use `.prompt.md` files.

**Use it:**

```powershell
cd my-project
copilot
```

Then, in the session:

- Type `/skills` to check that `project-docs` is listed.
- Write "Use the project-docs skill, mode: new", or paste the body of `project-docs.prompt.md` with the mode filled in.

The CLI reads `AGENTS.md` by itself.

## Tips

- **One question at a time.** If the agent fires off a long list of questions, say "one question at a time, with choices".
- **"You decide"** is a fine answer. The agent picks the simplest usual option and tells you what it chose in its summary.
- **Continue mode** merges into the docs you already have. Check what it changed before you commit.
- **Keep one source.** Edit `SKILL.md` in this folder, then run the install commands again, so every tool gets the same version.
- **In a repo,** commit `AGENTS.md`, `docs/`, and `.github/skills/` or `.claude/skills/` if you use project installs. Plans and briefs are part of the project's history.
