---
name: reusable-bricks
description: Extract the generic part of an app into a reusable, decoupled brick as soon as there is a second real use.
---

# Reusable bricks

The cost of the 1st app is development. The cost of the 5th is **repetition**: the same image resizing rewritten five times, with five different bugs. A decoupled brick costs a bit more up front and pays back from the 2nd use.

## Spotting a brick

A piece of code is a brick if **all three** conditions are met:

1. It answers a need that has nothing to do with the app's business domain (resize an image, send an email, sign a URL).
2. It can be described by a signature: inputs to outputs, without mentioning the app.
3. You can picture it in another project without effort.

Conversely, anything that touches business rules, the data schema or the user journey **is not** a brick: trying to generalize it produces needless abstraction.

Trigger rule: **the 2nd time you write the same thing, extract it.** Not the 1st (premature), not the 3rd (too late, three diverging variants to reconcile).

## The five decoupling rules

1. **Stateless.** Inputs to outputs. No global variable, no implicit home-made cache, no file written at a hardcoded path. If state is needed, it is **passed in** as an argument, not held.
2. **Configuration injected, never read.** The brick receives its parameters (keys, URLs, folders) as arguments or through a config object passed at initialization. It **never** reads `process.env` or `os.environ` itself: that is what welds it to an app.
3. **Zero imports from the host app.** No `from app.models import ...`. If the brick needs a type from the app, the boundary is in the wrong place.
4. **Primitive inputs and outputs.** Paths, bytes, dictionaries, standard types, not the app's ORM models.
5. **Typed errors, raised to the caller.** The brick raises its own explicit errors and does not decide in place of the caller (no `sys.exit`, no fatal log, no silent return of `null`).

## Extraction procedure

1. Isolate it in a separate folder or package (`packages/<brick>` in a monorepo, or a dedicated repository).
2. **Write the README before the code**: signature, one example call, known limits. Three lines are enough.
3. A test suite that depends on no app: this is the most honest decoupling test.
4. Version it (even a simple `0.1.0`): without a version, the 2nd app that uses it gets broken by the 3rd.
5. Wire the host app to the brick and **delete the old code**: otherwise the two diverge.

## Where to keep the list of bricks

A single list, `BRICKS.md` (name, one-line signature, location, version, apps that use it). Do not assume it exists: look for it in the connected files and in memory, and create it at the first extraction in a doc or folder chosen by the user, telling them where it is.

## Rules

- Extract from two **real** uses, not two assumed ones.
- A brick that takes more than six configuration parameters is probably two bricks.
- Before writing a cross-cutting feature: **first check whether the brick already exists** in the list. This is the reflex that makes all the rest pay off.
- End with the proposed next step or steps.
