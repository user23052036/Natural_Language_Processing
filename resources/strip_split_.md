`strip()`, `split()` are real Python string methods.

`trim()` is **not** a Python string method.

A lot of beginners confuse this because languages like JavaScript have `.trim()`.

---

# 1. `strip()`

Used to remove characters from the **beginning and end** of a string.

Most commonly: remove spaces/newlines/tabs.

Example:

```python
text = "   hello   "

print(text.strip())
```

Output:

```python
hello
```

Important:

It does **NOT** remove spaces from the middle.

```python
text = "   hello world   "

print(text.strip())
```

Output:

```python
hello world
```

---

You can also specify characters to remove:

```python
text = "!!!hello???"

print(text.strip("!?"))
```

Output:

```python
hello
```

Python keeps removing those chars from both ends until it hits something else.

---

There are also:

## `lstrip()` → left strip

```python
text = "   hello   "

print(text.lstrip())
```

Output:

```python
hello   
```

---

## `rstrip()` → right strip

```python
print(text.rstrip())
```

Output:

```python
   hello
```

---

# 2. `split()`

Used to break a string into pieces.

Returns a list.

Example:

```python
text = "apple banana mango"

print(text.split())
```

Output:

```python
['apple', 'banana', 'mango']
```

Default behavior:

* splits using whitespace
* handles multiple spaces/tabs/newlines automatically

---

You can specify a separator:

```python
text = "apple,banana,mango"

print(text.split(","))
```

Output:

```python
['apple', 'banana', 'mango']
```

---

Very important NLP usage:

```python
sentence = "I love Python"

words = sentence.split()
```

Now:

```python
['I', 'love', 'Python']
```

This is called tokenization (basic version).

---

# 3. `trim()`

Python:

```python
"hello".trim()
```

Output:

```python
AttributeError
```

Because Python strings do not have `.trim()`.

Equivalent in Python is:

```python
.strip()
```

---

# Internal behavior difference

## `strip()`

Does NOT modify original string.

Strings are immutable.

```python
text = " hello "

text.strip()

print(text)
```

Still:

```python
 hello
```

You must assign:

```python
text = text.strip()
```

---

## `split()`

Creates a new list object.

```python
text = "a b c"

x = text.split()
```

Memory now contains:

```python
['a', 'b', 'c']
```

---

# Common beginner mistake

This:

```python
text.split
```

is a method object.

This:

```python
text.split()
```

actually calls the method.

Huge difference.

---

# Practical NLP pipeline example

Typical preprocessing:

```python
text = "  Hello, world!  "

text = text.strip()
text = remove_punc(text)
words = text.split()

print(words)
```

Output:

```python
['Hello', 'world']
```

That is essentially the beginning of many NLP preprocessing pipelines.
