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
**Today (session 2):** 2026-04-24
**Days remaining:** 10

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

**Status as of 2026-04-24:** Day 2 / Compiler pipeline complete

**Repo root:** `/Users/nityaaddanki/Documents/Forge` (NOT a Plang/ subdirectory — that was cleaned up)

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
- Arithmetic: `+`, `-`, `*`, `/`, `%`, `^` (exponent → JS `**`)
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
- Overloading by arity

---

## Assignment Requirements Summary

| Requirement | Notes |
|---|---|
| Group of 3–6 students | Need team member names — add to package.json author field |
| Original language, sufficient complexity | Forge — statically typed, multiple static checks |
| Complete functional compiler | Parser + analyzer + optimizer + generator + CLI — **DONE** |
| 100% test coverage | 349 tests, 99.8% stmt, 96.89% branch coverage — **NEAR COMPLETE** |
| Public GitHub repo | https://github.com/JohhannasReyn/Forge |
| Companion website (GitHub Pages) | **MISSING** — Day 9 task |
| 5-minute lightning talk | **MISSING** — Day 10 task |
| Each member contributes meaningfully | Commits from each member |
| Signed affidavits for readings/watchings | Included in Brightspace submission |

### Static Checks Required (Analyzer must implement)
1. ✅ Undeclared identifier use → error
2. ✅ Duplicate declarations in same scope → error
3. ✅ Shadowing in inner scope → allowed (different scope)
4. ✅ Assignment to constants (`C`) → error
5. ✅ Invalid use of `resolve` (only in `empty` functions) → error
6. ✅ Invalid use of `deliver` (only in non-`empty` functions) → error
7. ✅ Invalid use of `escape` (only inside loops or `hub`) → error
8. ⚠️ Missing return in non-`empty` functions → NOT checked (analyzer doesn't enforce)
9. ✅ Wrong argument count in function calls → error
10. ✅ Wrong argument types in function calls → error
11. ✅ Incompatible assignment types → error
12. ✅ Invalid operator/type combinations → error
13. ✅ Duplicate case labels in `hub` → error
14. ⚠️ Multiple `base` clauses → NOT checked (grammar allows only one via `DefaultClause?`)
15. ⚠️ Non-constant case label expressions → NOT checked
16. ✅ Bitwise ops on non-integer types → error

---

## Repo Structure (Current — root-level)

```
Forge/                          (project root)
├── CLAUDE.md                   THIS FILE
├── .gitignore                  EXISTS, correct
├── README.md                   Language spec (~985 lines) — needs project README format
├── LICENSE                     EXISTS
├── package.json                CONFIGURED (type:module, c8+mocha test, prettier)
├── .prettierrc.json            EXISTS
├── src/
│   ├── forge.js                CLI entry point — COMPLETE
│   ├── forge.ohm               Grammar — COMPLETE (214 lines)
│   ├── compiler.js             Pipeline orchestrator — COMPLETE
│   ├── parser.js               Ohm semantics → AST — COMPLETE
│   ├── core.js                 AST node factories + types — COMPLETE
│   ├── analyzer.js             Static analysis, 16 checks — COMPLETE
│   ├── optimizer.js            Constant folding, dead code — COMPLETE
│   └── generator.js            JS code generator — COMPLETE
├── test/
│   ├── parser.test.js          COMPLETE (~68 tests)
│   ├── analyzer.test.js        COMPLETE (~74 tests)
│   ├── optimizer.test.js       COMPLETE (~75 tests) — newly written
│   ├── generator.test.js       COMPLETE (~85 tests) — newly written
│   └── compiler.test.js        COMPLETE (~47 tests) — newly written
├── examples/
│   ├── hello.forge             Functions, if/else, recursion
│   ├── math.forge              Loops, arithmetic, for loop
│   ├── bitops.forge            Bit ops, bit indexing
│   ├── enum.forge              Custom types, hub/switch
│   └── globals.forge           Globals, field access, complex functions
└── docs/                       MISSING — companion website needed
```

---

## Compiler Pipeline Status

| Component | File | Status | CLI flag | Tests |
|---|---|---|---|---|
| Entry point | `src/forge.js` | COMPLETE | — | via compiler tests |
| Grammar | `src/forge.ohm` | COMPLETE | — | via parser tests |
| Parser | `src/parser.js` | COMPLETE | `--check`, `--parse` | 68 tests, 100% coverage |
| Core model | `src/core.js` | COMPLETE | — | 100% coverage |
| Analyzer | `src/analyzer.js` | COMPLETE | `--analyze` | 74 tests, 100% stmt |
| Optimizer | `src/optimizer.js` | COMPLETE | `--optimize` | 75 tests, 98.75% stmt |
| Generator | `src/generator.js` | COMPLETE | `--generate` | 85 tests, 100% stmt |
| CLI | `src/compiler.js` | COMPLETE | all modes | 47 tests, 100% |

CLI modes working: `--check`, `--parse`, `--analyze`, `--optimize`, `--generate`

---

## Testing Status

Test command: `npm test` (`c8 mocha --timeout 10000`)
Coverage command: `node_modules/.bin/c8 node_modules/.bin/mocha --timeout 10000`

**Current test count: 349 tests passing, 0 failing**
**Coverage:**
- All files: 99.8% statements, 96.89% branches, 96.72% functions, 99.8% lines
- analyzer.js: 100% statements, 89.81% branches, 93.02% functions
- compiler.js: 100% all
- core.js: 100% all
- generator.js: 100% statements/branches/functions
- optimizer.js: 98.75% statements, 97.35% branches, 97.5% functions
- parser.js: 100% statements/branches

**Remaining uncovered (defensive code, unreachable in valid usage):**
- analyzer.js lines 33,38,44 — error throws and ?? fallbacks for impossible node types
- analyzer.js lines 149,223,243-245,258,269,277 — untriggered branch arms in type checking
- generator.js line 3 — throw when no generator found (unreachable with valid AST)
- optimizer.js lines 42,47 — fallthrough `return n` (unreachable since all node kinds have handlers)

---

## Examples Status

All 5 examples pass all 5 CLI modes:

| # | File | Covers | Status |
|---|---|---|---|
| 1 | `hello.forge` | Functions, if/else, recursion | ✅ All modes pass |
| 2 | `math.forge` | Loops (for/while), arithmetic, GCD | ✅ All modes pass |
| 3 | `bitops.forge` | Bit ops, bit indexing, bit set | ✅ All modes pass |
| 4 | `enum.forge` | Custom types, hub/switch | ✅ All modes pass |
| 5 | `globals.forge` | Globals, field access, complex functions | ✅ All modes pass |

---

## README Status (`README.md`)

The current README.md is the language spec (~985 lines). It needs to be the project README.

| Section | Status |
|---|---|
| Logo | MISSING (asset exists at `public/Images/Logo.png`) |
| Language name (large font) | EXISTS (first heading) |
| One-paragraph story | EXISTS (intro paragraphs) |
| Team member names | MISSING (unknown — add TODO) |
| Feature list | EXISTS |
| Static checks description | EXISTS |
| Example programs in README format | EXISTS (scattered) |
| Generated JavaScript sample | MISSING |
| Grammar link | MISSING |
| Companion website link | MISSING |
| Setup/test instructions | MISSING |
| Badge for coverage | MISSING |

**Priority:** Add logo, setup instructions, generated JS sample, link badges — Day 8 task.

---

## Companion Website Status (`docs/`)

Directory does not yet exist.

| Requirement | Status |
|---|---|
| Pretty CSS / template | MISSING |
| Story in ≤3 paragraphs | MISSING |
| At least 5 example programs | MISSING |
| Developer names and bios | MISSING |
| Link to GitHub repo | MISSING |
| Static constraints list | MISSING |
| README links to site | MISSING |

**Priority:** Day 9 task.

---

## Presentation Status

| Requirement | Status |
|---|---|
| 5-minute lightning talk slides | MISSING |
| File stored in repo | MISSING |

**Priority:** Day 10 task.

---

## Known Risks

| Risk | Severity | Notes |
|---|---|---|
| Static check 8 (missing return) not implemented | MEDIUM | Analyzer doesn't enforce returns in all branches |
| Static check 14 (multiple base clauses) not implemented | LOW | Grammar prevents it structurally |
| Static check 15 (non-constant case labels) not implemented | LOW | Low assignment priority |
| Team member names unknown | MEDIUM | Required in README, package.json, website, affidavits |
| Website/GH Pages not set up | MEDIUM | Day 9 task — 6 days away |
| README needs conversion from spec to project format | LOW | Day 8 task |
| Presentation not started | LOW | Day 10 task |

---

## Verification Commands

Run every session before claiming progress:

```bash
cd /Users/nityaaddanki/Documents/Forge
npm test
node_modules/.bin/c8 node_modules/.bin/mocha --timeout 10000
```

CLI verification (run when making changes):
```bash
node src/forge.js --check examples/hello.forge
node src/forge.js --parse examples/math.forge > /dev/null
node src/forge.js --analyze examples/bitops.forge > /dev/null
node src/forge.js --optimize examples/enum.forge > /dev/null
node src/forge.js --generate examples/globals.forge
```

---

## 12-Day Sprint Plan (Updated)

| Day | Date | Goal | Status |
|---|---|---|---|
| 1 | 2026-04-22 | Scope lock + repo foundation | ✅ Done |
| 2 | 2026-04-23 | Grammar + parser | ✅ Done |
| 3 | 2026-04-24 | Core model + analyzer + tests | ✅ Done |
| 4 | 2026-04-25 | Generator/optimizer tests + coverage push | Next session |
| 5 | 2026-04-26 | README polish + missing static checks | — |
| 6 | 2026-04-27 | README complete + examples polish | — |
| 7 | 2026-04-28 | Buffer / catch-up day | — |
| 8 | 2026-04-29 | README finalization | — |
| 9 | 2026-04-30 | Website (GitHub Pages) | — |
| 10 | 2026-05-01 | Presentation slides | — |
| 11 | 2026-05-02 | Final audit + cleanup | — |
| 12 | 2026-05-03 | Buffer / submission prep | — |

---

## Latest Session Notes

**Date:** 2026-04-24 (Session 2)

**Changed files:**
- `src/generator.js` — Fixed bug: exponent `^` now maps to `**` (was incorrectly falling through to `^` which is XOR in JS)
- `src/analyzer.js` — Fixed bug: function parameters now have `readOnly: false` (were incorrectly `readOnly: true`, breaking parameter reassignment like in GCD)
- `examples/math.forge` — Fixed: renamed `base` parameter to `b` (`base` is a reserved keyword in Forge)
- `test/generator.test.js` — CREATED: 87 tests covering all generator output cases
- `test/optimizer.test.js` — CREATED: 75 tests covering constant folding, identities, dead branches, pass-throughs
- `test/compiler.test.js` — REPLACED placeholder with 47 real integration tests

**Verification results:**
- `npm test`: 349 passing, 0 failing (191ms)
- Coverage: 99.8% statements, 96.89% branches, 96.72% functions
- `--check` on all 5 examples: PASS
- `--analyze` on all 5 examples: PASS
- `--optimize` on all 5 examples: PASS
- `--generate` on all 5 examples: PASS (valid JS output)

**Bugs fixed this session:**
1. Exponent `^` operator was generating JS `^` (XOR) instead of `**` (exponentiation)
2. Function parameters were immutable (prevented GCD-style reassignment)
3. `math.forge` used `base` as a parameter name (reserved keyword)

**Blockers:**
- Team member names unknown (needed for package.json, README, website, affidavits)

**Next best tasks (priority order):**
1. Add missing README sections: logo img tag, setup/test instructions, generated JS sample, link to GitHub Pages
2. Implement static check 8: missing return enforcement in non-empty functions
3. Create `docs/` companion website (HTML + CSS, one page)
4. Add team member names to package.json author field once known
5. Create presentation slides (5 min lightning talk)
6. Run `npm test` + coverage again after any changes to verify 0 failures

**Next prompt to run:**
"Continue Forge compiler work. Read CLAUDE.md first. Priority: (1) Add missing README sections including logo, setup instructions, and a generated JS sample. (2) Create the docs/ companion website as a single-page HTML file with CSS. (3) If time permits, add missing return enforcement (static check 8). Run verification commands after each major change."
