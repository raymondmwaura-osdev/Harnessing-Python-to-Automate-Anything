# `logging`

## Motivation and Purpose

* The `logging` module provides a **flexible, standardised facility** for producing log messages from Python programs: messages about program flow, warnings, errors, diagnostics, and other events.
* Compared to using ad-hoc `print()` statements, logging offers structured, configurable output (including severity levels, timestamping, output destinations (console, files, etc.), and hierarchy) making it well suited for both small scripts and large, modular applications.
* Because many third-party Python libraries themselves use `logging`, adopting it ensures that logs from your code and libraries can be unified into a coherent log for monitoring, debugging, and auditing.

---

## Core Concepts and Architecture

The `logging` module is built around several core abstractions.

+ **Logger**; The entry point for your code to issue logging calls (e.g., debug, info, warning, error, critical). A logger is identified by a name (often the module name).
+ **Handler**; Defines where log records go; e.g., to console (stream), to a file, over the network, etc. You can attach zero or more handlers to a logger.
+ **Formatter**; Controls the layout/format of the log message (timestamp, logger name, severity, message, custom metadata).
+ **Log level / Severity**; A threshold indicating the importance/severity of each log record. Only messages at or above the configured level are emitted.

The standard severity levels (in increasing order) are:

* `DEBUG` (numeric value 10)
* `INFO` (20)
* `WARNING` (30)
* `ERROR` (40)
* `CRITICAL` (50)

Additionally, there is a `NOTSET` level (0), used in certain advanced configurations.

---

## Quick Start

The simplest way to start using logging is to rely on the module-level interface and default configuration:

```python
import logging

logging.warning("Watch out!")
logging.info("Starting the application")
logging.debug("Detailed debug information")
```

By default:

* The root logger is used.
* The default severity threshold is `WARNING`. Thus, in the example above, only the warning (or higher) message is output.

Example output:

```
WARNING:root:Watch out!
```

This simplicity is convenient for quick scripts or small tasks.

---

## Configuring Logging; `basicConfig`

For greater control (such as changing the log level, specifying output files, or formatting) you can configure logging using `logging.basicConfig(...)`.

Typical parameters:

* `level`: the minimum severity to log (e.g., `logging.DEBUG`, `logging.INFO`)
* `filename`: name of a file to which to write logs (instead of the console); results in a `FileHandler`.
* `filemode`: mode for opening the log file; e.g., `'a'` (append, default) or `'w'` (overwrite).
* `format`: a format string controlling how each log record is rendered (timestamp, logger name, severity, message, etc.)
* `datefmt`: specification for how timestamps should be formatted, if the format includes a timestamp.

Example of a more structured setup:

```python
import logging

logging.basicConfig(
    filename="app.log",
    level=logging.INFO,
    filemode='w',
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)

logging.info("Application started")
logging.error("An error occurred")
```

This will write logs to `app.log`; because `filemode='w'`, the file is overwritten each run (rather than appended). Only `INFO`, `WARNING`, `ERROR`, and `CRITICAL` messages will be recorded; `DEBUG` messages will be ignored under this configuration.

---

## Using Named Loggers and Modular Logging (for Larger Applications)

For non-trivial applications or systems with multiple modules, it is considered best practice to create **named logger instances**; rather than relying on the root logger. This improves clarity and modularity.

Typical workflow in a module:

```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

# Optionally, create and add handlers/formatters here; or rely on root configuration.
logger.info("Module initialized")
logger.error("Something went wrong")
```

* `getLogger(name)` returns a logger associated with the given name. If called multiple times with the same name, the same logger object is returned.
* This facilitates hierarchical or module-wise logging configurations: parent loggers, child loggers, per-module settings.
* In a larger application, central configuration (handlers, formats, thresholds) is typically done once (e.g., at the program entry point), while modules simply fetch and use their named loggers.

This separation allows:

* Modular code reuse (each module logs under its own namespace).
* Fine-grained control over verbosity per module (e.g., DEBUG for your modules, WARNING for imported libraries).
* Easier debugging, tracing, and maintenance when reading logs in production or during development.

---

## Advanced Features (Handlers, Formatters, Multiple Outputs, Library-Friendly Logging)

While the basic configuration covers the majority of common use cases, the `logging` module supports advanced features enabling production-grade logging infrastructure.

* **Multiple handlers**: A logger can dispatch log records to multiple destinations; for example, one handler writing to a file, another streaming to console.
* **Different handlers’ levels**: Each handler can have its own threshold. This lets you direct verbose debug logs to a file while only writing warnings or above to the console. (Not always trivial via `basicConfig`; often requires manual handler setup.)
* **Custom formatting**: Using `Formatter`, you can embed additional context into log records (module name, timestamp, function name, custom metadata, etc.), which greatly aids debugging and traceability.
* **Library-friendly logging**: If you're writing a library (not an application), it’s good practice to use named loggers, but *not* to configure handlers or logging levels; leave configuration to the application that imports your library. This avoids unintentional interference with application-wide logging settings.
* **Flexibility and extensibility**: The module allows custom log record factories, custom logger classes, custom handlers (e.g. for HTTP, memory buffering, external services), and more; making it powerful enough for enterprise-grade logging systems.

---

## Recommended Basic Workflow (for Most Applications)

1. In your “main”/entry point (e.g., `if __name__ == "__main__": …`), configure logging: choose your handlers, log level, format, output destinations.
2. In each module, obtain a logger via `logging.getLogger(__name__)`.
3. Use the logger to record relevant events: use appropriate severity levels (`DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`) depending on the significance of the event.
4. Avoid sprinkling bare `print()` statements for debugging/logging; use `logging` for any event that matters for debugging, auditing, monitoring, or post-mortem analysis.
5. If building a library: do not perform global logging configuration. Only obtain a named logger and issue log calls. Let the embedding application configure handlers and levels.

---

## Sample Minimal Setup + Example

```python
# main.py
import logging
import module_a

def setup_logging():
    logging.basicConfig(
        level=logging.INFO,
        format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
        filename='app.log',
        filemode='w'
    )

if __name__ == "__main__":
    setup_logging()
    logging.info("Application started")
    module_a.do_work()
```

```python
# module_a.py
import logging

logger = logging.getLogger(__name__)

def do_work():
    logger.debug("Entered do_work")       # won't appear (threshold INFO)
    logger.info("Doing work")
    try:
        x = 1 / 0
    except Exception:
        logger.exception("Unexpected error")
```

Expected behavior:

* `app.log` will contain timestamped `INFO` log from main, plus the `INFO` and full exception trace from `module_a`.
* The debug log from `module_a` will be suppressed because the level threshold is `INFO`.
* You get structured, timestamped, and contextual logs; far more useful than print-based debugging.

---
