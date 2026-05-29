---
name: fresh-eyes-review
description: >
  Simulate real human readers (QA / dev / tech lead at chosen experience
  levels) reading handover, onboarding, or handbook docs — blind (doc-only)
  or with full repo access — to catch what would make the receiving team
  bounce it back ("what did you even send me?"). Reports per-persona
  comprehension, friction points, reading fatigue, doc-vs-code drift, and
  undecodable internal jargon. Read-only — never edits.
---

# fresh-eyes-review

Before you hand a document to your dev / QA / ops team, you want to know one
thing: **will a real person actually understand it?**

This skill answers that. It role-plays several human readers (personas) and has
each of them read your handover / onboarding / handbook docs **the way a human
actually reads** — one file at a time, with limited memory, getting tired and
lost like a person would — *not* like an AI that scans every file at once and
remembers everything. It then reports exactly where the receiving team will get
confused, give up, or bounce the document back to you.

Use it as a **pre-flight check** for any document set before it goes out.

---

## When to trigger

Invoke this skill when the user says things like:

- "blind test these docs" / "read this like a real person" / "fresh eyes"
- "review this onboarding / handbook / handover before I send it to the team"
- "would a new hire actually understand this?"
- "will the team bounce this back?"
- "have a few team members read this and tell me what breaks"

> Works on **any folder of documents** — it is generic and not tied to one repo.

---

## Operating principle — READ-ONLY (non-negotiable)

This skill **only reads and reports**. It never changes anything.

- ❌ Never edit, create, or delete files. Never propose a diff/patch. Never open a PR.
- ❌ Never run anything with side effects: no build, migrate, deploy, push, or
  tests that write to a database.
- ✅ Only read-style commands are allowed (`ls`, `cat`, `grep`, `find`, reading
  `file:line`) — used to "open files the way a real reader would open them."
- ✅ Output is a **report in the conversation only**. Do not write results to
  disk unless the user explicitly asks.

If the user wants the findings fixed, say that this skill reviews only — they
should use a separate edit/authoring flow, then re-run `fresh-eyes-review` to
confirm the fixes landed.

---

## Output language

> Write the report in the **same language as the documents being reviewed** —
> Thai docs → Thai report, English docs → English report.

Reason: flow, tone, and word-level ambiguity can only be judged in the reader's
actual language. If the docs mix languages, report in the document's primary
language and flag any place where the language-switching itself causes friction.

---

## Workflow (follow in order)

### Step 0 — Confirm the document scope

If not already given, ask the user: **which folder / files should be reviewed?**
Record the path clearly. In Mode A (blind) this scope is the hard boundary you
must not read outside of.

### Step 1 — Ask for the reading mode (A / B / C)

Let the user choose before you start:

| Mode | Name | Can read | Extra work |
|------|------|----------|------------|
| **A** | Blind / doc-only | **Only the specified docs folder** | Test whether the docs stand on their own |
| **B** | Full repo | Everything (code, notes, config) | Actually perform tasks + cross-check for drift |
| **C** | Both | Run A first, then B | Compare: what *requires* reading the code to understand |

> If the user doesn't choose, default to **A (blind)** — it best matches the
> core question "would a new reader understand this?"

**Mode details:**

- **Mode A (Blind):** Read only the files inside the specified docs folder. Do
  **not** open source code, internal notes/vaults, or anything outside scope. If
  a document links out to code or external files, treat that link as
  **"cannot open,"** and record whether not being able to follow it leaves the
  reader with an incomplete understanding (i.e. how much the docs depend on
  external material / whether they stand on their own).

- **Mode B (Full repo):** Everything is readable. Beyond reading, you must:
  1. **Actually attempt 1–2 tasks the doc instructs** (e.g. follow the setup,
     run a described flow, find a file it points to) and note where you get stuck.
  2. **Cross-check for drift:** Does each cited `file:line` exist and match? Do
     the numbers / values / function names / env vars in the doc match the real
     source? Do the links resolve? Every mismatch is **drift** — flag it with
     evidence (what the doc claims vs. what the source actually says).

- **Mode C:** Run a full Mode A pass first (record the findings), then run Mode B.
  Summarize at the end: which points a blind reader got stuck on but the code
  resolved (→ the docs should add this), and which points read fine blind but the
  code proves the doc is wrong (→ real drift).

### Step 2 — Define the simulated team (personas)

Ask the user who should read it. For each persona, capture:

- **Role** (e.g. QA, dev, tech lead, ops, non-dev owner)
- **Years of experience + level of dev knowledge** (e.g. "QA, 3y, low dev",
  "tech lead, 10y")
- (Optional) specific context, e.g. "just joined, has never seen this codebase"

> If the user doesn't specify, propose a **default set of 3**:
> - **QA** — 3 years, low dev knowledge (tires quickly on dense technical text)
> - **Mid-level dev** — 3 years, codes fluently but doesn't know this project
> - **Tech lead** — 10 years, reads fast, hunts for correctness / gaps / risk

**Do one full read per persona** — each has a different eye, patience level, and
background, so the points where they stumble will differ.

### Step 3 — Read like a real human (the most important rule)

Every persona must read under these constraints — **do not behave like an
all-knowing AI:**

1. **Read sequentially, file by file**, the way a real person would open them
   (start from the README / index / entry point the docs point to). Follow links
   and files as the document directs — do **not** scan every file in the folder
   at once and assemble a mental picture.
2. **Limited memory:** at any point you only know what you've read *so far*. If
   file 7 uses a term defined back in file 2, can you still recall it? If you'd
   have to scroll back to find it, that's a friction point.
3. **You get tired, bored, and lost:** report the **real feelings** during the
   read — where fatigue sets in, where you want to skip, where you lose the
   thread because links bounce around.
4. **Never open with "I now have the whole picture in my head."** Simulate the
   genuine experience of someone who just opened a pile of files and has to climb
   it gradually — including the "ugh, this is a lot, where do I even start?"
   reaction.
5. **Keep a running log while reading** ("file A: understood… file B: started
   getting confused at X because the term wasn't defined yet… file C: had to
   scroll back to A") so feedback reflects the actual reading order, not
   hindsight knowledge.

### Step 4 — Decoder check for internal jargon / codes

While reading, hunt down every code / abbreviation / piece of jargon the docs
use, e.g.:

- Encoded sprint/phase names (`M28b`, `8e`, `N1`, `Phase 2`, …)
- Internal acronyms / home-grown system or module names
- Domain-specific terms (e.g. `H-beam`, `wideflange`, project-specific
  product/customer/SKU codes)

For each one, ask on behalf of a new reader:

- **Can a new reader figure it out from the docs alone?**
- Is there a **glossary / decoder**? **How many clicks / files away** is it from
  where the term is used?
- Does hitting a bare code mid-sentence (no inline explanation) cause a **stumble**?
- Any code with **no definition anywhere in the docs** → flag it as a
  **"the team will definitely ask about this"** item.

### Step 5 — Per-persona report

Each persona produces all five of these:

1. **Comprehension:** roughly what % of the system / task did they understand?
   Briefly summarize what they actually grasped. Where did they get confused,
   misunderstand, or have to guess?
2. **Friction points:** which file / line / section made them **stop / get
   confused / re-read / get lost**. Cite the location (`file:section` or
   `file:line`).
3. **Doc accuracy (Mode B/C only):** do the cited `file:line` / values / links
   **match reality**? Where is the drift (doc says X / reality is Y)?
4. **Fatigue & flow:** was reading it fun / neutral / tiring / painful? Where?
   Smooth or choppy? How did the volume of files feel? Were long tables
   manageable? Did link-heavy sections make you lose the thread? Where did you
   start wanting to quit?
5. **Verdict:** if you were really this person — could you do real work on day
   one? Would you keep reading, or close it / bounce it back? Give a direct
   verdict in that persona's voice.

### Step 6 — Combined summary + "will the team bounce it?"

Close with a cross-persona overview:

1. **🚩 What will make the receiving team ask "what did you even send me?"** —
   list these clearly, ordered by severity. This is the heart of the output.
2. **🔧 Fix list** — ordered by **impact / effort** (what to fix first for the
   biggest gain). For each, state what the reader gains once it's fixed.
3. **Classify every finding** as one of:
   - **Real bug** = drift / wrong info / broken link (must fix)
   - **By-design** = intentional (maybe just add a note)
   - **Simulation limit** = something the simulated persona might misjudge (e.g.
     blind mode can't open code, so it flags something as "missing" when the code
     actually has it) — call these out so the user doesn't over-correct

---

## Report format (template)

Use this structure (adapt the language to match the docs):

```
# Fresh-Eyes Review — <document set name>
Mode: <A / B / C>   |   Scope: <path>   |   Personas: <n>

────────────────────────────────────────
## 👤 Persona 1: <role, years, dev level>

**Running log (in reading order):**
- <file A>: ...
- <file B>: started getting confused at ... because ...

1. Comprehension: ~__%  → <what they actually grasped / where they misread>
2. Friction points:
   - `file:section` — <symptom: stopped / re-read / got lost>
3. Doc accuracy (B/C): <drift found / "no drift">
4. Fatigue & flow: <fun/tiring/painful where, smooth or choppy>
5. Verdict: <can they work on day one? keep reading or bounce it?>

(repeat for every persona)

────────────────────────────────────────
## 🧩 Decoder check — internal jargon / codes
| Code/term | Defined in docs? | Distance from use | Verdict |
|-----------|------------------|-------------------|---------|
| M28b      | ❌ nowhere       | -                 | 🚩 team will ask |

────────────────────────────────────────
## 📋 Combined summary
### 🚩 What makes the team bounce it back
1. ...
### 🔧 Fix list (by impact / effort)
| # | Issue | Type (bug / by-design / sim-limit) | Impact | Effort | Payoff once fixed |
|---|-------|------------------------------------|--------|--------|-------------------|
### Mode C only — blind vs. full comparison
- Needs the code to understand: ...
- Reads fine blind, but code proves the doc wrong (drift): ...
```

---

## Acceptance criteria

- Invoking the skill → asks for mode (A/B/C) + the simulated team → reviews as
  chosen → produces a **per-persona report** + a **combined summary**.
- Reusable on any folder of documents (generic).
- **Never modifies any file** — strictly read-only.
- Report is written in the same language as the reviewed documents.
