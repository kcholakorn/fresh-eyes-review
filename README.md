# fresh-eyes-review

> You hand the doc to the tech team. They open it.
> The reply lands: **"the hell is this? what did you even send me?"**

A pre-handover gauge. `fresh-eyes-review` is a
[Claude Code](https://docs.claude.com/en/docs/claude-code) skill that puts real
human readers — QA, dev, tech lead — through your docs the way humans actually
read (one file at a time, limited memory, getting tired and lost), then tells
you exactly where the team will bounce it back. Run it *before* you hit send —
not after.

<p align="left">
  <img alt="License: MIT" src="https://img.shields.io/badge/License-MIT-blue.svg">
  <img alt="Claude Code skill" src="https://img.shields.io/badge/Claude%20Code-skill-8A2BE2">
  <img alt="Read-only" src="https://img.shields.io/badge/mode-read--only-success">
</p>

---

## The problem

You finish writing a handover doc. You *think* it's clear. But the only real
test is whether the dev/QA/ops team on the other end can actually use it — and
you usually find out *after* you've sent it, when the questions start coming back:

> "Wait, what's `M28b`?" · "Where's the setup step?" · "This file doesn't exist."
> · "never mind, I'll just rewrite it myself."

An LLM asked to "review this doc" is a poor stand-in for that test: it reads
every file at once and remembers everything perfectly, so it never experiences
the confusion a real reader does.

## The idea

`fresh-eyes-review` role-plays several **human personas** (e.g. a 3-year QA, a
mid-level dev, a 10-year tech lead) and makes each one read your docs **the way a
human actually reads** — sequentially, one file at a time, with limited memory,
getting tired and lost like a person would. Then it reports precisely where each
reader stumbles, and what the team is most likely to bounce back.

Think of it as a **pre-flight check** for any document set.

## What it catches

| | |
|---|---|
| 🧠 **Per-persona comprehension** | How much each role/experience level actually understood, and where they got lost |
| 🧱 **Friction points** | The exact file / line / section that made a reader stop, re-read, or lose the thread |
| 😮‍💨 **Reading fatigue & flow** | Where it gets tiring, whether long tables are bearable, where a reader wants to quit |
| 🔀 **Doc-vs-code drift** *(full-repo mode)* | Cited `file:line`, values, and links that no longer match the real source |
| 🧩 **Undecodable jargon** | Internal codes/abbreviations (e.g. `M28b`, `H-beam`) with no glossary the reader can reach |

**Read-only by design.** It reviews and reports — it never edits files, opens
PRs, or proposes diffs.

## How it works

When invoked, the skill runs a short setup and then reads:

**1. Pick a reading mode**

| Mode | Name | Reads | What it adds |
|------|------|-------|--------------|
| **A** | Blind / doc-only | Only the docs folder | Tests whether the docs stand on their own |
| **B** | Full repo | Code, notes, config too | Actually performs tasks from the docs + checks for drift |
| **C** | Both | A then B | Shows what *requires* reading the code to understand |

**2. Define the simulated team** — roles + years of experience + dev level. No
input? It defaults to three readers: QA (3y, low dev) · mid-level dev (3y) · tech
lead (10y).

**3. Read like a human** — one file at a time, in order, with limited memory and
real fatigue. One full read per persona, with a running log.

**4. Report** — a per-persona breakdown (comprehension, friction, fatigue,
verdict) plus a combined summary: *what will make the team bounce it back*, and a
fix list ordered by impact/effort.

> **Reports come out in the document's own language.** Reviewing Thai docs
> produces a Thai report; English docs produce an English report — because flow
> and word-level ambiguity can only be judged in the reader's actual language.

## Installation

Skills live in Claude Code's **skills directory** — `~/.claude/skills/` (global,
available in every project) or a repo's own `.claude/skills/` (one project only).
This repo is the skill folder itself (`SKILL.md` at its root), so you can clone it
straight in:

**Global** (available in every project):

```bash
git clone https://github.com/kcholakorn/fresh-eyes-review.git \
  ~/.claude/skills/fresh-eyes-review
```

**Per-project** (one repo only):

```bash
git clone https://github.com/kcholakorn/fresh-eyes-review.git \
  <your-repo>/.claude/skills/fresh-eyes-review
```

Update later with `git -C ~/.claude/skills/fresh-eyes-review pull`.

> New skills are picked up the next time you start Claude Code.

## Usage

Open Claude Code in the project that holds the docs, then either run the skill
directly:

```
/fresh-eyes-review
```

…or just ask in plain language:

```
Fresh-eyes the docs in docs/handover/ — would a new hire actually understand them?
```

The skill will ask for the mode (A / B / C) and the personas (or use the
defaults), then read and report.

## Example output (excerpt)

```
# Fresh-Eyes Review — docs/handover/
Mode: A (blind)   |   Scope: docs/handover/   |   Personas: 3

## 👤 Persona 2: Mid-level dev, 3y, doesn't know this project
**Running log:**
- 01-overview.md: clear, good map of the system.
- 03-deploy.md: stalled — references "the M28b cutover" with no prior mention.
- 04-env.md: had to scroll back twice to recall what `SBB_` vars meant.

1. Comprehension: ~60% — got the architecture, lost on the release process.
2. Friction: `03-deploy.md:§Cutover` — "M28b" used as if known.
4. Fatigue & flow: fine until the deploy doc; the unexplained codes broke momentum.
5. Verdict: could start reading code, but would Slack the author before deploying.

## 📋 Combined summary
### 🚩 What makes the team bounce it back
1. `M28b` / `8e` sprint codes used throughout with no glossary anywhere.
2. 03-deploy.md assumes prod access that a new hire doesn't have on day one.

### 🔧 Fix list (by impact / effort)
| # | Issue | Type | Impact | Effort | Payoff |
|---|-------|------|--------|--------|--------|
| 1 | Add a glossary for sprint codes | bug | high | low | unblocks every reader |
| 2 | State prerequisites at top of deploy doc | by-design | med | low | fewer day-one questions |
```

## Notes & limitations

- The personas are **simulations**, not your actual teammates — findings tagged
  *simulation limit* in the report flag where a simulated reader might misjudge
  (e.g. blind mode flagging something as "missing" that the code actually has).
- Mode A deliberately **cannot** read code, so it tests document self-sufficiency,
  not factual accuracy. Use Mode B/C to check for drift against the source.

## Contributing

Issues and pull requests are welcome — especially additional persona archetypes,
report-format ideas, and real-world "this is what tripped my team up" examples.

## License

Released under the [MIT License](LICENSE).
