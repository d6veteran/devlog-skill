---
name: devlog
description: Maintain a structured development changelog (DEVLOG.md) that captures architectural decisions, progress milestones, key conversation takeaways, and strategic/business decisions for any project. Use this skill whenever the user says "log this", "devlog", "update the devlog", "push devlog", or references logging a decision, milestone, or takeaway. Also trigger proactively at the end of conversations that involve significant architectural choices, design changes, strategic pivots, or completed milestones — suggest a devlog entry even if the user doesn't ask. Trigger whenever the user mentions changelog, development log, decision log, or progress tracking for their project. Works across any project — not tied to a specific repo or product.
---

# Development Changelog (Devlog) Skill

This skill maintains a living development log (`DEVLOG.md`) in the root of a project's Git repository. It captures the important decisions, progress, and insights from development conversations so that future sessions have full context on what was done and why.

This skill is **project-agnostic** — it works with any repository. On first use in a session, it identifies the active project and adapts accordingly.

## Why This Matters

Development conversations contain critical context — the *reasoning* behind decisions, alternatives that were considered and rejected, and strategic pivots that shaped the project. Without a structured log, this context evaporates between sessions. The devlog serves as institutional memory for the project, making every future conversation more informed.

## Project Context (First Use Per Session)

The first time this skill triggers in a session, establish the project context by checking for clues in this order:

1. **Memory/conversation context** — Does Claude already know what project the user is working on? If so, confirm: "I'll log this to the devlog for [Project Name] — correct?"
2. **Project knowledge** — Are there project files or documents that identify the project name and repo?
3. **Ask the user** — If neither source provides clarity, ask: "Which project should I log this to? I'll need the project name and Git repo URL."

Capture and remember for the session:
- **Project name** — Used in the DEVLOG.md header and commit messages (e.g., "The Chronicle", "My Portfolio Site")
- **Git repo URL** — Where to push (e.g., `https://github.com/user/repo.git` or `https://gitlab.com/user/repo.git`)
- **Authentication** — If git is configured with a credential helper (e.g., macOS Keychain), no token is needed — git handles it transparently. Only ask for a Personal Access Token if a push fails with an auth error.
- **Default branch** — Usually `main`, but confirm if unsure

## Entry Format

Each entry follows this structure:

```markdown
## [YYYY-MM-DD] Brief descriptive title

**Category:** `feature` | `bugfix` | `refactor` | `infrastructure` | `security` | `milestone` | `strategy`
**Tags:** `relevant`, `topic`, `tags`
**Risk Level:** `low` | `med` | `high`
**Breaking Change:** `yes` | `no`

### Summary
A 1-2 sentence overview of what happened or was decided.

### Detail
Full context including:
- What was decided or accomplished
- Why this approach was chosen
- Dependencies or implications for future work
- Any open questions or follow-ups

### Decisions Made
Explicit trade-off decisions recorded here — what options were evaluated, what was chosen, and why alternatives were rejected.

### Related
- Links to related entries, documents, or resources when applicable
```

## Categories Explained

Choose the category that best fits each entry:

- **`feature`** — New capability or user-facing functionality added to the project.

- **`bugfix`** — A defect was identified and resolved.

- **`refactor`** — Internal restructuring with no behavior change (code quality, naming, structure). Use this for what was previously called `architecture`.

- **`infrastructure`** — Build system, CI/CD, deployment, tooling, or dependency changes.

- **`security`** — Security hardening, vulnerability fixes, auth changes, secrets management, access control.

- **`milestone`** — Completed phase, release, or significant deliverable. The "what we shipped" entries.

- **`strategy`** — Business decisions, market positioning, pricing changes, go-to-market pivots, competitive analysis conclusions. The "why we're doing it this way" entries. Use for purely non-technical context.

## Fields Explained

### Risk Level
Describes the blast radius and reversibility of the change:
- `low` — Isolated change, easy to revert, no shared state affected
- `med` — Touches shared code, multiple files, or has integration dependencies
- `high` — Affects shared infrastructure, auth, data models, or production systems

### Breaking Change
Whether this update changes a public contract, API, CLI behavior, or file format:
- `yes` — Existing consumers must update to remain compatible
- `no` — Backwards compatible, no consumer changes required

### Decisions Made
A dedicated section for recording explicit trade-off decisions: what options were evaluated, what was chosen, and why alternatives were rejected. Acts as a lightweight ADR (Architecture Decision Record) inline with the log entry. Populate this whenever an alternative approach was considered — even briefly — to prevent re-litigating settled decisions in future sessions.

## When to Create Entries

### Proactive Suggestions (Claude-initiated)
At natural pause points or the end of substantive conversations, review what was discussed and suggest devlog entries for any of the following:

- A technical architecture decision was made or changed
- A meaningful piece of work was completed
- A strategic or business direction was established or shifted
- An important insight emerged that should persist across sessions
- A design trade-off was evaluated and a direction was chosen

Frame the suggestion naturally: "This feels like something worth capturing in the devlog — we decided X because of Y. Want me to log it?"

Do NOT suggest entries for:
- Routine Q&A or simple lookups
- Work that's still in-progress with no decision point
- Minor clarifications that don't change direction

### Ad-hoc Entries (User-initiated)
The user may say things like:
- "Log this"
- "Add this to the devlog"
- "This should go in the changelog"
- "Devlog: [description]"

When this happens, capture what the user is referring to — which may be the immediately preceding discussion, a specific decision, or something they describe explicitly.

## Workflow: Creating and Pushing an Entry

### Step 1: Draft the Entry
Write the entry following the format above. Include rich detail — the goal is that someone reading this months from now (including a future Claude session) can fully understand what happened and why.

### Step 2: Show the User
Present the drafted entry in the conversation and ask for confirmation or edits before committing. Never push without the user seeing the entry first.

### Step 3: Update DEVLOG.md
**IMPORTANT: DEVLOG.md always lives inside the project repo root — never in a parent directory.**

Determine the correct path:
- **Local Claude Code session** (most common): The primary working directory IS the repo root. Use `{cwd}/DEVLOG.md` directly — e.g., if cwd is `/Users/alice/projects/my-app`, write to `/Users/alice/projects/my-app/DEVLOG.md`.
- **Remote/cloned session**: Use the cloned repo path (e.g., `/home/claude/[repo-name]/DEVLOG.md`).

Read the existing DEVLOG.md from that path (or create it if it doesn't exist). Insert the new entry at the top of the file, below the header. Entries are reverse-chronological (newest first).

If creating DEVLOG.md for the first time, use this header:

```markdown
# [Project Name] — Development Log

A living record of architectural decisions, milestones, key insights, and strategic direction.  
Auto-maintained via Claude devlog skill. Entries are reverse-chronological.

---
```

Replace `[Project Name]` with the actual project name established during session setup (e.g., "The Chronicle", "My API Service").

### Step 4: Commit and Push
After the user approves:

```bash
cd /path/to/repo
git add DEVLOG.md
git commit -m "devlog: [brief title from entry]"
git push origin main
```

Use a commit message prefixed with `devlog:` followed by a short version of the entry title.

### Error Handling
- **Git not configured:** Prompt the user to provide their repo URL and set up authentication. Walk them through creating a Personal Access Token if needed.
- **Push fails:** Show the error, suggest common fixes (auth issues, branch protection, network). Offer to save the entry locally so it's not lost.
- **DEVLOG.md has merge conflicts:** Show the conflict, help resolve it, then retry.
- **No repo cloned locally:** Clone the repo first, then proceed.

## Git Setup (First-Time Per Session)

### Local Claude Code Session (Most Common)
If Claude Code's primary working directory is already inside the project repo, no setup is needed — the repo is already available locally. Verify with:

```bash
git -C /path/to/cwd rev-parse --show-toplevel
```

The output is the repo root. DEVLOG.md belongs there.

### Remote/Sandboxed Session
If the repo isn't available locally, check whether it's been cloned and git is configured:

```bash
# Check if repo exists locally (use project-specific directory name)
ls /home/claude/[repo-name] 2>/dev/null

# If not, clone it (user needs to provide URL + token)
# GitHub:  https://[token]@github.com/[user]/[repo].git
# GitLab:  https://oauth2:[token]@gitlab.com/[user]/[repo].git
git clone https://[token]@[host]/[user]/[repo].git /home/claude/[repo-name]

# Configure git identity
git config --global user.name "[Project Name] Devlog"
git config --global user.email "devlog@[repo-name].dev"
```

Replace `[repo-name]`, `[user]`, `[token]`, and `[Project Name]` with the actual values from the project context established during session setup.

Only ask the user for a personal access token if a push fails with an authentication error. If git is using a credential helper (e.g., macOS Keychain), auth is handled transparently and no token is needed.

## Multi-Project Support

If the user works on multiple projects, keep each repo cloned in its own directory under `/home/claude/`. When the user switches context (mentions a different project or repo), confirm the switch: "Switching devlog context to [Other Project] — correct?"

The devlog for each project lives in its own repo's `DEVLOG.md` and is managed independently.

## Reading the Devlog

When the user asks about past decisions, progress, or project history:

1. Read DEVLOG.md from the repo
2. Search entries by category, tags, or keywords
3. Summarize relevant entries in the conversation

This makes the devlog a two-way resource — not just for writing, but for recalling context.

## Multiple Entries Per Session

If a conversation covers several loggable topics, batch them:
- Draft all entries at once
- Present them together for review
- Commit them in a single push with a summary commit message like `devlog: session updates - [topic1], [topic2]`

## Style Guidelines

- Write in past tense for decisions and completed work ("Chose SQLAlchemy over raw SQL")
- Write in present tense for ongoing context ("The data model supports 14 tables")
- Be specific — include version numbers, file names, and concrete details
- Include the "why" for every "what" — rationale is the most valuable part
- Reference related devlog entries by date when they exist
- Keep tags lowercase, hyphenated, and specific enough to be useful for searching
- Set `Risk Level: high` any time the change touches authentication, secrets, data models, CI/CD pipelines, or production infrastructure
- Populate `Decisions Made` whenever an alternative approach was considered and rejected — even briefly. The goal is to prevent re-litigating settled decisions in future sessions.
