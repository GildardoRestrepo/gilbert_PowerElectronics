---
title: "Documentation Style Guide"
created: 2026-08-22
time: ""
creator: "Gilbert"
last update: 2026-10-10
update by: "Gilbert"
type: reference
status: active
stage: ""
area: management
editor: "Gilbert"
order: 0
tags:
  - type/reference
---
# Documentation Style Guide

> [!NOTE]
> A single format for every `.md` file in this repository (calculation reports, references, decision records, READMEs and test reports). Frontmatter is Obsidian-ready and renders on GitHub.

## 1. Required skeleton

Every document follows this structure, in this order:

```markdown
---
title: "Document title"            # same text as the H1, in double quotes
created: 2026-08-08                 # ISO 8601 (YYYY-MM-DD), creation date
time: ""                            # creation time (HH:MM), optional
creator: "Gilbert"                  # who created it
last update: 2026-08-08             # ISO 8601, date of the last content change
update by: "Gilbert"                # who made the last change
type: calculation                   # see section 3
status: active                      # draft | active | accepted | obsolete
stage: 1                            # 1–5 (see section 4); "" if cross-cutting
area: power                         # see section 4; "" if cross-cutting
editor: "Gilbert"                   # person responsible for the document
order: 0                            # integer used to sort files within a folder
tags:
  - type/calculation                # must match the `type` field
---
# Document title

> [!NOTE]
> One line stating what the document solves and for whom.

## 1. First section
...

---
## 2. Second section
...

---
## Links
- [Related document](../path/to/document.md)
```

---
## 2. Rules

- **Complete frontmatter**: use all thirteen fields (`title`, `created`, `time`,
  `creator`, `last update`, `update by`, `type`, `status`, `stage`, `area`, `editor`,
  `order`, `tags`). Fields that do not apply are left empty (`time: ""`, `stage: ""`),
  never omitted.
- **Quotes**: free-text values (`title`, `creator`, `update by`, `editor`) go in
  **double quotes**. ISO dates, `order` (integer) and closed-list values (`type`,
  `status`, `stage`, `area`) go without quotes.
- **`type` and `tags` must match** (`type: calculation` ⇒ `tags: [type/calculation]`).
- **Dates are always ISO 8601** (`2026-08-08`). Never `DD/MM/YYYY`.
- **Dates live only in the frontmatter** (`created` / `last update`). Do not repeat a
  "last updated" footer in the body.
- **Status lives only in the frontmatter** (`status`). Do not repeat it in the body
  (this applies especially to decision records).
- **Authorship lives only in the frontmatter** (`creator` / `editor` / `update by`).
  No author block in the body.
- **`title` = H1 text**, in double quotes.
- **`order`**: integer used to sort documents within a folder. If the file has a numeric
  prefix (`01_buck.md`), use that number; otherwise `0`.
- **Summary as a callout**: `> [!NOTE]` followed by the text on `>` lines. GitHub only
  renders the alert types `NOTE`, `TIP`, `IMPORTANT`, `WARNING` and `CAUTION`; use
  `> [!WARNING]` for safety notes.
- **Single language: English.**
- **Headings**: a single `# H1` (the document title). Content sections are `## 1.`,
  `## 2.`; subsections are `### 1.1`, `### 1.2`. The only unnumbered section is the
  closing `## Links` appendix.
- **Section separators**: a horizontal rule `---` between consecutive sections (before
  every `## ` except the first).
- **Links**: use relative Markdown links (`[Buck](../21_calculations/01_buck.md)`).
  Obsidian wikilinks (`[[ ]]`) do not resolve on GitHub and must not be used.
- **Equations**: LaTeX between `$...$` (inline) or `$$...$$` (block), with units in
  `\mathrm{}` (e.g. `$V_o = 110\,\mathrm{V}$`).
- **Code and paths** always in `backticks`; code blocks declare their language
  (```python, ```cpp, ```yaml).

---
## 3. Allowed `type` values

| `type`        | Use                                                          |
| ------------- | ------------------------------------------------------------ |
| `calculation` | Design calculation and component sizing report.              |
| `test`        | Record of a lab test or experiment.                          |
| `manual`      | Step-by-step procedure (assembly, setup, operation).         |
| `reference`   | Explains how something works, without being a procedure.     |
| `adr`         | Architecture decision record (see section 5).                |
| `readme`      | README of a folder or the project.                           |
| `doc`         | Project document that fits none of the categories above.     |

---
## 4. `status`, `stage` and `area` values

**`status`**: life cycle of the document.

| `status`    | Meaning                                         |
| ----------- | ----------------------------------------------- |
| `draft`     | Being written; not reliable yet.                |
| `active`    | Current and in use.                             |
| `accepted`  | Approved decision (for ADRs).                   |
| `obsolete`  | Superseded or historical; kept as a record.     |

**`stage`**: design stage, matching the last digit of the stage folder
(`21_calculations` ⇒ `stage: 1`).

| `stage` | Stage            |
| ------- | ---------------- |
| `1`     | Calculations     |
| `2`     | PSIM simulation  |
| `3`     | PCB design       |
| `4`     | Build            |
| `5`     | Lab test         |

**`area`**: main discipline of the document.

| `area`        | Scope                                                     |
| ------------- | --------------------------------------------------------- |
| `power`       | Power stage: topology, magnetics, semiconductors, losses. |
| `control`     | Gating, firmware, PWM generation, protections.            |
| `pcb`         | Schematic, layout, gate drivers, isolation.               |
| `mechanical`  | Enclosure, 3D modelling and printing, thermal mounting.   |
| `management`  | README, roadmap, style, processes.                        |

---
## 5. ADR template

An ADR (*Architecture Decision Record*) documents **why** a decision was made, not only
what was chosen. Use `type: adr`. `status` reflects the decision state (`accepted`,
`obsolete`…) and is not repeated in the body. Recommended structure per decision:

```markdown
## N. Decision title

**Context.** Problem or constraint that motivates the decision.

**Options considered.**
- *Option A.* Pros/cons.
- *Option B.* Pros/cons.

**Decision.** What was chosen.

**Consequences.**
- ✅ Positive effect.
- ⚠️ Accepted cost or limitation.
```

---
## Links
- [Project README](../README.md)
