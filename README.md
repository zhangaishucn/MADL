# MADL

**Markdown Agent Description Language**

Write workflows as documents. Run documents as programs.

MADL is a Markdown-embedded language for describing **agent workflows, tool orchestration, long-running tasks, host callbacks, and recoverable execution** in a form that stays readable to humans.

It is not a prompt format. It is not a thin wrapper around JSON. It is a language proposal for treating **documents as executable flow definitions**.

---

## Why MADL

Most agent systems today split the world in awkward ways:

- prompts live in strings
- orchestration lives in code
- workflow state lives in runtime internals
- human approval, callbacks, waiting, and recovery are bolted on later

MADL starts from a different assumption:

> A workflow should be readable like a document, structured like a program, and resumable like a runtime.

That means one source file can describe:

- control flow
- data flow
- tool calls
- model calls
- human-in-the-loop steps
- host-delegated async work
- suspension and resumption points

without giving up static structure.

---

## Core ideas

### Markdown-first

MADL is embedded in Markdown instead of replacing it. Explanatory text and executable logic can live in the same file.

### Explicit over implicit

MADL avoids hidden variable creation, hidden capture, hidden shared mutable state, and hidden agent loops.

### Value semantics by default

` .const ` creates immutable bindings. ` .value ` creates isolated mutable values. The language does not expose shared-reference mutation as observable semantics.

### Host-coordinated execution

Long-running waits, callbacks, delegated HTTP, recovery, and agent/tool loops are treated as first-class runtime scenarios, not edge cases.

### Static structure matters

Flow names, tool references, and strategy clauses are designed to stay statically analyzable.

---

## What makes it different

MADL is designed for workflows that are not just “call model, get text”.

It aims at systems where you need to express things like:

- ask a model for a decision
- call tools conditionally
- wait for an approval callback
- sleep and resume tomorrow
- delegate work to the host runtime
- preserve VM state across suspension
- keep source files readable enough for review

In other words: **agent execution with real control flow, real waiting, and real recovery semantics**.

---

## Example

```md
---
name: demo/approval
parameters:
  - order_id
---

## .callback {"type":"approval","order_id":order_id} -> token
## .emit {"approval_token": token, "order_id": order_id}
## .await token -> approval

## .if approval.status == "approved"
### .return {"ok": true, "order_id": order_id}
## .else
### .return {"ok": false, "reason": approval.reason}
```

This is the kind of workflow MADL is built for: readable, explicit, suspendable, and host-friendly.

---

## Current status

MADL is currently a **language specification project**.

At this stage, the repository focuses on:

- the core language model
- syntax and block structure
- binding and value semantics
- control flow and iteration
- host coordination primitives
- model and agent invocation semantics
- consistency and error conditions

There is **no production runtime yet**.

That is intentional.

The current goal is to make the language model precise before locking in parser, VM, host protocol, and execution strategy decisions.

---

## Non-goals

MADL is not trying to be:

- a general-purpose programming language
- a Markdown templating trick
- a vendor-specific prompt schema
- a hidden-code agent framework
- a purely synchronous scripting format

The point is not to hide complexity.
The point is to make workflow complexity **explicit, structured, and recoverable**.

---

## Project direction

The design currently explores a language core centered around:

- one document = one flow
- Markdown heading-based block structure
- explicit data-flow operators (`->`, `=>`, `->>`)
- immutable and mutable value bindings
- host-facing suspension points
- single-shot model calls and higher-level agent orchestration

Future work may include:

- parser and AST
- static checker
- VM and resume model
- host integration protocol
- task model / concurrency model refinement
- tooling, highlighting, and editor support

---

## Read the spec

See:

- [`MADL_语言规范_v0.1.0.md`](./MADL_语言规范_v0.1.0.md)

---

## Manifesto

MADL comes from a simple belief:

**Workflows deserve a real language.**

Not just strings. Not just glue code. Not just invisible runtime magic.

If agents are going to coordinate tools, wait for people, survive suspension, and run across host boundaries, then those semantics should be visible in the source language itself.

MADL is an attempt to make that visible.
