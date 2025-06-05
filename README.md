# 🧚 Wisp Language Specification (WIP-- pre-v1)

## ✨ Overview

Wisp is a declarative, whitespace-sensitive DSL for defining human-readable, structured schemas with expressive constraints.

Its primary goals are:

- **Clarity** — schemas are designed to be read and written by humans.  
- **Expressiveness** — rich constraint logic, defaults, and validation hints.  
- **Tooling-agnostic** — usable across runtimes (LLMs, JSON Schema, codegen, etc.)

---

## Syntax Rules

### 🧐 Show Me

```wisp
@wisp:meta(
  version = "1.0.0"
  name = "My Schema"
)

User:
  name      string:min(1)
  email     string:email:min(5):max(50)
  ?age      int:min(0):max(27) :is(120) = 27 "uncool age bro"
  height    int:all(:min(100), :max(300))
  verified  boolean!:is(false) = true
```

---

## 🧑‍💻 Field Definitions

```text
[?]fieldName  [type] [:constraint[:...]] [= default] ["validation message"]
```

| Component       | Description |
|----------------|-------------|
| `?` (optional) | Visual-only cue for optional fields. Optional fields are defined by `= default`, not this symbol. |
| `fieldName`    | Required. Alphanumeric with `_` and `-`. |
| `type`         | Optional. Defaults to `string`. |
| `:constraint`  | Optional. Colon-prefixed constraint expressions (can be nested or negated). |
| `= default`    | Optional. If present, field is optional. |
| `"message"`    | Optional. Always comes last if present. Overrides inferred validation message. |

---

## 🔗 Constraint Syntax

- Constraints **start with `:`** and can be **chained for AND logic**
- One or more **spaces between constraints = OR logic**
- Negation uses `!:` before the constraint

### ✅ Constraint Examples:

```wisp
:min(0):max(10)                 # AND
:min(0)  :is(999)               # OR
!:is(false)                     # NOT
:all(:min(5), :max(10))           # ALL
:any(:is("yes"), :is("y"))      # ANY
```

---

## 🧬 Typing

### ✅ Primitives (and dates 😉)

- `string`
- `int`
- `float`
- `boolean`
- `date`

### 🚧 (Planned)

- `array`, `object`, `map`, `set`
- Constructor-style: `String`, `Number`, `Date`, etc.

---

## 🎭 Constraint Aliasing

```wisp
@age  :min(0):max(120)

User:
  ?age  int:age = 27 "cool age bro"
```

- Define reusable constraint aliases using `@name :constraint...`
- **Only constraints** may be aliased — **not** types or record names

---

## 💬 Validation Messages

- Optional custom validation messages follow the default value:

```wisp
?email  string:email = "no-reply@example.com" "Must be a valid email."
```

- If omitted, inferred messages are generated:

```text
age must be at least 0; age must not exceed 120; age must equal 120
```

---

## 📚 Meta Block

```wisp
@wisp:meta(
  version = "1.0.0"
  name = "My App Schema"
)
```

- Declares schema-level metadata like version and schema name

---

## 🔜 Post-Parse Features

- Whitespace-sensitive logic resolution (AND vs OR)
- Emission of **JSON Schema** (and eventually YAML, TOML, TS, etc.)
- Support for `:re(...)` constraints with **regex-based pattern inference**
