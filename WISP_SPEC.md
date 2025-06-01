# WISP_SPEC.md

## Overview

**Wisp** is a human-friendly, declarative schema definition language for structured data. It offers expressive constraint syntax, logical validation expressions, and default value support, with an emphasis on clarity and extensibility. Wisp is whitespace-sensitive and designed to be both developer-friendly and machine-readable.

---

## Key Features

- Indentation-based structure (like YAML or Python)
- Named records (e.g., `User`) with typed fields
- Fields support type declarations, constraint chains, default values, and validation messages
- AND/OR logic based on whitespace sensitivity
- Extensible with aliases and meta blocks
- Emits JSON Schema and more

---

## Syntax Rules

### Record and Field Definitions

```wisp
User:
  name      string:min(1) "Name is required"
  email     string:email:min(5):max(50)
  ?age      int:min(0):max(27) :is(120) = 27 "Invalid age"
  height    int:all(:gt(100), :lt(300))
  verified  boolean!:is(false) = true
```

### Field Structure

```wisp
?field_name  type:constraint1:constraint2 ... = default_value "validation message"
```

| Element            | Description |
|--------------------|-------------|
| `?` (optional)     | Visual indicator of optional field (not required, but helpful) |
| `type`             | ECMAScript-style type (string, int, boolean, array, object, etc.) |
| `:constraint(...)` | Constraints applied to the field (e.g. `:min(0)`) |
| `= value`          | Default value, making the field optional |
| `"message"`        | Optional custom validation message (must come last) |

---

## Constraint Logic

### AND vs OR (Whitespace-Based)

| Syntax | Meaning |
|--------|---------|
| `:a:b` | A AND B |
| `:a  :b` | A OR B (2+ spaces = OR) |

### Logical Groups

```wisp
:all(:gt(100), :lt(200))
:any(:min(5), :is(10), :max(50))
:not(:email)
!:is(false)
```

---

## Constraint Aliases

Define reusable constraint groups:

```wisp
@age :min(0):max(120)

User:
  ?age  :age = 27 "Cool age"
```

---

## Meta Blocks

```wisp
@wisp:meta(
  version = 1.1.0
  name = "My App Schema"
)
```

---

## Supported Types (v1.0)

- `string`
- `int`
- `float`
- `boolean`
- `date`
- `array`
- `object`

---

## Future Roadmap (Post v1.0)

- Playground UI with live validation
- Code generation for TS, Python, JSON Schema, TOML/YAML
- Support for sets/maps and custom types
- Built-in validators and coercers

---

## License

Wisp is MIT licensed.
