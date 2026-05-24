# Python Strings — Parser Internals & Raw Strings

## Core Idea

Python reads strings **character by character**.  
The backslash `\` is an **escape character** — it tells the parser:

> "The next character should be interpreted specially."

---

## How the Parser Works

### Example 1 — `"\n"`

| Stage | What Python sees |
|---|---|
| Source code | `\` then `n` |
| Parser interprets | `\n` → newline escape |
| Stored in memory | `[newline character]` — **1 character** |

The two characters `\` and `n` in source code collapse into **one** character in memory.

---

### Example 2 — `"\\"`

| Stage | What Python sees |
|---|---|
| Source code | `\` then `\` |
| Parser interprets | first `\` is the escape instruction; second `\` means "insert a literal backslash" |
| Stored in memory | `\` — **1 character** |

---

## The Biggest Misconception

People look at `"\\"` and think it contains **two backslashes**.

**Wrong.**

| | Count |
|---|---|
| Characters in **source code** | 2 (`\` and `\`) |
| Characters in **memory** | 1 (a single `\`) |

> That distinction — **source code vs memory** — is the whole game.

---

## Why Raw Strings Exist

Normally Python **processes** backslashes during parsing.  
A raw string (prefix `r`) tells Python:

> "Do NOT treat backslashes specially. Take every character literally."

### Comparison

| String | Stored in memory | Character count |
|---|---|---|
| `"\n"` | `[newline]` | 1 |
| `r"\n"` | `\` + `n` | 2 |
| `"\\"` | `\` | 1 |
| `r"\\"` | `\` + `\` | 2 |

---

## Your Confusing Example — `r"\\"`

Since this is a **raw** string, Python does **not** process escapes.  
It stores every character literally:

```
\ + \   →   2 backslashes in memory
```

So `len(r"\\")` is `2`, not `1`.

---

## Why Raw Strings Cannot End With a Single Backslash

```python
r"\"    # SyntaxError
```

Python reads:
1. Opening quote `"`
2. Backslash `\`
3. Parser thinks: *"this backslash is escaping the next quote"*
4. The closing `"` is consumed as an escaped quote, not as the end of the string
5. The string **never closes** → `SyntaxError`

Even in raw strings, the backslash still has one remaining power: it can escape the **quote character** at the string boundary. This is the one edge case where raw strings are not fully "raw".

---

## `print()` vs `repr()` — Two Completely Different Things

This is where most confusion lives.

### `print(s)` → shows **actual characters in memory**

### Writing `s` alone in Jupyter / REPL → shows **`repr(s)`**

`repr()` escapes special characters so the output is valid Python source code that could recreate the string. It is **not** showing you the memory content — it is showing you a **safe representation** of it.

---

## The repr() Escaping Rule

Every real backslash in memory must be shown as `\\` in repr(), because a single `\` in source code would be misread as an escape.

| Actual characters in memory | `repr()` displays |
|---|---|
| `\` (1 backslash) | `\\` |
| `\\` (2 backslashes) | `\\\\` |

---

## Full Worked Example

```python
s = r'\\'
```

**Step 1 — What is in memory?**  
Raw string, so no escape processing. Stores `\` + `\` → **2 backslashes**.

**Step 2 — `print(s)`**  
Prints the actual characters:
```
\\
```
Two backslashes on screen. Correct.

**Step 3 — `s` alone in Jupyter**  
Jupyter calls `repr(s)`. Each `\` becomes `\\` in repr:
```
'\\\\'
```
Looks like 4 backslashes, but that is just the representation. Memory still has 2.

**Step 4 — The definitive proof**
```python
print(len(s))   # 2
```
Memory contains exactly **2** characters. The four in the repr display are an illusion of representation.

---

## Mental Model Summary

```
Source code  ──[Python parser]──►  Memory  ──[print()]──►  Screen output
                                      │
                                      └──[repr()]──►  Jupyter / REPL display
```

- `print()` and `repr()` read from the **same memory**.
- They just **display** it differently.
- `repr()` adds extra escaping so the output is valid Python syntax.
- `print()` shows the raw characters as-is.

---

## Quick Reference

| You write | Memory holds | `print()` shows | `repr()` shows | `len()` |
|---|---|---|---|---|
| `"\n"` | newline | (blank line) | `'\n'` | 1 |
| `"\\"` | `\` | `\` | `'\\'` | 1 |
| `r"\n"` | `\n` | `\n` | `'\\n'` | 2 |
| `r"\\"` | `\\` | `\\` | `'\\\\'` | 2 |

---

## Why This Matters for Regex

Regex patterns need real backslashes in memory (e.g., `\d`, `\w`, `\s`).

Without raw strings you would have to double-escape everything:

```python
pattern = "\\d+"    # memory: \d+   ✓ but ugly
pattern = r"\d+"    # memory: \d+   ✓ clean and readable
```

Both work. Raw strings are not magic — they just save you from writing every `\` twice. The regex engine always receives whatever is **in memory**, never the source code representation.