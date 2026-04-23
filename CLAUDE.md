# Forge Project Memory

## Session Protocol

At the beginning of every Claude Code session:
1. Read this file first.
2. Inspect current repo status before editing (`git status`, scan src/test files).
3. Use relevant Superpowers skills before acting.
4. Prefer completing required assignment layers over adding new features.
5. Never add new language features after Day 1 scope is frozen.

At the end of every session:
1. Update this file with changed files, decisions, verification results, blockers, next tasks.
2. Do not claim completion without running verification commands and recording output.

Assignment spec lives at:
`/Users/nityaaddanki/Documents/Forge/pdf_lib/CMSI 3802_ Languages and Automata II_ Homework #1.pdf`

---

## Assignment Goal

**Course:** CMSI 3802 — Languages and Automata II, Homework #1
**Due:** 2026-05-04
**Today (session 0):** 2026-04-22
**Days remaining:** 12

Deliverables required:
- Original programming language of sufficient complexity
- Complete compiler (parser → analyzer → optimizer → generator)
- CLI behavior matching the Carlos compiler model
- 100% test coverage target (at least 50 tests, aim for hundreds)
- Public GitHub repository
- README (logo, story, features, static checks, examples, links)
- Companion GitHub Pages website
- At least 5 complete example programs covering all syntactic forms
- 5-minute lightning talk presentation
- Brightspace submission with individual affidavits

---

## Language Name and Status

**Language name:** Forge
**Target output:** JavaScript
**Grammar tool:** Ohm.js
**Type system:** Statically typed (explicit function signatures, inferred locals)

**Status as of 2026-04-22:** Day 0 / Bootstrap only
- Language spec exists as a detailed markdown doc (`README.md` at repo root — 985 lines)
- Draft Ohm grammar exists at `Ohm-Script.ohm` (repo root, covers ~50% of features)
- All source files in `Plang/src/` are empty placeholders
- All test files in `Plang/test/` are empty placeholders
- `Plang/README.md` is empty
- `Plang/package.json` is empty
- `Plang/docs/` and `Plang/examples/` are placeholder directories only

---

## Frozen Feature Scope

**Status: FROZEN as of Day 0 / 2026-04-22**

### In-Scope (must implement)
- Functions: `func name(param: type): returnType { }` with typed params and return types
- Variable declarations: `let x = expr;` and `let x: type = expr;`
- Constants: `C x = expr;` (immutable, must initialize, no reassignment)
- Assignments: `x = expr;` (with const-check)
- Return semantics: `resolve;` (empty return) and `deliver expr;` (valued return)
- If/else: `is (cond) { } not { }`
- While loop: `whenis (cond) { }`
- Do-while: `execute { } whenis (cond);`
- For loop: `whento (init; cond; update) { }`
- Break: `escape;` (only inside loops or hub)
- Switch: `hub (expr) { :{val}: { } base { } }`
- Arithmetic: `+`, `-`, `*`, `/`, `%`, `^` (exponent)
- Comparison: `<`, `>`, `<=`, `>=`, `==`, `!=`
- Logical: `!`, `&&`, `||`, `^|` (xor), `^&` (xnor)
- Bitwise word ops: `bitand`, `bitor`, `bitxor`, `bitnot`, `shiftl`, `shiftr`
- Symbolic bitwise: `&`, `|`
- Ternary: `(cond) ? expr : expr`
- Literals: integer, float, boolean (`TT`/`FF`), string (`"..."`), char (`'...'`), null (`non`)
- Array indexing: `arr[i]` and slicing: `arr[i:j]`
- Field access: `expr.field`
- Custom types (enums): `custom Name = { A, B(val); }`
- Global declarations: `global g = { field: val, ... };`
- Bit indexing: `x{n}` read and `x{n} = TT/FF` write
- Comments: `//` and `/* */`
- Increment/decrement: `n++`, `n--`, `++n`, `--n`
- Compound assignment: `+=`, `-=`, `*=`, `/=`
- Function calls: `name(args)`

### Out-of-Scope (v0.1 — do not implement)
- Classes (`blueprint`), inheritance (`inherits`), visibility modifiers
- Exceptions (`test`, `onError`, `after`)
- Async/await
- Interfaces (`formed`)
- Summation operator (`E:...`)
- Factorial (`!!`)
- Method access (`->`) as distinct from field access
- `$::` (this) references
- `farm` (imports), `save` (exports)
- Overloading by arity (too complex — keep one function per name per scope)

---

## Assignment Requirements Summary

| Requirement | Notes |
|---|---|
| Group of 3–6 students | Need team member names |
| Original language, sufficient complexity | Forge — statically typed, multiple static checks |
| Complete functional compiler | Parser + analyzer + optimizer + generator + CLI |
| 100% test coverage | At least 50 tests required for full points |
| Public GitHub repo | Repo name should match language name: "Forge" |
| Companion website (GitHub Pages) | Single-page, pretty CSS, 5+ examples, dev bios |
| 5-minute lightning talk | Presentation slides stored in repo |
| Each member contributes meaningfully | Commits from each member |
| Signed affidavits for readings/watchings | Included in Brightspace submission |

### Static Checks Required (Analyzer must implement)
1. Undeclared identifier use → error
2. Duplicate declarations in same scope → error
3. Shadowing in inner scope → warning (or error)
4. Assignment to constants (`C`) → error
5. Invalid use of `resolve` (only in `empty` functions) → error
6. Invalid use of `deliver` (only in non-`empty` functions) → error
7. Invalid use of `escape` (only inside loops or `hub`) → error
8. Missing return in non-`empty` functions → error
9. Wrong argument count in function calls → error
10. Wrong argument types in function calls → error
11. Incompatible assignment types → error
12. Invalid operator/type combinations → error
13. Duplicate case labels in `hub` → error
14. Multiple `base` clauses → error
15. Non-constant case label expressions → error
16. Bitwise ops on non-integer types → error

---

## Repo Structure Status

Expected structure (Plang/ is the project root for the compiler):

```
Plang/
├── .gitignore          MISSING
├── README.md           EMPTY (needs full content)
├── LICENSE             EXISTS
├── package.json        EMPTY (needs full content)
├── .prettierrc.json    EXISTS
├── docs/               PLACEHOLDER ONLY
├── examples/           PLACEHOLDER ONLY
├── src/
│   ├── forge.js        EXISTS as (yourlanguagename).js — needs rename + content
│   ├── forge.ohm       EXISTS as (yourlanguagename).ohm — needs rename + content
│   ├── compiler.js     EXISTS but EMPTY
│   ├── parser.js       EXISTS but EMPTY
│   ├── core.js         EXISTS but EMPTY
│   ├── analyzer.js     EXISTS but EMPTY
│   ├── optimizer.js    EXISTS but EMPTY
│   └── generator.js    EXISTS but EMPTY
└── test/
    ├── compiler.test.js   EMPTY
    ├── parser.test.js     EMPTY
    ├── analyzer.test.js   EMPTY
    ├── optimizer.test.js  EMPTY
    └── generator.test.js  EMPTY
```

Assets available:
- Logo: `/Users/nityaaddanki/Documents/Forge/public/Images/Logo.png` and `.webp`
- Icon: `/Users/nityaaddanki/Documents/Forge/public/Images/Icon.png` and `.webp`
- Language spec: `/Users/nityaaddanki/Documents/Forge/README.md` (root — adapt to Plang/README.md)
- Draft grammar: `/Users/nityaaddanki/Documents/Forge/Ohm-Script.ohm` (move to `Plang/src/forge.ohm`)
- Sprint timeline: `/Users/nityaaddanki/Documents/Forge/to-do/Sprint-Timeline.md`
- Prompt list: `/Users/nityaaddanki/Documents/Forge/to-do/prompt-list.md`

Extra files at repo root (will need .gitignore or cleanup):
- `Plang.md` — directory layout doc (not needed in final repo)
- `language-keywords.txt` — design notes (not needed in final repo)
- `Ohm-Script.ohm` — draft grammar (will be moved to Plang/src/forge.ohm)

---

## Compiler Pipeline Status

| Component | File | Status | CLI flag | Tests |
|---|---|---|---|---|
| Entry point | `src/forge.js` | EMPTY | — | — |
| Grammar | `src/forge.ohm` | DRAFT (50% complete) | — | — |
| Parser | `src/parser.js` | EMPTY | `--parse` | EMPTY |
| Core model | `src/core.js` | EMPTY | — | — |
| Analyzer | `src/analyzer.js` | EMPTY | `--analyze` | EMPTY |
| Optimizer | `src/optimizer.js` | EMPTY | `--optimize` | EMPTY |
| Generator | `src/generator.js` | EMPTY | `--generate` | EMPTY |
| CLI | `src/compiler.js` | EMPTY | all modes | EMPTY |

CLI modes required: syntax-check, parse, analyze, optimize, generate

---

## Testing Status

Test command: `npm test` (not yet configured)
Coverage command: to be configured with `c8` or `nyc` in package.json

Current test count: **0**
Current coverage: **0%**
Target: **100% coverage, ≥50 tests (aim for hundreds)**

Known test gaps: Everything — all test files are empty.

---

## Examples Status

Required: at least 5 complete, semantically correct programs covering all syntactic forms.

| # | File | Status | Covers |
|---|---|---|---|
| 1 | TBD | MISSING | — |
| 2 | TBD | MISSING | — |
| 3 | TBD | MISSING | — |
| 4 | TBD | MISSING | — |
| 5 | TBD | MISSING | — |

---

## README Status (`Plang/README.md`)

| Section | Status |
|---|---|
| Logo | MISSING (asset exists at `public/Images/Logo.png`) |
| Language name (large font) | MISSING |
| One-paragraph story | MISSING |
| Team member names | MISSING (unknown — add TODO) |
| Feature list | MISSING |
| Static checks description | MISSING |
| Example programs | MISSING |
| Generated JavaScript sample | MISSING |
| Grammar link | MISSING |
| Companion website link | MISSING |
| Setup/test instructions | MISSING |
| Accurate implementation status | MISSING |

Source material: adapt from `README.md` at repo root (language spec).

---

## Companion Website Status (`Plang/docs/`)

| Requirement | Status |
|---|---|
| Pretty CSS / template | MISSING |
| Story in ≤3 paragraphs | MISSING |
| At least 5 example programs | MISSING |
| Developer names and bios | MISSING |
| Link to GitHub repo | MISSING |
| Static constraints list | MISSING |
| README links to site | MISSING |

---

## Presentation Status

| Requirement | Status |
|---|---|
| 5-minute lightning talk structure | MISSING |
| Each member introduction cue | MISSING |
| Language story | MISSING |
| Feature showcase slides | MISSING |
| Compiler/static-analysis highlight | MISSING |
| Code snippet examples | MISSING |
| Memorable intro/outro | MISSING |
| File stored in repo | MISSING |

---

## Known Risks

| Risk | Severity | Notes |
|---|---|---|
| All source files empty — large implementation gap | HIGH | 12 days, significant daily work needed |
| Bit indexing syntax `x{3}` may conflict with Ohm rules | MEDIUM | May need creative grammar workaround |
| Team member names unknown | MEDIUM | Required in README, package.json, website, affidavits |
| Custom type (enum) access forms are complex | MEDIUM | 4 access modes, hub compatibility |
| `whento` for-loop syntax tricky in Ohm | LOW-MEDIUM | Semicolons inside parens need careful grammar |
| Overloading by arity was originally in spec — cut | LOW | Simplifies analyzer significantly |
| Website/GH Pages not set up | LOW | Day 7 task |
| Coverage tooling not configured | LOW | Fix in package.json Day 1 |

---

## Verification Commands

Run every session before claiming progress:

```bash
cd /Users/nityaaddanki/Documents/Forge/Plang
npm test
npm run format   # when configured
```

CLI verification (run when compiler is functional):
```bash
node src/forge.js --check examples/example1.forge
node src/forge.js --parse examples/example1.forge
node src/forge.js --analyze examples/example1.forge
node src/forge.js --optimize examples/example1.forge
node src/forge.js --generate examples/example1.forge
```

---

## 12-Day Sprint Plan

| Day | Date | Goal | Must Deliver |
|---|---|---|---|
| 1 | 2026-04-22 | Scope lock + repo foundation | Clean structure, package.json, README skeleton, .gitignore, npm test runs |
| 2 | 2026-04-23 | Grammar + parser | forge.ohm complete, parser.js working, parser tests written |
| 3 | 2026-04-24 | Core model + analyzer foundation | core.js, scope/symbol table, basic static checks |
| 4 | 2026-04-25 | Analyzer completion | All 16 static checks, heavy test suite |
| 5 | 2026-04-26 | Generator + optimizer + full CLI | JS output works, optimizations, all CLI modes |
| 6 | 2026-04-27 | Tests + coverage push | ≥50 tests, coverage report, 5+ examples |
| 7 | 2026-04-28 | Buffer / catch-up day | Fix any failing pipeline layers |
| 8 | 2026-04-29 | README + examples polish | README complete, all 5+ examples run through pipeline |
| 9 | 2026-04-30 | Website | GitHub Pages site live |
| 10 | 2026-05-01 | Presentation | Slides in repo |
| 11 | 2026-05-02 | Final audit + cleanup | All checklist items, repo pristine |
| 12 | 2026-05-03 | Buffer / submission prep | Brightspace submission text ready |

---

## Next-Session Checklist (Day 1)

- [ ] Rename `Plang/src/(yourlanguagename).js` → `Plang/src/forge.js`
- [ ] Rename `Plang/src/(yourlanguagename).ohm` → `Plang/src/forge.ohm`
- [ ] Fill `Plang/package.json` (name, type:module, test with coverage, author TODOs, repo)
- [ ] Create `Plang/.gitignore` (node_modules, coverage, .DS_Store, build output)
- [ ] Copy draft grammar from repo-root `Ohm-Script.ohm` into `Plang/src/forge.ohm` as starting point
- [ ] Copy logo from `public/Images/Logo.png` into `Plang/docs/` or `Plang/src/`
- [ ] Write `Plang/README.md` skeleton with required sections
- [ ] Create placeholder example files in `Plang/examples/`
- [ ] Verify `npm test` runs (even if 0 tests pass)
- [ ] Run `git status` and ensure .gitignore is correct
- [ ] Confirm language scope is frozen (do not expand after today)
- [ ] Update CLAUDE.md with all decisions made

---

## Latest Session Notes

**Date:** 2026-04-22 (Session 0 — Bootstrap)

**Changed files:**
- Created: `/Users/nityaaddanki/Documents/Forge/CLAUDE.md` (this file)

**Verification results:**
- npm test: NOT RUN (package.json empty, test files empty)
- Coverage: NOT RUN
- CLI: NOT RUN (all source files empty)

**Blockers:**
- All source files empty
- package.json unconfigured
- Team member names unknown

**Next tasks:**
- Day 1: repo foundation (see checklist above)
- Run Day 1 prompt from `to-do/prompt-list.md`
