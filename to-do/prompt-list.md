Below is a set of **copy/paste prompts for Claude Code with Superpowers enabled**, structured as 7 sessions. Each prompt tells Claude to:

- read `CLAUDE.md` at the beginning
- update `CLAUDE.md` at the end with memory, decisions, next tasks, and verification results
- automate progress through tests/checklists
- verify before claiming completion
- keep the project aligned with the assignment spec PDF

You can run one prompt per day, or run multiple in sequence if you’re moving fast.

**Prompt 0: Project Bootstrap / Persistent Memory Setup**

Use this first before Day 1.

```text
You are working in the repo at C:\Projects\Forge.

Superpowers are enabled. Use the relevant Superpowers skills before acting, especially:
- using-superpowers
- brainstorming, if scope/design decisions are needed
- writing-plans, if making or revising a plan
- test-driven-development, when implementing features
- systematic-debugging, when tests fail
- verification-before-completion, before claiming anything is done

At the beginning of this session:
1. Open and read CLAUDE.md if it exists.
2. If CLAUDE.md does not exist, create it.
3. Also read the assignment spec at:
   C:\Projects\Forge\pdf_lib\CMSI 3802_ Languages and Automata II_ Homework #1.pdf
4. Read the existing README, language spec, grammar, package.json, src, test, docs, and examples directories.

Update CLAUDE.md so it becomes the project memory file. It should include:
- Project goal: complete the CMSI 3802 language/compiler assignment in 7 days.
- Due date: May 4, 2026.
- Language name and current status.
- Assignment requirements summary.
- Current repo structure status.
- Frozen feature scope once decided.
- Compiler pipeline status: parser, analyzer, optimizer, generator, CLI.
- Testing and coverage status.
- Website status.
- Presentation status.
- Known risks.
- Next-session checklist.
- Verification commands to run every session.

Important workflow rules:
- At the beginning of every future session, read CLAUDE.md first.
- At the end of every session, update CLAUDE.md with completed work, changed files, test results, coverage status, blockers, and next steps.
- Do not claim completion unless verification commands were run.
- Keep edits scoped to the assignment.
- Do not add new language features after Day 1 unless absolutely necessary.
- Keep README, examples, and companion site accurate to what is actually implemented.

After setting up CLAUDE.md, produce a short project status report:
- What exists
- What is missing
- Highest-risk areas
- Recommended Day 1 tasks
```

**Prompt 1: Day 1, Scope Lock + Repo Foundation**

```text
You are working in C:\Projects\Forge with Superpowers enabled.

Before doing anything:
1. Read CLAUDE.md.
2. Read the assignment spec PDF:
   C:\Projects\Forge\pdf_lib\CMSI 3802_ Languages and Automata II_ Homework #1.pdf
3. Read README.md, Plang.md, Ohm-Script.ohm, package.json if present, and the current project structure.
4. Use relevant Superpowers skills before acting.

Today’s objective:
Complete Day 1 of the 7-day plan: scope lock, repo cleanup, and project foundation.

Tasks:
1. Determine the actual project root. If the real project is inside C:\Projects\Forge\Plang, decide whether to work there or migrate/rename files so the repo matches the assignment structure cleanly.
2. Make the required structure match the spec:
   - .gitignore
   - README.md
   - LICENSE
   - package.json
   - optional .prettierrc.json
   - docs/
   - examples/
   - src/
   - test/
3. Rename placeholder files like "(yourlanguagename).js" and "(yourlanguagename).ohm" to the real language name, likely Forge unless CLAUDE.md says otherwise.
4. Configure package.json:
   - project name
   - "type": "module"
   - author field with team members if known, or TODO placeholders if unknown
   - repository metadata if known
   - test script with coverage
   - format script if useful
5. Lock the Day 1 language scope. Do not over-expand. Decide which language features are in-scope for a 7-day finish.
6. Update README with:
   - language name
   - logo placeholder if no real logo exists
   - one-paragraph story
   - feature list
   - static checks list
   - setup/test instructions
   - implementation status table
   - companion website placeholder
7. Create or clean examples/ with placeholder or draft example programs if useful.
8. Make sure .gitignore excludes node_modules, coverage, build output, OS junk, and temp files.

Verification:
- Run npm install only if dependencies are missing and package.json requires it.
- Run npm test.
- Run any available lint/format/check command.
- Inspect git status.
- Do not claim the project foundation is complete unless these were run or explain exactly why they could not run.

At the end:
1. Update CLAUDE.md with:
   - decisions made
   - frozen language scope
   - files changed
   - verification commands and results
   - remaining risks
   - Day 2 checklist
2. Give me a concise report with:
   - completed items
   - failed verification or blockers
   - exact next prompt to run
```

**Prompt 2: Day 2, Grammar + Parser**

```text
You are working in C:\Projects\Forge with Superpowers enabled.

Start-of-session protocol:
1. Read CLAUDE.md first.
2. Read the assignment spec PDF only as needed for parser/compiler requirements.
3. Read the current grammar file, parser file, compiler CLI file, README examples, and parser tests.
4. Use relevant Superpowers skills before acting, especially test-driven-development and verification-before-completion.

Today’s objective:
Complete Day 2: grammar and parser.

Tasks:
1. Finalize the Ohm grammar for the frozen Day 1 language scope.
2. Ensure the grammar supports all intended syntax:
   - functions
   - variable declarations
   - constants
   - assignments
   - returns / resolve / deliver
   - conditionals
   - loops
   - switch / hub if in scope
   - expressions and precedence
   - literals
   - indexing/slicing if in scope
   - comments and whitespace
3. Implement or repair src/parser.js.
4. Connect parser behavior to the compiler CLI:
   - syntax check mode
   - parse mode
5. Write parser tests before or alongside implementation:
   - valid programs
   - invalid syntax
   - expression precedence
   - nested blocks
   - each major syntactic form
6. Add or revise at least 3 example programs that parse successfully.

Verification:
- Run npm test.
- Run parser-specific tests if available.
- Run CLI syntax check against example programs.
- Run CLI parse mode against example programs.
- Confirm examples parse.
- Inspect coverage if configured.

At the end:
1. Update CLAUDE.md with:
   - grammar status
   - parser status
   - CLI parser modes status
   - tests added
   - examples that parse
   - verification results
   - Day 3 checklist
2. Report:
   - what syntax is now supported
   - what remains risky
   - whether Day 2 is complete
```

**Prompt 3: Day 3, Core Model + Analyzer Foundation**

```text
You are working in C:\Projects\Forge with Superpowers enabled.

Start-of-session protocol:
1. Read CLAUDE.md first.
2. Read src/core.js, src/parser.js, src/analyzer.js, parser tests, analyzer tests, and README static checks.
3. Use relevant Superpowers skills before acting, especially test-driven-development and systematic-debugging if anything fails.

Today’s objective:
Complete Day 3: core AST/semantic model and analyzer foundation.

Tasks:
1. Design or clean up src/core.js to represent:
   - program
   - declarations
   - statements
   - expressions
   - types
   - functions
   - variables/constants
   - scopes or entities if appropriate
2. Ensure parser output can be transformed into the core model.
3. Implement analyzer foundation:
   - context/scope management
   - symbol table
   - declaration analysis
   - duplicate declaration checks
   - undeclared identifier checks
   - const reassignment checks
   - basic expression analysis
   - basic function declaration handling
4. Add analyzer tests for:
   - valid declarations
   - duplicate names
   - undeclared identifiers
   - nested scopes
   - const reassignment
   - simple valid functions
   - simple invalid functions
5. Keep error messages clean and specific.

Verification:
- Run npm test.
- Run analyzer-specific tests.
- Run parser tests to ensure no regression.
- Run CLI analyze mode if already available, or add it if straightforward.
- Test at least one valid and one invalid program from examples or fixtures.

At the end:
1. Update CLAUDE.md with:
   - core model decisions
   - analyzer foundation status
   - static checks now implemented
   - tests added
   - verification results
   - Day 4 checklist
2. Report:
   - what static checks work
   - what analyzer work remains
   - known risks
```

**Prompt 4: Day 4, Analyzer Completion**

```text
You are working in C:\Projects\Forge with Superpowers enabled.

Start-of-session protocol:
1. Read CLAUDE.md first.
2. Read src/analyzer.js, src/core.js, grammar, README static checks, examples, and analyzer tests.
3. Use relevant Superpowers skills before acting, especially test-driven-development, systematic-debugging, and verification-before-completion.

Today’s objective:
Complete Day 4: finish static analysis, the most important assignment layer.

Tasks:
1. Finish all in-scope static checks from CLAUDE.md and README.
2. Implement checks for:
   - type compatibility
   - assignment compatibility
   - operator operand validity
   - boolean/condition expression validity
   - function call parameter count
   - function call argument types if typed
   - return / resolve / deliver validity
   - missing returns where applicable
   - escape/break only inside loops or switches
   - switch/hub case validity if in scope
   - indexing/slicing validity if in scope
   - null/non usage rules if any
3. Ensure the language has enough static complexity to satisfy the assignment.
4. Expand analyzer tests heavily:
   - good programs
   - bad programs
   - one test per static rule
   - nested edge cases
5. Update README static/safety/security checks so it matches actual implementation.
6. Add invalid example snippets as tests, not necessarily as public examples.

Verification:
- Run npm test.
- Run coverage.
- Run analyze mode against every complete example program.
- Confirm bad analyzer fixtures fail with expected messages.
- Confirm no parser/generator regressions if generator exists.

At the end:
1. Update CLAUDE.md with:
   - completed static checks
   - analyzer remaining gaps, if any
   - test count
   - coverage status
   - examples analyzed successfully
   - verification results
   - Day 5 checklist
2. Report:
   - whether analyzer is complete
   - evidence from tests
   - any assignment-risk items
```

**Prompt 5: Day 5, Generator + Optimizer + Full CLI**

```text
You are working in C:\Projects\Forge with Superpowers enabled.

Start-of-session protocol:
1. Read CLAUDE.md first.
2. Read src/generator.js, src/optimizer.js, src/compiler.js, src/core.js, src/analyzer.js, tests, and examples.
3. Use relevant Superpowers skills before acting, especially test-driven-development and verification-before-completion.

Today’s objective:
Complete Day 5: generation, optimization, and full compiler CLI pipeline.

Tasks:
1. Implement or finish src/generator.js to produce readable JavaScript.
2. Add generator support for every analyzed in-scope construct.
3. Implement or finish src/optimizer.js with a few non-trivial optimizations, such as:
   - constant folding
   - algebraic simplification
   - dead code after return
   - unreachable branch simplification
   - identity operation removal
4. Ensure optimizer preserves program meaning.
5. Connect compiler CLI modes:
   - syntax check
   - parse
   - analyze
   - optimize
   - generate
6. Add generator tests:
   - each statement type
   - expressions
   - functions
   - control flow
   - generated code readability
7. Add optimizer tests:
   - each optimization
   - cases that must not optimize incorrectly
8. Add compiler integration tests for full pipeline.
9. Add generated-code examples to README or prepare them for the website.

Verification:
- Run npm test.
- Run coverage.
- Run compiler CLI modes on each example program.
- Generate JavaScript for each complete example.
- If possible, execute generated JavaScript and confirm expected output/behavior.
- Inspect generated code for readability.

At the end:
1. Update CLAUDE.md with:
   - generator status
   - optimizer status
   - CLI mode status
   - examples successfully generated
   - verification results
   - Day 6 checklist
2. Report:
   - full pipeline status
   - optimizations implemented
   - remaining gaps
```

**Prompt 6: Day 6, Tests + Coverage + README + Examples**

```text
You are working in C:\Projects\Forge with Superpowers enabled.

Start-of-session protocol:
1. Read CLAUDE.md first.
2. Read all test files, examples, README, package.json, compiler source files, and coverage config.
3. Use relevant Superpowers skills before acting, especially verification-before-completion and systematic-debugging for failures.

Today’s objective:
Complete Day 6: test coverage, examples, README, and repo polish.

Tasks:
1. Ensure there are at least 50 tests.
2. Push toward 100% test coverage.
3. Add tests for uncovered branches and edge cases:
   - parser
   - analyzer
   - optimizer
   - generator
   - compiler CLI
4. Ensure examples/ has at least 5 complete, semantically correct programs.
5. Make examples cover:
   - all syntactic forms
   - most major static checks
   - interesting language features
6. Run each example through the full compiler pipeline.
7. Finish README:
   - logo
   - large language name
   - one-paragraph story
   - team names or TODOs if unknown
   - feature list
   - static/safety/security checks
   - setup instructions
   - CLI usage
   - example programs
   - generated JavaScript sample
   - grammar link
   - companion site link or placeholder
   - implementation status warnings for anything incomplete
8. Run Prettier/formatting.
9. Remove unnecessary files from repo.

Verification:
- Run npm test.
- Run coverage and record percentage.
- Run format/check script.
- Run compiler on all examples.
- Inspect git status.
- Confirm no node_modules, coverage output, or junk files are tracked unless intentionally ignored.

At the end:
1. Update CLAUDE.md with:
   - test count
   - coverage percentage
   - examples list and status
   - README status
   - repo cleanup status
   - verification results
   - Day 7 checklist
2. Report:
   - whether testing target is met
   - whether README is grade-ready
   - any remaining risks before final day
```

**Prompt 7: Day 7, Website + Presentation + Final Submission**

```text
You are working in C:\Projects\Forge with Superpowers enabled.

Start-of-session protocol:
1. Read CLAUDE.md first.
2. Read assignment spec PDF sections on companion website, presentation, checklists, and submission.
3. Read README, docs, examples, package.json, and current test/coverage status.
4. Use relevant Superpowers skills before acting, especially verification-before-completion.

Today’s objective:
Complete Day 7: companion website, presentation materials, final repo verification, and submission package.

Tasks:
1. Build or finish the GitHub Pages companion website.
2. Website must include:
   - attractive CSS
   - story of the language in no more than 3 paragraphs
   - at least 5 complete example programs
   - developer names and short bios, using TODO placeholders only if names are unknown
   - link to the GitHub repo
   - list of static constraints/checks
   - generated-code example if useful
3. Add a link from README to the companion website.
4. Prepare presentation materials in the repo:
   - slides, markdown slides, or another visual aid
   - 5-minute lightning talk structure
   - each team member introduction cue
   - language story
   - feature showcase
   - compiler/static-analysis highlight
   - example snippets
   - memorable closing
5. Prepare final Brightspace submission text with:
   - every student name
   - affidavit line for each student confirming individual readings/watchings
   - public GitHub repo link
   - note that repo links to website and presentation
6. Final repo cleanup.
7. Final verification pass.

Verification:
- Run npm test.
- Run coverage.
- Run format/check.
- Run compiler pipeline on all examples.
- Verify README links work or are correctly marked.
- Verify website can be opened locally.
- Verify presentation file exists in repo.
- Inspect git status.
- Confirm no unnecessary files are present.

At the end:
1. Update CLAUDE.md with:
   - final status of every assignment layer
   - final test count
   - final coverage
   - final examples list
   - website path/link
   - presentation path
   - submission text location or content
   - unresolved TODOs, if any
2. Report:
   - final project readiness
   - exact verification results
   - remaining manual actions, such as publishing GitHub Pages or filling real team names
```

**Optional Prompt: Continuous Progress Automation**

Use this between days if you want Claude to inspect progress and choose the next best task automatically.

```text
You are working in C:\Projects\Forge with Superpowers enabled.

Start by reading CLAUDE.md. Treat it as persistent project memory.

Your job in this session is to automatically advance the project toward completion based on the current 7-day plan.

Process:
1. Read CLAUDE.md.
2. Inspect the repo.
3. Compare current status against the assignment spec and the 7-day sprint plan.
4. Identify the highest-priority incomplete layer.
5. Work on that layer using the relevant Superpowers skills.
6. Prefer finishing incomplete required work over adding new features.
7. Keep the language scope frozen unless CLAUDE.md explicitly says a change is allowed.
8. Write or update tests for any behavior you implement.
9. Run verification before claiming progress.

Priority order:
1. Working compiler pipeline
2. Analyzer/static checks
3. Tests and coverage
4. Five complete examples
5. README accuracy
6. Companion website
7. Presentation and submission polish

At the end:
1. Update CLAUDE.md with completed work, files changed, test results, coverage, blockers, and next best task.
2. Report what changed and what should be done next.
```

**Optional Prompt: Final Assignment Checklist Audit**

Use this before submission.

```text
You are working in C:\Projects\Forge with Superpowers enabled.

Read CLAUDE.md first, then audit the project against the assignment spec PDF:
C:\Projects\Forge\pdf_lib\CMSI 3802_ Languages and Automata II_ Homework #1.pdf

Do not implement new features unless the audit reveals a required missing piece that is small enough to fix safely.

Create an audit checklist covering:
- Project structure
- GitHub repo requirements
- README requirements
- package.json requirements
- parser
- analyzer
- optimizer
- generator
- compiler CLI
- tests and coverage
- examples
- code style
- companion website
- presentation
- participation/submission materials

For each item, mark:
- PASS
- FAIL
- PARTIAL
- NOT APPLICABLE

Then fix any small, safe FAIL/PARTIAL items.

Verification:
- Run npm test.
- Run coverage.
- Run format/check.
- Run compiler pipeline on all examples.
- Check git status.

At the end:
1. Update CLAUDE.md with the final audit result.
2. Provide a concise readiness report and list only the remaining manual tasks.
```

**CLAUDE.md Template**

You can also give Claude this template directly if it creates `CLAUDE.md` from scratch:

```markdown
# Forge Project Memory

## Session Protocol

At the beginning of every Claude Code session:
1. Read this file first.
2. Read the assignment spec if requirements are unclear:
   `C:\Projects\Forge\pdf_lib\CMSI 3802_ Languages and Automata II_ Homework #1.pdf`
3. Inspect current repo status before editing.
4. Use relevant Superpowers skills.
5. Prefer completing required assignment layers over adding new features.

At the end of every session:
1. Update this file.
2. Record changed files.
3. Record verification commands and results.
4. Record next best tasks.
5. Do not claim completion without verification.

## Assignment Goal

Complete the CMSI 3802 Languages and Automata II Homework #1 term project by May 4, 2026.

The project must include:
- original programming language of sufficient complexity
- complete compiler
- parser
- analyzer with significant static analysis
- optimizer
- generator
- command-line compiler behavior like Carlos
- 100% test coverage target
- public GitHub repo
- README
- companion GitHub Pages website
- examples
- presentation
- team affidavits for readings/watchings

## Frozen Language Scope

Status: NOT YET FINALIZED

In-scope features:
- TODO

Out-of-scope features:
- TODO

Do not add new features after Day 1 unless required for assignment completion.

## Compiler Pipeline Status

Parser:
- Status:
- Files:
- Tests:

Analyzer:
- Status:
- Static checks implemented:
- Tests:

Optimizer:
- Status:
- Optimizations implemented:
- Tests:

Generator:
- Status:
- Target:
- Tests:

CLI:
- Syntax check:
- Parse:
- Analyze:
- Optimize:
- Generate:

## Testing Status

Test command:
```bash
npm test
```

Coverage command:
```bash
TODO
```

Current test count:
- TODO

Current coverage:
- TODO

Known test gaps:
- TODO

## Examples Status

Required: at least 5 complete, semantically correct examples.

Examples:
1. TODO
2. TODO
3. TODO
4. TODO
5. TODO

Each example should parse, analyze, optimize, and generate.

## README Status

Required sections:
- Logo:
- Language name:
- Story:
- Team names:
- Features:
- Static checks:
- Examples:
- Generated JavaScript:
- Grammar link:
- Companion website link:
- Accurate implementation status:

## Companion Website Status

Required:
- Pretty CSS:
- Story in <= 3 paragraphs:
- At least 5 examples:
- Developer bios:
- Repo link:
- Static checks:
- README links to site:

## Presentation Status

Required:
- 5-minute lightning talk:
- Each member introduction:
- Language intro:
- Story:
- Visual aids:
- Interesting snippets:
- Structure:
- Memorable intro/outro:
- File stored in repo:

## Submission Status

Brightspace submission must include:
- Student names:
- Individual affidavit for each student:
- Public GitHub repo link:
- Repo links to website and presentation:

## Verification Commands

Run as applicable:
```bash
npm test
npm run format
npm run check
npm run coverage
```

Also run compiler CLI against every example program.

## Latest Session Notes

Date:
- TODO

Changed files:
- TODO

Verification results:
- TODO

Blockers:
- TODO

Next tasks:
- TODO
```

My suggestion: use **Prompt 0 once**, then run **Prompts 1-7 in order**. If Claude gets stuck or you lose track, use the **Continuous Progress Automation** prompt to reorient from `CLAUDE.md`.