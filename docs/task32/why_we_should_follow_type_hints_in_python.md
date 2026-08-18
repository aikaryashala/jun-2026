# Why We Should Follow Type Hints in Python

## Core Principle

Going forward, our Python coding standard is:

> **Every variable should have a clearly intended type, and that type
> should not change during the variable's lifetime.**

``` python
name: str = "Ravi"
age: int = 20
marks: list[int] = [80, 90, 85]
```

Avoid reusing a variable for another type:

``` python
age: int = 20
age = "twenty"   # Don't do this
```

Python is still dynamically typed; this is a **coding discipline**, not
a claim that Python is statically typed.

## Why This Matters

### 1. Makes intent explicit

``` python
age: int = 20
```

The type of the data is immediately visible to students, developers, and
tools.

### 2. Makes data structures easier to understand

``` python
student: dict[str, str] = {
    "name": "Ravi",
    "city": "Vizag"
}
```

Students immediately learn:

``` text
dict[key_type, value_type]

dict[str, str]
     ↑    ↑
   key   value
```

Similarly:

``` python
marks: dict[str, int]
numbers: list[int]
names: list[str]
```

This makes the **shape of data** explicit.

### 3. Makes function contracts clear

``` python
def calculate_average(marks: list[int]) -> float:
    ...
```

Students can immediately see:

``` text
Input  → list[int]
Output → float
```

### 4. Helps tools catch mistakes

Type checkers such as Pyright and mypy can detect inconsistent
assignments and incorrect function arguments before runtime.

### 5. Improves AI code generation

This is especially important for AI-native development.

Compare:

``` python
data = get_student()
```

with:

``` python
data: dict[str, int] = get_student()
```

The second gives an AI coding tool much stronger information about the
programmer's intent.

Type hints therefore act as **constraints on AI-generated code**:

``` text
Less information
      ↓
more possible interpretations
      ↓
more opportunities for incorrect code

More type information
      ↓
fewer possible interpretations
      ↓
more precise code
```

### 6. Creates a foundation for modern Python

Type hints naturally lead students from:

``` text
Variables
   ↓
list[T] and dict[K, V]
   ↓
Functions with typed interfaces
   ↓
Classes / dataclasses
   ↓
JSON / APIs
   ↓
Backend applications
   ↓
AI applications
```

## Our Python Coding Standard

1.  **Use type hints for variables.**
2.  **Keep a variable's intended type stable.**
3.  **Specify function parameter and return types.**
4.  **Specify element types for lists and key/value types for
    dictionaries.**
5.  **Use a type checker in larger projects.**

## The Bigger Picture

Type hints benefit **three participants** in modern software
development:

``` text
                         Explicit Types
                              │
               ┌──────────────┼──────────────┐
               ↓              ↓              ↓
            Student        Developer      AI Tools
               │              │              │
               ↓              ↓              ↓
          Understand       Understand      Generate
           data better      code better    better code
```

-   **Students** → clarity and programming discipline
-   **Developers** → contracts, readability, and maintainability
-   **AI tools** → stronger context and constraints for code generation

> **Write code that clearly communicates the shape, type, and intent of
> data --- to humans and to AI coding tools.**
