# devlog — Development Log

A living record of architectural decisions, milestones, key insights, and strategic direction.
Auto-maintained via Claude devlog skill. Entries are reverse-chronological.

---

## [2026-03-10] Devlog skill schema v2 — richer entry format

**Category:** `feature`
**Tags:** `devlog`, `schema`, `skill`, `adr`
**Risk Level:** `low`
**Breaking Change:** `yes`

### Summary
Updated SKILL.md with a richer entry schema: two new metadata fields (`Risk Level`, `Breaking Change`), a dedicated `Decisions Made` section, and a revised category taxonomy.

### Detail
- Added `Risk Level: low | med | high` metadata to every entry — describes blast radius and reversibility
- Added `Breaking Change: yes | no` metadata — flags when public contracts, APIs, or CLI behavior changes
- Added `### Decisions Made` section to the entry template — a lightweight inline ADR for trade-off decisions
- Replaced old categories (`architecture`, `milestone`, `takeaway`, `strategy`) with: `feature`, `bugfix`, `refactor`, `infrastructure`, `security`, `milestone`, `strategy`
- Added new `## Fields Explained` section to document the new fields with definitions and value options
- Updated Style Guidelines with two new rules: flag `high` risk on auth/secrets/data model changes; always populate `Decisions Made` when alternatives were considered
- `strategy` category retained — preserved for purely non-technical/business entries

### Decisions Made
- **Kept `strategy` category**: Initial proposal dropped it in favor of a code-centric taxonomy. Restored after recognizing business/non-technical decisions need a home in the log.
- **Removed `takeaway` and `architecture`**: Both were vague and overlapped with other categories. `architecture` maps cleanly to `refactor`/`infrastructure`; `takeaway` knowledge now belongs in `Decisions Made` of the relevant entry.
- **`Decisions Made` as a section, not inline in `Detail`**: Separating ADR content from general context makes it easier to scan entries for trade-off rationale specifically.

### Related
- Breaking change: existing DEVLOG.md entries using `architecture`, `takeaway` categories are not retroactively updated — new entries use the new taxonomy
