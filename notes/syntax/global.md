# `global`

## The Purpose and Semantics of the `global` Statement

The `global` statement in Python changes how the interpreter treats certain identifiers inside the current scope (function, module, or class definition).

```python
global identifier ("," identifier)*
```

Declaring an identifier with `global` makes assignments to that name in the current scope refer to the variable in the module’s global scope. Without it, assignment would create a new local variable.

### Important Notes

* **Read access to globals**; you don’t need `global` if you only read (i.e. inspect) a global variable inside a function.
* **Write/rebind access**; to reassign or create a global variable inside a function, you must declare it as `global`. Otherwise Python will treat the assignment as creating a new local variable.
* **Effect outside functions**; a `global` statement at module (top) level has no effect: module variables are already global by default.

---

## Typical Use Cases

### Reading global variables from inside a function

```python
x = 42  # global

def foo():
    print(x)  # just reading

foo()
# → 42
```

No `global` needed because we are not assigning to `x`.

---

### Rebinding (modifying) a global variable inside a function

Without `global`, this fails:

```python
x = 1

def bad():
    x = x + 1  # attempt to change global x
    print(x)

bad()  
# → UnboundLocalError: local variable 'x' referenced before assignment
```

Because assignment makes `x` local, but you read it before its assignment.

Fix with `global`:

```python
x = 1

def good():
    global x
    x = x + 1
    print(x)

good()
# → 2
print(x)
# → 2
```

With `global x`, you tell Python “use the module-level `x`,” so assignment rebinds the global name.

---

### Creating a new global variable from within a function

You can also declare and define a global variable inside a function:

```python
def init():
    global y
    y = "initialized"

init()
print(y)  
# → initialized
```

Here `y` did not exist before; the `global` statement creates it at module level.

---

### `global` in nested functions

`global` works even inside nested functions: regardless of nesting level, `global name` binds to module-level `name`.

```python
def outer():
    def inner():
        global z
        z = "set in inner"

    inner()

outer()
print(z)
# → "set in inner"
```

That said; if you intend to modify variables in an enclosing (non-global) scope, use `nonlocal` instead.

---

## Underlying Mechanism and Language Reference Notes

* The `global` statement is processed by the Python parser before code execution; it affects how names are bound in the current code block.
* Python distinguishes **free variables** (names used but not defined in the current scope) from **local variables** (names assigned in the current scope). Without `global`, assignment always makes a name local, even if there is a global name of the same identifier.
* Using `global` does **not** automatically make a name “special” beyond binding to the module namespace; it simply ensures assignment refers to the global namespace.

---

## Pitfalls, Limitations, and Best Practices

* Global state makes code harder to reason about; functions with side-effects (modifying globals) are harder to test, debug, and reuse.
* Overuse of global variables can lead to “spaghetti code”; unpredictable dependencies and coupling.
* For better modularity: prefer passing parameters to functions, returning results, or using objects/modules/classes to encapsulate shared state.
* If you use mutable global objects (lists, dicts), you may not need `global` to modify their contents; only for rebinding.
* For large projects, consider managing state through dedicated classes or data-structures, rather than scattered global variables.

---

## When to Use `global`; and When You Should Probably Avoid It

**Use `global` when:**

* You genuinely need to define or modify module-level state from inside a function.
* You are writing quick scripts or small programs where global variables simplify state sharing.

**Avoid or minimize `global` when:**

* You are writing larger, production-scale, or long-lived software; side-effects and global state reduce maintainability.
* You can refactor code to pass data via function parameters, return values, or use classes/objects to manage state.
* You need thread safety or reentrancy: global mutable state is often a poor fit.

---
