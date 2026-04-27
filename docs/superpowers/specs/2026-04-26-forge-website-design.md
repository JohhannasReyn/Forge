# Forge Companion Website — Design Spec

**Date:** 2026-04-26
**Author:** Brainstorming session (Johhannas Reyn + Claude)
**Status:** Approved — ready for implementation

---

## Goal

Build a GitHub Pages-ready companion website for the Forge programming language, hosted from `docs/` in the repo root. The site must satisfy the CMSI 3802 assignment rubric:

1. Visually polished hand-crafted CSS (3 pts)
2. Language story in ≤ 3 paragraphs
3. At least 5 complete example programs
4. Developer names and short bios
5. Link back to the GitHub repo

---

## Files

```
docs/
├── index.html       — single-page site, all sections
└── style.css        — all styling

Images referenced as:  ../public/Images/Logo.webp  (relative from docs/)
Interactive JS:        ~20 lines vanilla JS, inline at bottom of index.html
No build step, no frameworks, GitHub Pages-ready from /docs root.
```

---

## Visual Design

### Color Palette

| Role                  | Value     | Usage                                      |
|-----------------------|-----------|--------------------------------------------|
| Background            | `#f7f3ec` | Page background (cream)                    |
| Surface               | `#eee9df` | Cards, code panel backgrounds              |
| Forest dark           | `#2d3b2a` | Headings, nav, footer background           |
| Sage green            | `#7a9e7e` | Accents, links, active tab indicators      |
| Muted sage            | `#b4c9b4` | Borders, dividers, inactive tab lines      |
| Code block background | `#252f24` | All `<pre><code>` blocks                   |
| Code text             | `#d4e8c2` | Code inside dark blocks                    |
| Body text             | `#3a3a3a` | Paragraph text on cream background         |

### Typography

- **Headings:** Georgia, 'Times New Roman', serif — handcrafted feel
- **Body:** system-ui, -apple-system, sans-serif — clean and readable
- **Code:** 'Courier New', Courier, monospace — no external font loads

### Aesthetic Rules

- No drop shadows
- No rounded corners except on code blocks (4px)
- Generous whitespace between sections
- Section separators: thin `1px solid #b4c9b4` horizontal rule
- Intentional and quiet — nothing generic or boilerplate

---

## Responsive Behavior

Two `@media` breakpoints only:

- **≥ 900px (desktop):** Features 3-column grid, team cards side-by-side, full nav
- **480px–899px (tablet):** Features 2-column grid, team cards stacked
- **< 480px (mobile):** Features 1-column, nav wraps to centered rows, code blocks scroll horizontally

---

## Page Sections (in order)

### 1. Sticky Nav
- Left: `Icon.webp` (24px) + "Forge" wordmark in forest dark
- Right: anchor links — Story, Features, Checks, Examples, Team, GitHub
- Background: cream `#f7f3ec`, bottom border `1px solid #b4c9b4`
- Stays fixed at top during scroll

### 2. Hero
- Centered `Logo.webp`, max-width 220px
- Large serif `<h1>` — "Forge"
- One-line tagline: *"A statically typed language that compiles to JavaScript — built for clarity, precision, and craft."*
- Two CTA buttons:
  - "View on GitHub" → `https://github.com/JohhannasReyn/Forge` (sage green, filled)
  - "Jump to Examples" → `#examples` anchor (sage green, outlined)

### 3. Story (≤ 3 paragraphs)
Section ID: `#story`

Paragraph 1 — The problem and motivation:
> Forge was built around a simple question: what if writing low-level logic felt as deliberate as metalwork? Most languages either enforce rigid ceremony or leave too much implied. Forge takes a different path — every function declares what it returns, every variable knows its type, and the compiler tells you exactly when something is wrong before your code ever runs.

Paragraph 2 — What Forge does:
> Forge compiles to JavaScript through a full pipeline: a grammar defined in Ohm.js, a parser that builds a typed AST, a static analyzer that enforces 13 safety checks, an optimizer that folds constants and eliminates dead branches, and a code generator that produces clean, readable JS. Control flow reads like intention — `is` instead of `if`, `whenis` for while loops, `deliver` to return a value, `resolve` to return nothing.

Paragraph 3 — The craft angle:
> The language is intentionally small. Forge doesn't try to do everything — it tries to do its small set of things precisely and verifiably. It's a language for people who want to know exactly what their code is doing, and a compiler that makes that knowledge cheap.

### 4. Features
Section ID: `#features`

Six cards in a 3-column grid:

| Card | Title | Content |
|------|-------|---------|
| 1 | Strong Types | 9 primitive types: `byte`, `char`, `snum`, `inum`, `lnum`, `fnum`, `dnum`, `hilo` (bool), `empty` (void) |
| 2 | Expressive Control Flow | `is`/`not` (if/else), `whenis` (while), `whento` (for), `execute`/`whenis` (do-while), `hub` (switch) |
| 3 | Safety by Default | 13 static checks enforced at compile time — undeclared variables, bad assignments, wrong call signatures, and more |
| 4 | Bit-Level Precision | Native bit indexing (`x{n}`), bitwise word operators (`bitand`, `bitor`, `shiftl`, `shiftr`, `bitnot`), array slicing (`x[i:j]`) |
| 5 | Custom Types | Enum-style `custom` types with optional literal payloads; `hub` switch dispatches on them cleanly |
| 6 | Compiles to JavaScript | Full optimizer + generator pipeline produces readable, idiomatic JS — no runtime, no bundler required |

### 5. Static Checks
Section ID: `#checks`

Numbered list. Enforced checks in sage green; unimplemented ones muted with "Not yet enforced" label.

| # | Check | Status |
|---|-------|--------|
| 1 | Undeclared identifier use | Enforced |
| 2 | Duplicate declarations in same scope | Enforced |
| 3 | Assignment to constants (`C`) | Enforced |
| 4 | `resolve` used in non-void function | Enforced |
| 5 | `deliver` used in void function | Enforced |
| 6 | `escape` used outside loop or `hub` | Enforced |
| 7 | Wrong argument count in function calls | Enforced |
| 8 | Wrong argument types in function calls | Enforced |
| 9 | Incompatible assignment types | Enforced |
| 10 | Invalid operator/type combinations | Enforced |
| 11 | Duplicate case labels in `hub` | Enforced |
| 12 | Bitwise operations on non-integer types | Enforced |
| 13 | Type widening rules (e.g., hilo → inum) | Enforced |
| 14 | Missing return in non-void functions | Not yet enforced |
| 15 | Multiple `base` clauses in `hub` | Not yet enforced (grammar prevents it) |
| 16 | Non-constant case label expressions | Not yet enforced |

### 6. Examples
Section ID: `#examples`

Five top-level tabs: Hello | Math | Bitops | Enum | Globals

Each tab panel has two sub-tabs: **Forge** | **JavaScript**

#### Example 1 — Hello (hello.forge)
Demonstrates: functions, typed parameters, `is`/`not`, recursion, `deliver`/`resolve`

**Forge source:** (full content of examples/hello.forge)
**Generated JS:** (full output of `node src/forge.js --generate examples/hello.forge`)

#### Example 2 — Math (math.forge)
Demonstrates: `whento` for-loop, `whenis` while-loop, compound assignment (`*=`, `+=`), constants (`C`), multiple functions

**Forge source:** (full content of examples/math.forge)
**Generated JS:** (full output of `node src/forge.js --generate examples/math.forge`)

#### Example 3 — Bitops (bitops.forge)
Demonstrates: `bitand`, `bitor`, `shiftl`, `shiftr`, `bitnot`, bit-index read (`x{pos}`), bit-index write (`flags{0} = TT`)

**Forge source:** (full content of examples/bitops.forge)
**Generated JS:** (full output of `node src/forge.js --generate examples/bitops.forge`)

#### Example 4 — Enum (enum.forge)
Demonstrates: `custom` types with/without payloads, `hub`/`switch` with case labels and `base` default, field access (`Direction.North`)

**Forge source:** (full content of examples/enum.forge)
**Generated JS:** (full output of `node src/forge.js --generate examples/enum.forge`)

#### Example 5 — Globals (globals.forge)
Demonstrates: `global` declarations, field access (`config.maxRetries`), complex multi-branch functions, nested loops

**Forge source:** (full content of examples/globals.forge)
**Generated JS:** (full output of `node src/forge.js --generate examples/globals.forge`)

### 7. Team
Section ID: `#team`

Two side-by-side cards (stack on mobile):

**Card 1 — Nitya Addanki**
- Name (h3)
- Role: "Language Designer & Compiler Engineer"
- Bio: *[Placeholder — Nitya, add a short 2–3 sentence bio here describing your background and what you worked on in Forge.]*

**Card 2 — Tessa Smigla**
- Name (h3)
- Role: "Language Designer & Compiler Engineer"
- Bio: *[Placeholder — Tessa, add a short 2–3 sentence bio here describing your background and what you worked on in Forge.]*

Cards styled with: `#eee9df` background, `1px solid #b4c9b4` border, sage-green top accent bar (4px).

### 8. Footer
- Centered text on `#2d3b2a` background, cream text
- "Forge — a statically typed language targeting JavaScript"
- Link: "View on GitHub →" → `https://github.com/JohhannasReyn/Forge`
- "MIT License · CMSI 3802 · Loyola Marymount University"

---

## Tab Interactivity (Vanilla JS)

Two layers of tabs:
1. **Example tabs** (Hello, Math, Bitops, Enum, Globals) — show/hide `.example-panel` divs
2. **Source/JS sub-tabs** (Forge, JavaScript) — show/hide `.code-pane` divs within active panel

Logic (~25 lines):
- On tab click: remove `active` class from all siblings, add to clicked tab, hide all panels, show matching panel
- Sub-tabs work identically within their panel scope
- Default: first example tab active, Forge sub-tab active

No external libraries. No build step.

---

## Rubric Checklist

| Requirement | How it's met |
|-------------|--------------|
| Visually polished, hand-crafted CSS (3 pts) | Custom sage/cream/forest palette, serif headings, dark code blocks, intentional spacing — nothing generic |
| Story ≤ 3 paragraphs | Exactly 3 paragraphs in section #3 |
| ≥ 5 example programs | 5 tabbed examples covering all syntactic forms |
| Developer names and bios | Nitya Addanki and Tessa Smigla, placeholder bio text |
| Points back to repo | Hero CTA button + footer link to https://github.com/JohhannasReyn/Forge |

---

## What Needs to Be Filled In Later

- Nitya Addanki bio (2–3 sentences)
- Tessa Smigla bio (2–3 sentences)
- Any team affiliation or headshot if desired

---

## GitHub Pages Setup

- Enable GitHub Pages in repo Settings → Pages → Source: `main` branch, `/docs` folder
- No `_config.yml` needed (plain HTML, no Jekyll)
- Site will be available at `https://johhanasreyn.github.io/Forge/`
