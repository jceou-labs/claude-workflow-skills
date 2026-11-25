---
name: stack-discipline
description: Personal and small-team projects: keep a single stack and a single deployment target, and justify any divergence.
---

# Stack discipline (personal and small-team projects)

Multiplying providers multiplies accounts, bills, CLIs, dashboards, update procedures and failure modes. The cost is not paid at the moment of the choice, it is paid **six months later**, in administration time, and it grows with the number of apps.

Principle: **one default stack, written down in black and white, and every exception is justified.**

Scope: personal or small-team projects (dashboards, home automation, assistants, other apps).

## 1. The stack charter

A single file, outside the app repositories: `STACK.md`. Do not assume it exists: look for it in the connected files and folders and in memory. If it does not exist, offer to create it (in a doc or notes folder chosen by the user) and pre-fill it.

```markdown
# Default stack - <date of last review>

| Layer | Default choice | Accepted alternative | Forbidden |
|---|---|---|---|
| Hosting / deployment | <only one> | - | <what we do not reopen> |
| Database | | | |
| Files / objects | | | |
| Authentication | | | |
| Transactional emails | | | |
| Scheduled tasks | | | |
| Observability / logs | | | |
| Secrets | <see secrets-centralization> | | |
| Front end | | | |
| Back end / runtime | | | |
| CI | | | |

## Exception rule
A divergence is accepted if, and only if, the constraint is technical
and documented here, with the date and the app concerned.
```

If the charter does not exist: rebuild it from the existing projects and **decide** the layers where there are currently two tools for the same role. That is where the immediate gain is.

## 2. At the start of a project

Do not reopen the debate: apply the charter. The "which tech for this project" discussion only happens if the charter is silent on a layer. In that case, propose a choice consistent with what exists and announce it directly.

## 3. When faced with a wish to diverge

Ask the three questions, in this order:

1. **Is the constraint real?** Distinguish "the default cannot do this" (constraint) from "the other tool is more fashionable" (wish). Nine times out of ten, it is the second case.
2. **Can the default do the job, even imperfectly?** A mediocre solution in the known stack beats an ideal solution in one more stack.
3. **Who pays for the gap?** Count honestly: one more account, one more bill, one more CLI, one more deployment procedure, an unknown failure mode, for the **whole lifetime** of the app.

If the divergence is accepted: write it into the charter ("accepted alternative" line, with date and app) and record the decision (see `plan-first`). An unwritten divergence becomes invisible debt.

## 4. Periodic check

Review the projects from time to time: how many distinct deployment targets, how many providers, how many paid accounts? Any layer where more than one tool is used without a written reason is a convergence project to schedule.

## Rules

- Standardize **deployment** before anything else: it is the layer you touch at every delivery.
- "I'm trying this new tool on this small project" is the normal mechanism by which debt accumulates. If the experiment is the goal, own it as a disposable sandbox.
- The charter is a floor, not a prison: it is revised deliberately, all at once, for all projects.
- End with the proposed next step or steps.
