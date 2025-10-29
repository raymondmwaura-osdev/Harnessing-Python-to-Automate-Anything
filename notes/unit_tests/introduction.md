# Introduction to Unit Tests

## What Are Unit Tests

### Definition

In software engineering, a *unit test* is a programmatic test designed to verify the correctness of the smallest testable part of an application, usually a function, method, or class, in **isolation**.
More formally, a unit is the minimal piece of code that can be executed independently and for which individual validation is meaningful.
Unit tests aim to assert that given specified inputs, the unit produces expected outputs or state changes under defined conditions.

### Characteristics of Unit Tests

Key characteristics include:

* **Isolation**: Unit tests should avoid dependence on external systems (databases, networks, file systems, OS services) so that failure indicates a defect in the unit, not in an external dependency.
* **Small scope**: Each test should exercise a single behaviour or aspect of the unit. Larger behaviour is then built from smaller units.
* **Automatable and repeatable**: Ideally, unit tests are executed automatically (for example, in a continuous integration pipeline) and should yield the same results each time.
* **Deterministic**: Results should not depend on non‐deterministic factors (timing, concurrency, randomness) unless explicitly handled.
* **Fast execution**: Because they are granular and isolated, unit tests should run quickly; this facilitates frequent execution.
* **Executable “specification”**: Well‐written unit tests serve as an executable form of specification for the behaviour of the unit.


---

## Why Unit Tests Matter

### Early Defect Detection

One of the principal advantages of unit testing is that it allows defects to be detected **early** in the software development lifecycle; at the moment when a unit is implemented or changed, rather than after integration or deployment.
This early detection is valuable because the cost of fixing a defect rises steeply the later it is found (for example during system testing or in production).

### Improved Code Design and Modularisation

Writing unit tests encourages developers to design code in a modular, decoupled fashion, because units must be testable in isolation. This tends to lead to cleaner, more maintainable code.

### Facilitates Refactoring and Safe Change

When unit tests exist for a module, developers can refactor or change its internals with greater confidence: if existing unit tests continue to pass, the module’s external contract is likely preserved.

### Documentation and Specification

Unit tests serve as *executable documentation*: by reading the tests, one gains insight into how the unit is intended to work, what input arguments are valid, what edge cases matter.
This is very useful for new team members or for yourself future-you returning to legacy code.

### Reduced Risk in Integration and Deployment

Because individual units are validated early, when they are integrated into larger subsystems the risk of defects or regressions is reduced. In consequence, system testing becomes less burdensome and release cycles can be more frequent.

### Improved Productivity and Maintainability

Although writing unit tests takes effort upfront, in the longer term they reduce debugging time, regression fixes, and allow faster development cycles.

### Encouragement of Test-able and Robust Architecture

Because the act of writing tests forces you to think about how the unit will be exercised under various conditions (including edge and error cases) you naturally build more robust architecture.

---
