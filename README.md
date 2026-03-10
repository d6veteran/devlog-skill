# claude-devlog-skill

> Stop rebuilding context. Start every Claude session where the last one left off.

![License](https://img.shields.io/badge/license-MIT-blue) ![Works with Claude Code](https://img.shields.io/badge/works%20with-Claude%20Code-8A2BE2) ![Skill](https://img.shields.io/badge/type-skill-orange)

---

## The Problem

Claude is exceptional within a session. It remembers everything you said, every decision you made, every tradeoff you discussed. Then the session ends.

Next time you open Claude Code, you're starting from zero. You re-explain the architecture. You re-litigate decisions you already made. Claude gives you generic advice about a problem you already solved last week. You waste the first ten minutes of every session rebuilding context that should already be there.

`devlog-skill` fixes this. It maintains a `DEVLOG.md` in your Git repository — a living briefing document that captures your architectural decisions, your roadmap direction, and the bugs you've already found and fixed. Every new session can open with full context on what was decided and why.

---

## Who This Is For

Developers working on long-running projects who are tired of losing context between Claude sessions.

If you've ever watched Claude suggest an approach you already evaluated and rejected, propose an architecture you already have in place, or ask you to explain something you explained in a session three weeks ago — this is for you.

Works especially well alongside [claude-dev-team](https://github.com/d6veteran/claude-dev-team). When you're switching between specialists like Akira, Robin, and River across multiple sessions, the devlog ensures each new session inherits the full context of what the previous one decided.

---

## See the Difference

A new session. You're picking up where you left off.

**Without the devlog:**

> **You:** Let's continue on the auth system. I want to add refresh token rotation.
>
> **Claude:** Great idea! Refresh token rotation is a good security practice. Here are some common approaches:
> - Store refresh tokens in an httpOnly cookie
> - Implement single-use rotation where each refresh invalidates the previous token
> - Consider a reuse detection window for clients on unreliable networks
>
> Which approach fits your use case?

**With the devlog:**

> **You:** Let's continue on the auth system. I want to add refresh token rotation.
>
> **Claude:** I can see from the devlog that on [date] you chose short-lived JWTs over session cookies specifically to support the mobile client — refresh token rotation fits cleanly with that. The key decision here is single-use rotation vs. a reuse window. The devlog also shows you're targeting HIPAA compliance, which pushes toward single-use despite the network retry tradeoff. The known issue logged on [date] with token invalidation on concurrent requests is relevant here too — want to address that as part of this change?

No re-explaining. No re-litigating. Just work.

---

## What It Does

- **Proactively suggests entries** when significant decisions, milestones, or insights come up in conversation
- **Accepts ad-hoc entries** when you say "log this", "devlog", or `/devlog`
- **Captures architectural decisions** — what was chosen, what was rejected, and why
- **Tracks the roadmap** — priorities, scope decisions, and planned next steps
- **Logs known bugs and solutions** — so future sessions don't re-diagnose solved problems
- **Writes detailed entries** with category, tags, risk level, and full rationale
- **Auto-pushes to your Git remote** after you approve each entry
- **Works with any project** — not tied to a specific repo or stack
- **Supports GitHub and GitLab** — uses your system's existing git credentials

---

## Entry Categories

| Category | What It Captures |
|---|---|
| `feature` | New capability or user-facing functionality added to the project |
| `bugfix` | A defect was identified and resolved |
| `refactor` | Internal restructuring with no behavior change (code quality, naming, structure) |
| `infrastructure` | Build system, CI/CD, deployment, tooling, or dependency changes |
| `security` | Security hardening, vulnerability fixes, auth changes, secrets management |
| `milestone` | Completed phase, release, or significant deliverable |
| `strategy` | Business decisions, market positioning, go-to-market pivots (non-technical) |

---

## Installation

This skill is for [Claude Code](https://claude.ai/code). Install it once and it's available across all your projects:

```bash
mkdir -p ~/.claude/skills/devlog
curl -o ~/.claude/skills/devlog/SKILL.md \
  https://raw.githubusercontent.com/d6veteran/claude-devlog-skill/main/SKILL.md
```

---

## Usage

Once installed, the skill activates automatically in Claude Code. You can:

- **Let Claude suggest entries** — it will offer to log significant decisions at natural pause points
- **Request entries directly** — say "log this", "devlog", or "add this to the devlog"
- **Invoke directly** — type `/devlog` in Claude Code
- **Review before pushing** — Claude always shows you the draft entry before committing

Authentication is handled via your system's git credential helper (e.g., macOS Keychain) — no token prompts needed per session.

---

## DEVLOG.md Format

Entries are reverse-chronological (newest first) and follow this structure:

```markdown
## [YYYY-MM-DD] Brief descriptive title

**Category:** `feature` | `bugfix` | `refactor` | `infrastructure` | `security` | `milestone` | `strategy`
**Tags:** `relevant`, `topic`, `tags`
**Risk Level:** `low` | `med` | `high`
**Breaking Change:** `yes` | `no`

### Summary
A 1-2 sentence overview of what happened or was decided.

### Detail
Full context including what was decided, why, and implications for future work.

### Decisions Made
Explicit trade-off decisions — what options were evaluated, what was chosen,
and why alternatives were rejected. Acts as a lightweight inline ADR.

### Related
- Links to related entries or documents
```

### Metadata Fields

**Risk Level** describes the blast radius and reversibility of the change:
- `low` — Isolated, easy to revert, no shared state affected
- `med` — Touches shared code, multiple files, or has integration dependencies
- `high` — Affects shared infrastructure, auth, data models, or production systems

**Breaking Change** flags whether existing consumers must update:
- `yes` — Public contract, API, CLI behavior, or file format changed
- `no` — Backwards compatible

---

## Repository Contents

| File | Purpose |
|---|---|
| `SKILL.md` | The skill source file — Claude's instructions for how the devlog works |
| `README.md` | This file — repo documentation |

---

## License

See [LICENSE](LICENSE) for details.
