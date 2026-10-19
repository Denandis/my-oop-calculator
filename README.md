# OOP Calculator

A command-line calculator built for IS218 to practice object-oriented
programming, design patterns, testing, and continuous integration.
It supports `add`, `subtract`, `history`, `remove`, `help`, and `exit`.

## Install

    python3 -m venv .venv
    source .venv/bin/activate
    python -m pip install -r requirements.txt

## Run

    python -m calculator

## Test (100% line and branch coverage)

    python -m pytest

## Example session

    OOP Calculator

    Type "help" for commands.
    > add
    First number: 10
    Second number: 5
    Result: 15
    > subtract
    First number: 20
    Second number: 7
    Result: 13
    > history
    Calculation History

    1. Add: 10, 5 = 15
    2. Subtract: 20, 7 = 13
    > remove
    Calculation History

    1. Add: 10, 5 = 15
    2. Subtract: 20, 7 = 13
    Enter calculation number to remove: 1
    Removed: Add: 10, 5 = 15
    > exit
    Goodbye!

## Successful GitHub Actions run

(Add the link to your green Actions run here after pushing.)

## Coverage exclusions

None. The whole `calculator` package is covered by tests. The abstract
`get_result()` in `Calculation` has only a docstring body and is fully
exercised through its `Add` and `Subtract` subclasses.

## Method types

- Static-like behavior is not used here; instead each operation is an
  instance method. `get_result()` in `Add`/`Subtract` is an instance
  method — it uses the object's own `self.a` and `self.b`.
- Class method: `Calculation` is created through normal construction
  (`Add(10, 5)`); `self` refers to the specific object being built.
- Instance method: `History.add`, `History.remove`, and `History.get_history`
  all act on that History object's own `_calculations` list.

## Design patterns

- Factory-style construction: `cli.py` keeps a dictionary
  `operations = {"add": Add, "subtract": Subtract}` and constructs the chosen
  class, so the loop does not hard-code which operation to build.
- Strategy: the selected operation class is the strategy that decides how the
  two numbers are combined; the CLI treats them all the same way.
- Command-like flow: the REPL reads a command, selects behavior, executes it,
  and records successful results in history.

## OOP ideas

- Encapsulation: `History` keeps its list private as `_calculations` and only
  exposes `add`, `remove`, and `get_history`. `get_history` returns a copy, so
  callers cannot change the stored list directly.
- Abstraction: `Calculation` is an abstract base class that requires every
  subclass to provide `get_result()`. It defines the promise, not the details.
- Inheritance: `Add` and `Subtract` inherit `__init__` and the operands from
  `Calculation` and only add their own `get_result()`.
- Polymorphism: `describe()` and the main loop call `get_result()` without
  knowing whether the object is an `Add` or a `Subtract`.
- Composition: a `History` object holds many `Calculation` objects; a
  calculation holds its two operands. Objects are built from other objects.

## SOLID ideas

- Single responsibility: operations do math, `History` manages the collection,
  and `cli.py` handles input and output. Each part has one job.
- Open/closed: a new operation can be added by writing a new `Calculation`
  subclass and registering it in the CLI dictionary, without changing existing
  classes.
- Liskov substitution: any `Calculation` subclass can replace another because
  each returns a numeric result from `get_result()`.
- Interface segregation: a calculation only needs `get_result()`, not a large
  set of unrelated methods.
- Dependency inversion: the CLI and `History` depend on the `Calculation`
  abstraction rather than on `Add` or `Subtract` specifically.

## Stage 6 reflection: transferring the design

The core ideas here are not specific to Python. In another language such as
Java or C#, `Calculation` would be an abstract class or interface with a
`getResult()` method, and `Add` and `Subtract` would implement it. `History`
would hold a private list and expose controlled methods. Adding a new
operation (for example, `multiply`) or a new notification type would mean
adding a new small class that honors the existing contract and registering it,
rather than rewriting the loop or the history. The REPL, the error handling,
and the separation between the model and the input/output layer would stay the
same. This is the real benefit of the design: new behavior is added at the
edges without changing the parts that already work.
