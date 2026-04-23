# Forge Compiler Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development or superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build a complete Forge→JavaScript compiler with parser, analyzer, optimizer, generator, CLI, 100% test coverage, and 5+ examples.

**Architecture:** Ohm.js parses Forge source into a match tree; parser.js converts it to core.js AST nodes; analyzer.js decorates/checks the AST; optimizer.js transforms it; generator.js emits JavaScript.

**Tech Stack:** Node.js ESM, Ohm-js, c8 (coverage), Mocha (tests), Prettier

**Project root:** `/Users/nityaaddanki/Documents/Forge/Plang`

---

### Task 1: Project Foundation

**Files:**
- Create: `Plang/package.json`
- Create: `Plang/.gitignore`
- Rename: `src/(yourlanguagename).js` → `src/forge.js`
- Rename: `src/(yourlanguagename).ohm` → `src/forge.ohm`

- [ ] Write `package.json`:
```json
{
  "name": "forge",
  "version": "0.1.0",
  "description": "The Forge programming language compiler",
  "type": "module",
  "main": "src/forge.js",
  "bin": { "forge": "src/forge.js" },
  "author": ["TODO: Add team member names"],
  "license": "MIT",
  "repository": { "type": "git", "url": "https://github.com/JohhannasReyn/Forge" },
  "scripts": {
    "test": "c8 mocha --timeout 10000",
    "coverage": "c8 --reporter=lcov mocha",
    "format": "prettier --write \"**/*.{js,ohm,md,json}\""
  },
  "dependencies": { "ohm-js": "^17.1.0" },
  "devDependencies": {
    "c8": "^9.0.0",
    "mocha": "^10.0.0",
    "prettier": "^3.0.0"
  }
}
```

- [ ] Write `.gitignore`:
```
node_modules/
coverage/
.DS_Store
*.log
```

- [ ] `mv "src/(yourlanguagename).js" src/forge.js && mv "src/(yourlanguagename).ohm" src/forge.ohm`
- [ ] `cd Plang && npm install`
- [ ] Verify: `npm test` runs (0 tests passing is fine)

---

### Task 2: Ohm Grammar (`src/forge.ohm`)

- [ ] Write complete grammar:

```ohm
Forge {
  Program = Statement*

  Statement
    = FuncDecl
    | CustomDecl
    | GlobalDecl
    | VarDecl
    | ConstDecl
    | AssignStmt
    | CompoundAssignStmt
    | ReturnStmt
    | BreakStmt
    | IfStmt
    | WhileStmt
    | DoWhileStmt
    | ForStmt
    | SwitchStmt
    | ExprStmt

  FuncDecl = "func" id "(" Params ")" ":" Type Block

  Params = ListOf<Param, ",">
  Param = id ":" Type

  Type
    = "byte"  -- byte
    | "char"  -- char
    | "snum"  -- snum
    | "inum"  -- inum
    | "lnum"  -- lnum
    | "fnum"  -- fnum
    | "dnum"  -- dnum
    | "hilo"  -- hilo
    | "empty" -- empty
    | id      -- custom

  CustomDecl = "custom" id "=" "{" NonemptyListOf<CustomMember, ","> ";" "}"
  CustomMember
    = id "(" literal ")"  -- valued
    | id                  -- plain

  GlobalDecl = "global" id "=" "{" NonemptyListOf<GlobalField, ","> "}" ";"
  GlobalField = id ":" Exp

  VarDecl
    = "let" id ":" Type "=" Exp ";"  -- typed
    | "let" id "=" Exp ";"           -- inferred

  ConstDecl
    = "C" id ":" Type "=" Exp ";"  -- typed
    | "C" id "=" Exp ";"           -- inferred

  AssignStmt
    = id "{" Exp "}" "=" boollit ";"  -- bitset
    | id "=" Exp ";"                  -- simple

  CompoundAssignStmt = id compoundOp Exp ";"
  compoundOp = "+=" | "-=" | "*=" | "/="

  ReturnStmt
    = "resolve" ";"     -- empty
    | "deliver" Exp ";" -- value

  BreakStmt = "escape" ";"

  IfStmt = "is" "(" Exp ")" Block ("not" Block)?

  WhileStmt = "whenis" "(" Exp ")" Block

  DoWhileStmt = "execute" Block "whenis" "(" Exp ")" ";"

  ForStmt = "whento" "(" ForInit ";" Exp ";" ForUpdate ")" Block
  ForInit
    = "let" id "=" Exp  -- decl
    | id "=" Exp        -- assign
  ForUpdate
    = id "++"                  -- postInc
    | id "--"                  -- postDec
    | "++" id                  -- preInc
    | "--" id                  -- preDec
    | id compoundOp Exp        -- compound

  SwitchStmt = "hub" "(" Exp ")" "{" CaseClause* DefaultClause? "}"
  CaseClause = ":{" Exp "}:" Block
  DefaultClause = "base" Block

  Block = "{" Statement* "}"
  ExprStmt = Exp ";"

  Exp = Ternary
  Ternary
    = LogOrExp "?" Ternary ":" Ternary  -- ternary
    | LogOrExp                          -- passthru

  LogOrExp
    = LogOrExp "||" LogXorExp  -- or
    | LogOrExp "^|" LogXorExp  -- xor
    | LogXorExp                -- passthru

  LogXorExp
    = LogXorExp "^&" LogAndExp  -- xnor
    | LogAndExp                 -- passthru

  LogAndExp
    = LogAndExp "&&" EqExp  -- and
    | EqExp                 -- passthru

  EqExp
    = EqExp "==" RelExp  -- eq
    | EqExp "!=" RelExp  -- ne
    | RelExp             -- passthru

  RelExp
    = RelExp "<=" AddExp  -- le
    | RelExp ">=" AddExp  -- ge
    | RelExp "<" AddExp   -- lt
    | RelExp ">" AddExp   -- gt
    | AddExp              -- passthru

  AddExp
    = AddExp "+" MulExp  -- plus
    | AddExp "-" MulExp  -- minus
    | MulExp             -- passthru

  MulExp
    = MulExp "*" BitExp  -- times
    | MulExp "/" BitExp  -- divide
    | MulExp "%" BitExp  -- mod
    | BitExp             -- passthru

  BitExp
    = BitExp "bitand" ExpExp  -- bitand
    | BitExp "bitor"  ExpExp  -- bitor
    | BitExp "bitxor" ExpExp  -- bitxor
    | BitExp "shiftl" ExpExp  -- shiftl
    | BitExp "shiftr" ExpExp  -- shiftr
    | BitExp "&" ExpExp       -- symbAnd
    | BitExp "|" ExpExp       -- symbOr
    | ExpExp                  -- passthru

  ExpExp
    = UnaryExp "^" ExpExp  -- power
    | UnaryExp             -- passthru

  UnaryExp
    = "++" id              -- preInc
    | "--" id              -- preDec
    | "!" UnaryExp         -- not
    | "-" UnaryExp         -- neg
    | "bitnot" UnaryExp    -- bitnot
    | PostfixExp           -- passthru

  PostfixExp
    = PostfixExp "++"  -- postInc
    | PostfixExp "--"  -- postDec
    | CallExp          -- passthru

  CallExp
    = id "(" ListOf<Exp, ","> ")"  -- call
    | MemberExp                    -- passthru

  MemberExp
    = MemberExp "." id             -- field
    | id "{" Exp "}"               -- bitIndex
    | id "[" Exp ":" Exp "]"       -- slice
    | id "[" Exp "]"               -- index
    | PriExp                       -- passthru

  PriExp
    = "(" Exp ")"  -- paren
    | boollit      -- bool
    | "non"        -- null
    | strlit       -- string
    | charlit      -- char
    | numlit       -- num
    | id           -- id

  boollit = "TT" | "FF"

  numlit
    = digit+ "." digit+  -- float
    | digit+             -- int

  strlit  = "\"" strchar* "\""
  strchar = ~("\"" | "\\" | "\n") any | "\\" escseq
  charlit = "'" charchar "'"
  charchar = ~("'" | "\\" | "\n") any | "\\" escseq
  escseq  = "n" | "t" | "r" | "\"" | "'" | "\\" | "0"

  literal
    = numlit
    | strlit
    | charlit
    | boollit

  id = ~keyword (letter | "_") (alnum | "_")*

  keyword
    = ( "async" | "await" | "base" | "bitand" | "bitnot" | "bitor" | "bitxor"
      | "blueprint" | "byte" | "char" | "custom" | "C" | "deliver" | "dnum"
      | "empty" | "escape" | "execute" | "farm" | "FF" | "fin" | "fnum" | "func"
      | "global" | "hilo" | "hub" | "inum" | "is" | "let" | "lnum" | "non"
      | "not" | "overt" | "preload" | "proceed" | "resolve" | "secret" | "shiftl"
      | "shiftr" | "snum" | "synced" | "TT" | "whenis" | "whento" ) ~(alnum | "_")

  space
   += "//" (~"\n" any)* "\n"?  -- lineComment
    | "/*" (~"*/" any)* "*/"    -- blockComment
}
```

- [ ] Verify grammar in Ohm editor or via quick parse test

---

### Task 3: Core AST Model (`src/core.js`)

- [ ] Write complete `src/core.js`:

```js
// Named types (singletons)
export const BYTE = Object.freeze({ kind: "byte" })
export const CHAR = Object.freeze({ kind: "char" })
export const SNUM = Object.freeze({ kind: "snum" })
export const INUM = Object.freeze({ kind: "inum" })
export const LNUM = Object.freeze({ kind: "lnum" })
export const FNUM = Object.freeze({ kind: "fnum" })
export const DNUM = Object.freeze({ kind: "dnum" })
export const HILO = Object.freeze({ kind: "hilo" })
export const EMPTY = Object.freeze({ kind: "empty" })
export const ANY = Object.freeze({ kind: "any" }) // for error recovery / non

export const typeFromName = {
  byte: BYTE, char: CHAR, snum: SNUM, inum: INUM, lnum: LNUM,
  fnum: FNUM, dnum: DNUM, hilo: HILO, empty: EMPTY,
}

export function customType(name) { return { kind: "custom", name } }
export function arrayType(base) { return { kind: "array", base } }

// Type predicates
export const isIntegerType = (t) =>
  [BYTE, SNUM, INUM, LNUM].includes(t)
export const isNumericType = (t) =>
  [BYTE, SNUM, INUM, LNUM, FNUM, DNUM].includes(t)
export const isAssignableTo = (from, to) => {
  if (from === to) return true
  if (from === ANY || to === ANY) return true
  // widening
  const chain = [BYTE, SNUM, INUM, LNUM, FNUM, DNUM]
  const fi = chain.indexOf(from), ti = chain.indexOf(to)
  if (fi >= 0 && ti >= 0 && fi <= ti) return true
  if (from === HILO && (to === INUM || to === LNUM || to === FNUM || to === DNUM)) return true
  return false
}

// AST node factories
export const program = (statements) => ({ kind: "Program", statements })
export const funcDecl = (name, params, returnType, body) =>
  ({ kind: "FuncDecl", name, params, returnType, body })
export const param = (name, type) => ({ kind: "Param", name, type })
export const customDecl = (name, members) => ({ kind: "CustomDecl", name, members })
export const customMember = (name, value) => ({ kind: "CustomMember", name, value })
export const globalDecl = (name, fields) => ({ kind: "GlobalDecl", name, fields })
export const globalField = (name, value) => ({ kind: "GlobalField", name, value })
export const varDecl = (name, type, initializer, readOnly) =>
  ({ kind: "VarDecl", name, type, initializer, readOnly })
export const assignStmt = (target, value) => ({ kind: "AssignStmt", target, value })
export const bitSetStmt = (name, index, flag) => ({ kind: "BitSetStmt", name, index, flag })
export const compoundAssignStmt = (name, op, value) =>
  ({ kind: "CompoundAssignStmt", name, op, value })
export const returnStmt = (expression) => ({ kind: "ReturnStmt", expression })
export const breakStmt = () => ({ kind: "BreakStmt" })
export const ifStmt = (condition, consequent, alternate) =>
  ({ kind: "IfStmt", condition, consequent, alternate })
export const whileStmt = (condition, body) => ({ kind: "WhileStmt", condition, body })
export const doWhileStmt = (body, condition) => ({ kind: "DoWhileStmt", body, condition })
export const forStmt = (init, condition, update, body) =>
  ({ kind: "ForStmt", init, condition, update, body })
export const switchStmt = (discriminant, cases, defaultCase) =>
  ({ kind: "SwitchStmt", discriminant, cases, defaultCase })
export const caseClause = (test, body) => ({ kind: "CaseClause", test, body })
export const block = (statements) => ({ kind: "Block", statements })
export const exprStmt = (expression) => ({ kind: "ExprStmt", expression })
export const ternary = (condition, consequent, alternate) =>
  ({ kind: "Ternary", condition, consequent, alternate })
export const binaryExp = (op, left, right) => ({ kind: "BinaryExp", op, left, right })
export const unaryExp = (op, operand) => ({ kind: "UnaryExp", op, operand })
export const preIncDec = (op, name) => ({ kind: "PreIncDec", op, name })
export const postIncDec = (op, operand) => ({ kind: "PostIncDec", op, operand })
export const callExp = (callee, args) => ({ kind: "CallExp", callee, args })
export const fieldExp = (object, field) => ({ kind: "FieldExp", object, field })
export const bitIndexExp = (name, index) => ({ kind: "BitIndexExp", name, index })
export const sliceExp = (name, start, end) => ({ kind: "SliceExp", name, start, end })
export const indexExp = (name, index) => ({ kind: "IndexExp", name, index })
export const idExp = (name) => ({ kind: "IdExp", name })
export const intLit = (value) => ({ kind: "IntLit", value, type: INUM })
export const floatLit = (value) => ({ kind: "FloatLit", value, type: DNUM })
export const boolLit = (value) => ({ kind: "BoolLit", value, type: HILO })
export const stringLit = (value) => ({ kind: "StringLit", value })
export const charLit = (value) => ({ kind: "CharLit", value, type: CHAR })
export const nullLit = () => ({ kind: "NullLit", type: ANY })
```

---

### Task 4: Parser (`src/parser.js`)

- [ ] Write `src/parser.js`:

```js
import ohm from "ohm-js"
import { readFileSync } from "fs"
import { fileURLToPath } from "url"
import { dirname, join } from "path"
import * as core from "./core.js"

const __dir = dirname(fileURLToPath(import.meta.url))
const grammar = ohm.grammar(readFileSync(join(__dir, "forge.ohm"), "utf8"))

const semantics = grammar.createSemantics()

semantics.addOperation("ast", {
  Program(stmts) { return core.program(stmts.children.map(s => s.ast())) },

  FuncDecl(_func, name, _lp, params, _rp, _colon, type, body) {
    return core.funcDecl(name.sourceString, params.asIteration().children.map(p => p.ast()), type.ast(), body.ast())
  },
  Param(name, _colon, type) { return core.param(name.sourceString, type.ast()) },

  Type_byte(_) { return core.BYTE },
  Type_char(_) { return core.CHAR },
  Type_snum(_) { return core.SNUM },
  Type_inum(_) { return core.INUM },
  Type_lnum(_) { return core.LNUM },
  Type_fnum(_) { return core.FNUM },
  Type_dnum(_) { return core.DNUM },
  Type_hilo(_) { return core.HILO },
  Type_empty(_) { return core.EMPTY },
  Type_custom(id) { return core.customType(id.sourceString) },

  CustomDecl(_custom, name, _eq, _lbr, members, _semi, _rbr) {
    return core.customDecl(name.sourceString, members.asIteration().children.map(m => m.ast()))
  },
  CustomMember_valued(name, _lp, val, _rp) {
    return core.customMember(name.sourceString, val.ast())
  },
  CustomMember_plain(name) { return core.customMember(name.sourceString, null) },

  GlobalDecl(_global, name, _eq, _lbr, fields, _rbr, _semi) {
    return core.globalDecl(name.sourceString, fields.asIteration().children.map(f => f.ast()))
  },
  GlobalField(name, _colon, val) { return core.globalField(name.sourceString, val.ast()) },

  VarDecl_typed(_let, name, _colon, type, _eq, expr, _semi) {
    return core.varDecl(name.sourceString, type.ast(), expr.ast(), false)
  },
  VarDecl_inferred(_let, name, _eq, expr, _semi) {
    return core.varDecl(name.sourceString, null, expr.ast(), false)
  },
  ConstDecl_typed(_C, name, _colon, type, _eq, expr, _semi) {
    return core.varDecl(name.sourceString, type.ast(), expr.ast(), true)
  },
  ConstDecl_inferred(_C, name, _eq, expr, _semi) {
    return core.varDecl(name.sourceString, null, expr.ast(), true)
  },

  AssignStmt_bitset(name, _lb, idx, _rb, _eq, flag, _semi) {
    return core.bitSetStmt(name.sourceString, idx.ast(), flag.sourceString === "TT")
  },
  AssignStmt_simple(name, _eq, expr, _semi) {
    return core.assignStmt(name.sourceString, expr.ast())
  },
  CompoundAssignStmt(name, op, expr, _semi) {
    return core.compoundAssignStmt(name.sourceString, op.sourceString, expr.ast())
  },

  ReturnStmt_empty(_resolve, _semi) { return core.returnStmt(null) },
  ReturnStmt_value(_deliver, expr, _semi) { return core.returnStmt(expr.ast()) },

  BreakStmt(_escape, _semi) { return core.breakStmt() },

  IfStmt(_is, _lp, cond, _rp, body, notPart) {
    const alt = notPart.children.length ? notPart.children[0].ast() : null
    return core.ifStmt(cond.ast(), body.ast(), alt)
  },
  _iter(...children) { return children.map(c => c.ast()) },

  WhileStmt(_whenis, _lp, cond, _rp, body) {
    return core.whileStmt(cond.ast(), body.ast())
  },
  DoWhileStmt(_execute, body, _whenis, _lp, cond, _rp, _semi) {
    return core.doWhileStmt(body.ast(), cond.ast())
  },
  ForStmt(_whento, _lp, init, _semi1, cond, _semi2, update, _rp, body) {
    return core.forStmt(init.ast(), cond.ast(), update.ast(), body.ast())
  },
  ForInit_decl(_let, name, _eq, expr) {
    return core.varDecl(name.sourceString, null, expr.ast(), false)
  },
  ForInit_assign(name, _eq, expr) {
    return core.assignStmt(name.sourceString, expr.ast())
  },
  ForUpdate_postInc(name, _op) { return core.postIncDec("++", core.idExp(name.sourceString)) },
  ForUpdate_postDec(name, _op) { return core.postIncDec("--", core.idExp(name.sourceString)) },
  ForUpdate_preInc(_op, name) { return core.preIncDec("++", name.sourceString) },
  ForUpdate_preDec(_op, name) { return core.preIncDec("--", name.sourceString) },
  ForUpdate_compound(name, op, expr) {
    return core.compoundAssignStmt(name.sourceString, op.sourceString, expr.ast())
  },

  SwitchStmt(_hub, _lp, disc, _rp, _lb, cases, defClause, _rb) {
    const d = defClause.children.length ? defClause.children[0].ast() : null
    return core.switchStmt(disc.ast(), cases.children.map(c => c.ast()), d)
  },
  CaseClause(_lbr, expr, _rbr, body) { return core.caseClause(expr.ast(), body.ast()) },
  DefaultClause(_base, body) { return body.ast() },

  Block(_lb, stmts, _rb) { return core.block(stmts.children.map(s => s.ast())) },
  ExprStmt(expr, _semi) { return core.exprStmt(expr.ast()) },

  Ternary_ternary(cond, _q, cons, _colon, alt) {
    return core.ternary(cond.ast(), cons.ast(), alt.ast())
  },
  Ternary_passthru(e) { return e.ast() },

  LogOrExp_or(l, _op, r) { return core.binaryExp("||", l.ast(), r.ast()) },
  LogOrExp_xor(l, _op, r) { return core.binaryExp("^|", l.ast(), r.ast()) },
  LogOrExp_passthru(e) { return e.ast() },
  LogXorExp_xnor(l, _op, r) { return core.binaryExp("^&", l.ast(), r.ast()) },
  LogXorExp_passthru(e) { return e.ast() },
  LogAndExp_and(l, _op, r) { return core.binaryExp("&&", l.ast(), r.ast()) },
  LogAndExp_passthru(e) { return e.ast() },
  EqExp_eq(l, _op, r) { return core.binaryExp("==", l.ast(), r.ast()) },
  EqExp_ne(l, _op, r) { return core.binaryExp("!=", l.ast(), r.ast()) },
  EqExp_passthru(e) { return e.ast() },
  RelExp_le(l, _op, r) { return core.binaryExp("<=", l.ast(), r.ast()) },
  RelExp_ge(l, _op, r) { return core.binaryExp(">=", l.ast(), r.ast()) },
  RelExp_lt(l, _op, r) { return core.binaryExp("<", l.ast(), r.ast()) },
  RelExp_gt(l, _op, r) { return core.binaryExp(">", l.ast(), r.ast()) },
  RelExp_passthru(e) { return e.ast() },
  AddExp_plus(l, _op, r) { return core.binaryExp("+", l.ast(), r.ast()) },
  AddExp_minus(l, _op, r) { return core.binaryExp("-", l.ast(), r.ast()) },
  AddExp_passthru(e) { return e.ast() },
  MulExp_times(l, _op, r) { return core.binaryExp("*", l.ast(), r.ast()) },
  MulExp_divide(l, _op, r) { return core.binaryExp("/", l.ast(), r.ast()) },
  MulExp_mod(l, _op, r) { return core.binaryExp("%", l.ast(), r.ast()) },
  MulExp_passthru(e) { return e.ast() },
  BitExp_bitand(l, _op, r) { return core.binaryExp("bitand", l.ast(), r.ast()) },
  BitExp_bitor(l, _op, r) { return core.binaryExp("bitor", l.ast(), r.ast()) },
  BitExp_bitxor(l, _op, r) { return core.binaryExp("bitxor", l.ast(), r.ast()) },
  BitExp_shiftl(l, _op, r) { return core.binaryExp("shiftl", l.ast(), r.ast()) },
  BitExp_shiftr(l, _op, r) { return core.binaryExp("shiftr", l.ast(), r.ast()) },
  BitExp_symbAnd(l, _op, r) { return core.binaryExp("&", l.ast(), r.ast()) },
  BitExp_symbOr(l, _op, r) { return core.binaryExp("|", l.ast(), r.ast()) },
  BitExp_passthru(e) { return e.ast() },
  ExpExp_power(l, _op, r) { return core.binaryExp("^", l.ast(), r.ast()) },
  ExpExp_passthru(e) { return e.ast() },

  UnaryExp_preInc(_op, id) { return core.preIncDec("++", id.sourceString) },
  UnaryExp_preDec(_op, id) { return core.preIncDec("--", id.sourceString) },
  UnaryExp_not(_op, e) { return core.unaryExp("!", e.ast()) },
  UnaryExp_neg(_op, e) { return core.unaryExp("-", e.ast()) },
  UnaryExp_bitnot(_op, e) { return core.unaryExp("bitnot", e.ast()) },
  UnaryExp_passthru(e) { return e.ast() },

  PostfixExp_postInc(e, _op) { return core.postIncDec("++", e.ast()) },
  PostfixExp_postDec(e, _op) { return core.postIncDec("--", e.ast()) },
  PostfixExp_passthru(e) { return e.ast() },

  CallExp_call(name, _lp, args, _rp) {
    return core.callExp(name.sourceString, args.asIteration().children.map(a => a.ast()))
  },
  CallExp_passthru(e) { return e.ast() },

  MemberExp_field(obj, _dot, field) { return core.fieldExp(obj.ast(), field.sourceString) },
  MemberExp_bitIndex(name, _lb, idx, _rb) { return core.bitIndexExp(name.sourceString, idx.ast()) },
  MemberExp_slice(name, _lb, s, _colon, e, _rb) { return core.sliceExp(name.sourceString, s.ast(), e.ast()) },
  MemberExp_index(name, _lb, idx, _rb) { return core.indexExp(name.sourceString, idx.ast()) },
  MemberExp_passthru(e) { return e.ast() },

  PriExp_paren(_lp, e, _rp) { return e.ast() },
  PriExp_bool(b) { return core.boolLit(b.sourceString === "TT") },
  PriExp_null(_) { return core.nullLit() },
  PriExp_string(s) { return core.stringLit(s.sourceString.slice(1, -1)) },
  PriExp_char(c) { return core.charLit(c.sourceString.slice(1, -1)) },
  PriExp_num(n) { return n.ast() },
  PriExp_id(id) { return core.idExp(id.sourceString) },

  numlit_float(whole, _dot, frac) {
    return core.floatLit(parseFloat(whole.sourceString + "." + frac.sourceString))
  },
  numlit_int(digits) { return core.intLit(parseInt(digits.sourceString, 10)) },

  literal(e) { return e.ast() },
  boollit(b) { return core.boolLit(b.sourceString === "TT") },

  id(first, rest) { return this.sourceString },
  _terminal() { return this.sourceString },
})

export function parse(source) {
  const match = grammar.match(source)
  if (match.failed()) throw new Error(match.message)
  return semantics(match).ast()
}
```

---

### Task 5: Analyzer (`src/analyzer.js`)

- [ ] Write `src/analyzer.js`:

```js
import * as core from "./core.js"

class Context {
  constructor(parent = null, { inLoop = false, inSwitch = false, returnType = null } = {}) {
    this.parent = parent
    this.locals = new Map()
    this.inLoop = inLoop
    this.inSwitch = inSwitch
    this.returnType = returnType
  }
  add(name, entity) {
    if (this.locals.has(name)) throw new Error(`Variable already declared: ${name}`)
    this.locals.set(name, entity)
  }
  lookup(name) {
    if (this.locals.has(name)) return this.locals.get(name)
    if (this.parent) return this.parent.lookup(name)
    throw new Error(`Undeclared identifier: ${name}`)
  }
  child(opts) { return new Context(this, { inLoop: this.inLoop, inSwitch: this.inSwitch, returnType: this.returnType, ...opts }) }
}

function check(condition, message) {
  if (!condition) throw new Error(message)
}

function typeOf(node) {
  return node.type ?? core.ANY
}

function inferType(node) {
  if (!node.type) throw new Error("Cannot infer type from expression")
  return node.type
}

const analyze = {
  Program(node, ctx) {
    for (const s of node.statements) analyze[s.kind](s, ctx)
    return node
  },

  FuncDecl(node, ctx) {
    check(!ctx.locals.has(node.name), `Function already declared: ${node.name}`)
    ctx.locals.set(node.name, { kind: "Function", name: node.name, params: node.params, returnType: node.returnType })
    const inner = ctx.child({ returnType: node.returnType })
    for (const p of node.params) {
      inner.add(p.name, { kind: "Variable", name: p.name, type: p.type, readOnly: true })
    }
    analyze.Block(node.body, inner)
    return node
  },

  CustomDecl(node, ctx) {
    const names = new Set()
    for (const m of node.members) {
      check(!names.has(m.name), `Duplicate custom member: ${m.name}`)
      names.add(m.name)
    }
    ctx.add(node.name, { kind: "CustomType", name: node.name, members: node.members })
    return node
  },

  GlobalDecl(node, ctx) {
    ctx.add(node.name, { kind: "Global", name: node.name })
    return node
  },

  VarDecl(node, ctx) {
    const initType = typeOf(analyze.expr(node.initializer, ctx))
    const declaredType = node.type ?? initType
    if (node.type) check(core.isAssignableTo(initType, node.type), `Type mismatch in declaration of ${node.name}`)
    node.type = declaredType
    ctx.add(node.name, { kind: "Variable", name: node.name, type: declaredType, readOnly: node.readOnly })
    return node
  },

  AssignStmt(node, ctx) {
    const entity = ctx.lookup(node.target)
    check(!entity.readOnly, `Cannot assign to constant: ${node.target}`)
    const valType = typeOf(analyze.expr(node.value, ctx))
    check(core.isAssignableTo(valType, entity.type), `Type mismatch in assignment to ${node.target}`)
    return node
  },

  BitSetStmt(node, ctx) {
    const entity = ctx.lookup(node.name)
    check(core.isIntegerType(entity.type), `Bit operations require integer type: ${node.name}`)
    analyze.expr(node.index, ctx)
    return node
  },

  CompoundAssignStmt(node, ctx) {
    const entity = ctx.lookup(node.name)
    check(!entity.readOnly, `Cannot assign to constant: ${node.name}`)
    analyze.expr(node.value, ctx)
    return node
  },

  ReturnStmt(node, ctx) {
    if (node.expression === null) {
      check(ctx.returnType === core.EMPTY, "resolve can only be used in empty functions")
    } else {
      check(ctx.returnType !== core.EMPTY, "deliver cannot be used in empty functions")
      const t = typeOf(analyze.expr(node.expression, ctx))
      check(core.isAssignableTo(t, ctx.returnType), "Return type mismatch")
    }
    return node
  },

  BreakStmt(node, ctx) {
    check(ctx.inLoop || ctx.inSwitch, "escape must be inside a loop or hub")
    return node
  },

  IfStmt(node, ctx) {
    const condType = typeOf(analyze.expr(node.condition, ctx))
    check(condType === core.HILO || core.isAssignableTo(condType, core.HILO), "Condition must be hilo")
    analyze.Block(node.consequent, ctx.child())
    if (node.alternate) analyze.Block(node.alternate, ctx.child())
    return node
  },

  WhileStmt(node, ctx) {
    analyze.expr(node.condition, ctx)
    analyze.Block(node.body, ctx.child({ inLoop: true }))
    return node
  },

  DoWhileStmt(node, ctx) {
    analyze.Block(node.body, ctx.child({ inLoop: true }))
    analyze.expr(node.condition, ctx)
    return node
  },

  ForStmt(node, ctx) {
    const inner = ctx.child({ inLoop: true })
    if (node.init.kind === "VarDecl") analyze.VarDecl(node.init, inner)
    else analyze.AssignStmt(node.init, inner)
    analyze.expr(node.condition, inner)
    analyzeForUpdate(node.update, inner)
    analyze.Block(node.body, inner.child())
    return node
  },

  SwitchStmt(node, ctx) {
    analyze.expr(node.discriminant, ctx)
    const seen = new Set()
    for (const c of node.cases) {
      const label = JSON.stringify(c.test)
      check(!seen.has(label), "Duplicate case label in hub")
      seen.add(label)
      analyze.Block(c.body, ctx.child({ inSwitch: true }))
    }
    if (node.defaultCase) analyze.Block(node.defaultCase, ctx.child({ inSwitch: true }))
    return node
  },

  Block(node, ctx) {
    for (const s of node.statements) {
      if (analyze[s.kind]) analyze[s.kind](s, ctx)
    }
    return node
  },

  ExprStmt(node, ctx) {
    analyze.expr(node.expression, ctx)
    return node
  },

  expr(node, ctx) {
    switch (node.kind) {
      case "Ternary": return analyzeTernary(node, ctx)
      case "BinaryExp": return analyzeBinary(node, ctx)
      case "UnaryExp": return analyzeUnary(node, ctx)
      case "PreIncDec": return analyzePreIncDec(node, ctx)
      case "PostIncDec": return analyzePostIncDec(node, ctx)
      case "CallExp": return analyzeCall(node, ctx)
      case "FieldExp": analyze.expr(node.object, ctx); return node
      case "BitIndexExp": return analyzeBitIndex(node, ctx)
      case "SliceExp": return analyzeSlice(node, ctx)
      case "IndexExp": return analyzeIndex(node, ctx)
      case "IdExp": return analyzeId(node, ctx)
      case "IntLit": return node
      case "FloatLit": return node
      case "BoolLit": return node
      case "StringLit": return node
      case "CharLit": return node
      case "NullLit": return node
      default: return node
    }
  },
}

function analyzeForUpdate(node, ctx) {
  if (node.kind === "PostIncDec" || node.kind === "PreIncDec") {
    if (node.name) ctx.lookup(node.name)
  } else if (node.kind === "CompoundAssignStmt") {
    analyze.CompoundAssignStmt(node, ctx)
  }
}

function analyzeTernary(node, ctx) {
  analyze.expr(node.condition, ctx)
  const t1 = typeOf(analyze.expr(node.consequent, ctx))
  const t2 = typeOf(analyze.expr(node.alternate, ctx))
  node.type = core.isAssignableTo(t1, t2) ? t2 : t1
  return node
}

function analyzeBinary(node, ctx) {
  const left = analyze.expr(node.left, ctx)
  const right = analyze.expr(node.right, ctx)
  const lt = typeOf(left), rt = typeOf(right)
  const bitOps = ["bitand", "bitor", "bitxor", "shiftl", "shiftr", "&", "|"]
  const logicOps = ["&&", "||", "^|", "^&"]
  const numOps = ["+", "-", "*", "/", "%", "^"]
  const cmpOps = ["<", ">", "<=", ">=", "==", "!="]
  if (bitOps.includes(node.op)) {
    check(core.isIntegerType(lt), `Bitwise op requires integer left operand`)
    check(core.isIntegerType(rt), `Bitwise op requires integer right operand`)
    node.type = lt
  } else if (logicOps.includes(node.op)) {
    node.type = core.HILO
  } else if (numOps.includes(node.op)) {
    check(core.isNumericType(lt), `Arithmetic op requires numeric operand`)
    check(core.isNumericType(rt), `Arithmetic op requires numeric operand`)
    node.type = core.isAssignableTo(lt, rt) ? rt : lt
  } else if (cmpOps.includes(node.op)) {
    node.type = core.HILO
  }
  return node
}

function analyzeUnary(node, ctx) {
  const operand = analyze.expr(node.operand, ctx)
  const t = typeOf(operand)
  if (node.op === "!") { node.type = core.HILO }
  else if (node.op === "bitnot") {
    check(core.isIntegerType(t), "bitnot requires integer operand")
    node.type = t
  } else {
    node.type = t
  }
  return node
}

function analyzePreIncDec(node, ctx) {
  const entity = ctx.lookup(node.name)
  check(!entity.readOnly, `Cannot modify constant: ${node.name}`)
  node.type = entity.type
  return node
}

function analyzePostIncDec(node, ctx) {
  if (node.operand.kind === "IdExp") {
    const entity = ctx.lookup(node.operand.name)
    check(!entity.readOnly, `Cannot modify constant: ${node.operand.name}`)
    node.type = entity.type
  }
  return node
}

function analyzeCall(node, ctx) {
  const entity = ctx.lookup(node.callee)
  check(entity.kind === "Function", `${node.callee} is not a function`)
  check(node.args.length === entity.params.length,
    `${node.callee} expects ${entity.params.length} args, got ${node.args.length}`)
  for (let i = 0; i < node.args.length; i++) {
    const argType = typeOf(analyze.expr(node.args[i], ctx))
    check(core.isAssignableTo(argType, entity.params[i].type),
      `Argument ${i + 1} type mismatch in call to ${node.callee}`)
  }
  node.type = entity.returnType
  return node
}

function analyzeBitIndex(node, ctx) {
  const entity = ctx.lookup(node.name)
  check(core.isIntegerType(entity.type), `Bit index requires integer: ${node.name}`)
  analyze.expr(node.index, ctx)
  node.type = core.HILO
  return node
}

function analyzeSlice(node, ctx) {
  ctx.lookup(node.name)
  analyze.expr(node.start, ctx)
  analyze.expr(node.end, ctx)
  return node
}

function analyzeIndex(node, ctx) {
  ctx.lookup(node.name)
  analyze.expr(node.index, ctx)
  return node
}

function analyzeId(node, ctx) {
  const entity = ctx.lookup(node.name)
  node.type = entity.type ?? core.ANY
  return node
}

export function analyze(program) {
  const ctx = new Context()
  analyze.Program(program, ctx)
  return program
}
```

---

### Task 6: Optimizer (`src/optimizer.js`)

- [ ] Write `src/optimizer.js`:

```js
import * as core from "./core.js"

function foldBinary(op, l, r) {
  const lv = l.value, rv = r.value
  switch (op) {
    case "+": return lv + rv
    case "-": return lv - rv
    case "*": return lv * rv
    case "/": return rv !== 0 ? lv / rv : null
    case "%": return rv !== 0 ? lv % rv : null
    case "^": return lv ** rv
    case "<": return lv < rv
    case ">": return lv > rv
    case "<=": return lv <= rv
    case ">=": return lv >= rv
    case "==": return lv === rv
    case "!=": return lv !== rv
    case "&&": return lv && rv
    case "||": return lv || rv
    case "^|": return !lv !== !rv
    case "^&": return !lv === !rv
    case "bitand": case "&": return (lv & rv) | 0
    case "bitor": case "|": return (lv | rv) | 0
    case "bitxor": return (lv ^ rv) | 0
    case "shiftl": return (lv << rv) | 0
    case "shiftr": return (lv >> rv) | 0
    default: return null
  }
}

function isLit(n) {
  return ["IntLit", "FloatLit", "BoolLit"].includes(n.kind)
}

function makeLit(val) {
  if (typeof val === "boolean") return core.boolLit(val)
  if (Number.isInteger(val)) return core.intLit(val)
  return core.floatLit(val)
}

const opt = {
  Program(n) { return { ...n, statements: n.statements.map(s => opt.stmt(s)) } },

  stmt(n) {
    if (opt[n.kind]) return opt[n.kind](n)
    return n
  },

  FuncDecl(n) { return { ...n, body: opt.Block(n.body) } },
  CustomDecl(n) { return n },
  GlobalDecl(n) { return n },
  VarDecl(n) { return { ...n, initializer: opt.expr(n.initializer) } },
  AssignStmt(n) { return { ...n, value: opt.expr(n.value) } },
  BitSetStmt(n) { return { ...n, index: opt.expr(n.index) } },
  CompoundAssignStmt(n) { return { ...n, value: opt.expr(n.value) } },
  ReturnStmt(n) { return { ...n, expression: n.expression ? opt.expr(n.expression) : null } },
  BreakStmt(n) { return n },

  IfStmt(n) {
    const cond = opt.expr(n.condition)
    if (cond.kind === "BoolLit") {
      return cond.value ? opt.Block(n.consequent) : (n.alternate ? opt.Block(n.alternate) : core.block([]))
    }
    return { ...n, condition: cond, consequent: opt.Block(n.consequent), alternate: n.alternate ? opt.Block(n.alternate) : null }
  },

  WhileStmt(n) {
    const cond = opt.expr(n.condition)
    if (cond.kind === "BoolLit" && !cond.value) return core.block([])
    return { ...n, condition: cond, body: opt.Block(n.body) }
  },

  DoWhileStmt(n) { return { ...n, body: opt.Block(n.body), condition: opt.expr(n.condition) } },
  ForStmt(n) { return { ...n, body: opt.Block(n.body) } },

  SwitchStmt(n) {
    return {
      ...n,
      cases: n.cases.map(c => ({ ...c, body: opt.Block(c.body) })),
      defaultCase: n.defaultCase ? opt.Block(n.defaultCase) : null,
    }
  },

  Block(n) {
    const stmts = []
    for (const s of n.statements) {
      const os = opt.stmt(s)
      if (os.kind === "Block") stmts.push(...os.statements)
      else stmts.push(os)
      if (s.kind === "ReturnStmt" || s.kind === "BreakStmt") break // dead code removal
    }
    return core.block(stmts)
  },

  ExprStmt(n) { return { ...n, expression: opt.expr(n.expression) } },

  expr(n) {
    if (opt.exprs[n.kind]) return opt.exprs[n.kind](n)
    return n
  },

  exprs: {
    BinaryExp(n) {
      const l = opt.expr(n.left), r = opt.expr(n.right)
      // Constant folding
      if (isLit(l) && isLit(r)) {
        const v = foldBinary(n.op, l, r)
        if (v !== null) return makeLit(v)
      }
      // Identity operations
      if (n.op === "+" && r.kind === "IntLit" && r.value === 0) return l
      if (n.op === "+" && l.kind === "IntLit" && l.value === 0) return r
      if (n.op === "-" && r.kind === "IntLit" && r.value === 0) return l
      if (n.op === "*" && r.kind === "IntLit" && r.value === 1) return l
      if (n.op === "*" && l.kind === "IntLit" && l.value === 1) return r
      if (n.op === "*" && (r.kind === "IntLit" && r.value === 0)) return core.intLit(0)
      if (n.op === "*" && (l.kind === "IntLit" && l.value === 0)) return core.intLit(0)
      if (n.op === "^" && r.kind === "IntLit" && r.value === 1) return l
      if (n.op === "^" && r.kind === "IntLit" && r.value === 0) return core.intLit(1)
      return { ...n, left: l, right: r }
    },
    UnaryExp(n) {
      const operand = opt.expr(n.operand)
      if (n.op === "-" && operand.kind === "IntLit") return core.intLit(-operand.value)
      if (n.op === "-" && operand.kind === "FloatLit") return core.floatLit(-operand.value)
      if (n.op === "!" && operand.kind === "BoolLit") return core.boolLit(!operand.value)
      return { ...n, operand }
    },
    Ternary(n) {
      const cond = opt.expr(n.condition)
      if (cond.kind === "BoolLit") return cond.value ? opt.expr(n.consequent) : opt.expr(n.alternate)
      return { ...n, condition: cond, consequent: opt.expr(n.consequent), alternate: opt.expr(n.alternate) }
    },
    CallExp(n) { return { ...n, args: n.args.map(a => opt.expr(a)) } },
    FieldExp(n) { return { ...n, object: opt.expr(n.object) } },
  },
}

export function optimize(program) { return opt.Program(program) }
```

---

### Task 7: Generator (`src/generator.js`)

- [ ] Write `src/generator.js`:

```js
const gen = (node) => generators[node.kind](node)

const generators = {
  Program: (n) => n.statements.map(gen).join("\n"),

  FuncDecl: (n) =>
    `function ${n.name}(${n.params.map(p => p.name).join(", ")}) {\n${gen(n.body)}\n}`,

  CustomDecl: (n) => {
    const entries = n.members.map((m, i) =>
      m.value !== null ? `  ${m.name}: ${genLitVal(m.value)}` : `  ${m.name}: ${i}`
    )
    return `const ${n.name} = Object.freeze({\n${entries.join(",\n")}\n})`
  },

  GlobalDecl: (n) => {
    const fields = n.fields.map(f => `  ${f.name}: ${gen(f.value)}`).join(",\n")
    return `const ${n.name} = {\n${fields}\n}`
  },

  VarDecl: (n) =>
    `${n.readOnly ? "const" : "let"} ${n.name} = ${gen(n.initializer)}`,

  AssignStmt: (n) => `${n.target} = ${gen(n.value)}`,
  BitSetStmt: (n) =>
    n.flag
      ? `${n.name} = ${n.name} | (1 << ${gen(n.index)})`
      : `${n.name} = ${n.name} & ~(1 << ${gen(n.index)})`,
  CompoundAssignStmt: (n) => `${n.name} ${n.op} ${gen(n.value)}`,
  ReturnStmt: (n) => n.expression ? `return ${gen(n.expression)}` : `return`,
  BreakStmt: (_) => `break`,

  IfStmt: (n) => {
    let s = `if (${gen(n.condition)}) {\n${gen(n.consequent)}\n}`
    if (n.alternate) s += ` else {\n${gen(n.alternate)}\n}`
    return s
  },

  WhileStmt: (n) => `while (${gen(n.condition)}) {\n${gen(n.body)}\n}`,
  DoWhileStmt: (n) => `do {\n${gen(n.body)}\n} while (${gen(n.condition)})`,
  ForStmt: (n) => `for (${gen(n.init)}; ${gen(n.condition)}; ${gen(n.update)}) {\n${gen(n.body)}\n}`,

  SwitchStmt: (n) => {
    const cases = n.cases.map(c => `  case ${gen(c.test)}:\n${gen(c.body)}\n    break`).join("\n")
    const def = n.defaultCase ? `\n  default:\n${gen(n.defaultCase)}` : ""
    return `switch (${gen(n.discriminant)}) {\n${cases}${def}\n}`
  },

  Block: (n) => n.statements.map(gen).map(s => "  " + s).join("\n"),
  ExprStmt: (n) => gen(n.expression),

  Ternary: (n) => `(${gen(n.condition)} ? ${gen(n.consequent)} : ${gen(n.alternate)})`,

  BinaryExp: (n) => {
    const opMap = {
      "bitand": "&", "bitor": "|", "bitxor": "^",
      "shiftl": "<<", "shiftr": ">>",
      "^|": (l, r) => `(!!(${l}) !== !!(${r}))`,
      "^&": (l, r) => `(!!(${l}) === !!(${r}))`,
    }
    const l = gen(n.left), r = gen(n.right)
    if (typeof opMap[n.op] === "function") return opMap[n.op](l, r)
    const op = opMap[n.op] ?? n.op
    return `(${l} ${op} ${r})`
  },

  UnaryExp: (n) => {
    const op = n.op === "bitnot" ? "~" : n.op
    return `${op}(${gen(n.operand)})`
  },

  PreIncDec: (n) => `${n.op}${n.name}`,
  PostIncDec: (n) => {
    const operand = n.operand.kind === "IdExp" ? n.operand.name : gen(n.operand)
    return `${operand}${n.op}`
  },

  CallExp: (n) => `${n.callee}(${n.args.map(gen).join(", ")})`,
  FieldExp: (n) => `${gen(n.object)}.${n.field}`,
  BitIndexExp: (n) => `((${n.name} >> ${gen(n.index)}) & 1)`,
  SliceExp: (n) => `${n.name}.slice(${gen(n.start)}, ${gen(n.end)})`,
  IndexExp: (n) => `${n.name}[${gen(n.index)}]`,
  IdExp: (n) => n.name,

  IntLit: (n) => String(n.value),
  FloatLit: (n) => String(n.value),
  BoolLit: (n) => n.value ? "true" : "false",
  StringLit: (n) => JSON.stringify(n.value),
  CharLit: (n) => JSON.stringify(n.value),
  NullLit: (_) => "null",
}

function genLitVal(node) {
  return gen(node)
}

export function generate(program) { return gen(program) }
```

---

### Task 8: CLI Entry + Compiler Pipeline

- [ ] Write `src/compiler.js`:

```js
import { parse } from "./parser.js"
import { analyze } from "./analyzer.js"
import { optimize } from "./optimizer.js"
import { generate } from "./generator.js"

export function compile(source, { syntaxOnly = false, parseOnly = false, analyzeOnly = false, optimizeOnly = false } = {}) {
  const ast = parse(source)
  if (syntaxOnly || parseOnly) return ast
  const analyzed = analyze(ast)
  if (analyzeOnly) return analyzed
  const optimized = optimize(analyzed)
  if (optimizeOnly) return optimized
  return generate(optimized)
}
```

- [ ] Write `src/forge.js` (CLI entry, add `#!/usr/bin/env node` at top):

```js
#!/usr/bin/env node
import { readFileSync } from "fs"
import { compile } from "./compiler.js"

const [, , flag, file] = process.argv
const flags = {
  "--check": { syntaxOnly: true },
  "--parse": { parseOnly: true },
  "--analyze": { analyzeOnly: true },
  "--optimize": { optimizeOnly: true },
  "--generate": {},
}

if (!flag || !flags[flag] || !file) {
  console.error("Usage: forge --check|--parse|--analyze|--optimize|--generate <file>")
  process.exit(1)
}

try {
  const source = readFileSync(file, "utf8")
  const result = compile(source, flags[flag])
  if (typeof result === "string") process.stdout.write(result + "\n")
  else console.log(JSON.stringify(result, null, 2))
} catch (e) {
  console.error(e.message)
  process.exit(1)
}
```

---

### Task 9: Test Suite

Write comprehensive tests. Target: ≥100 tests, 100% coverage.

- [ ] Write `test/parser.test.js` — tests for every grammar form (valid + invalid)
- [ ] Write `test/analyzer.test.js` — one test per static rule (valid + each error)
- [ ] Write `test/optimizer.test.js` — one test per optimization
- [ ] Write `test/generator.test.js` — one test per construct
- [ ] Write `test/compiler.test.js` — end-to-end pipeline tests

---

### Task 10: Examples

- [ ] Write 5+ complete `.forge` programs in `examples/`:
  - `examples/hello.forge` — functions, if/else, string output
  - `examples/math.forge` — arithmetic, loops, exponent
  - `examples/bitops.forge` — bit indexing, bitwise ops
  - `examples/enum.forge` — custom types, hub/switch
  - `examples/globals.forge` — global declarations, field access

---

### Task 11: README + Final Verification

- [ ] Write complete `Plang/README.md`
- [ ] Run `npm test` — all tests pass, coverage reported
- [ ] Run `npm run format` — code prettied
- [ ] `git status` — no junk files tracked
