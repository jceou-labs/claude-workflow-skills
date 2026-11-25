---
name: plan-first
description: Write the plan to disk BEFORE touching the code, split it into phases with validation gates, and record structural decisions in a log (ADR). Use AS SOON AS a request involves more than a trivial change (new feature, refactor, migration, technical choice) or when the user says "let's go with", "how do we approach this", "make me a plan", "I have an idea for", "we should add". Prevents drift and survives context compaction.
---

# Plan on disk before code

A plan that lives only in the context dies at the first compaction, and the work drifts. A plan on disk survives, can be reread, criticized, and handed to another agent.

## What to apply when

| Scale | Treatment |
|---|---|
| Localized fix, one function | Built-in plan mode is enough, no file |
| Feature, refactor of a module | `PLAN.md` + phases + gates |
| New project, migration, architecture | `PLAN.md` + `ARCHITECTURE.md` + one ADR per decision |

Do not over-document a three-line `if`: it is the only way to keep the docs credible.

## Procedure

### 1. Frame before writing

If the goal, the constraints or the success criterion are unclear, first look for the answer (files, project docs, memory, conversation), then decide on your own and write the chosen assumption in one line at the top of `PLAN.md` and in the reply. Only interview the user if they asked for it explicitly. One exception: a decision that is hard to undo and could reasonably go either way is submitted to the user before executing.

### 2. Write `PLAN.md`

```markdown
# <Goal in one sentence>

## Success criterion
<observable, testable, not "it works better">

## Out of scope
<what we do NOT do, explicitly>

## Constraints
<technical, existing, non-negotiable>

## Phases
### Phase 1: <name>
- [ ] <step>
- [ ] <step>
**Exit gate**: <command/test that must pass before phase 2>

### Phase 2: ...

## Risks
| Risk | Mitigation |
|---|---|

## Files touched (forecast)
<list: serves as a guardrail, touching a file outside the list = sign of drift>
```

### 3. Have the plan criticized before executing it

A plan written by the one who will execute it is complacent. Submit it to an independent review (an agent/session without the context of the drafting): see `adversarial-review`. Fix it, and only then start.

### 4. Execute phase by phase

- Tick the boxes in `PLAN.md` **as you go**, not at the end.
- Do not cross an exit gate without having run the validation command that proves it. A gate not passed = stop and say so.
- Touching a file outside the planned list: flag it and update the plan, never silently.

### 5. Record structural decisions

Keep decisions in a permanent log, separate from the plan: `DECISIONS.md` for a simple project, or `docs/adr/NNNN-title.md` for a serious project, with a pointer line in `DECISIONS.md`. Never create a second decision file.

```markdown
# ADR 0007: <decision in one sentence>
Date: <ISO> · Status: accepted | superseded by ADR NNNN

## Context
<the problem, the constraints of the moment>

## Decision
<what was chosen>

## Alternatives rejected
- <option>: rejected because <reason>

## Consequences
<what it costs, what it locks in, what will need watching>
```

**Triviality filter**: an ADR only if the decision (a) is costly to undo, (b) constrains future code, or (c) will surprise someone reading the code without context. Naming a variable is not an ADR.

The real value: a future agent who does not understand why the code is the way it is will "fix" it. The ADR stops it.

## Rules

- The plan is a contract, not a note of intent: if it changes, it gets rewritten.
- No code may be written until the success criterion is written down in black and white.
- Reread the existing ADRs before proposing a technical choice: do not relitigate a trade-off already settled.
