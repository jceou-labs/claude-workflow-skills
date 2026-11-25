---
name: bottleneck-log
description: Record the technical frictions that repeat from one project to the next, and eliminate them with a permanent fix from the 2nd occurrence.
---

# Recurring bottleneck log

A friction that cost an evening on one app **will come back** on the next one. That is not bad luck, it is a hole in the starting setup. Without a log, you rediscover it every time and end up as a system administrator instead of making progress.

Not to be confused with `session-retro`, which analyzes how **one** session went. Here you track the frictions that **cut across projects**, and the fix is a reusable artifact, not a guideline.

## The log

A single file, outside the app repositories: `BOTTLENECKS.md`. Do not assume it exists: look for it in the connected files and in memory. If it does not exist, create it at the first occurrence in a doc or notes folder chosen by the user, and tell them where it is.

```markdown
# Recurring bottlenecks

## <Short title of the symptom>
- Occurrences: <app A (date)>, <app B (date)>
- Observed symptom: <what you see, not the assumed cause>
- Root cause: <why it happens structurally>
- Detection: <the command or alert that would have seen it coming>
- Permanent fix: <reusable artifact + where it lives>
- Status: to do | fix in place | built into the project template
```

Write the entry **while it is burning**, not afterwards. Once the problem is solved, you forget the detail that matters.

## Procedure

### At the 1st occurrence
Fix it, and **open the entry** in the log. Nothing more: do not build automation for a single incident.

### At the 2nd occurrence
This is the trigger threshold. Do not fix it by hand a second time without producing a permanent fix. The fix must be:

- **Reusable as is** on the next app (script, scheduled task, maintenance service, configuration rule).
- **Autonomous**: it runs without anyone having to think about it, and it raises an alert when it fails.
- **Stored** next to the reusable bricks (see `reusable-bricks`), not in the repository of the app where the problem appeared.

Typical example: disk full on the dev servers because the temporary files of the processing jobs are never purged. This is not an app problem, it is a missing maintenance service. Fix: a scheduled cleanup task, added by default to every new project.

### At the 3rd occurrence
The fix was not applied, or it is not enough. Do not add a patch: the fix **moves up into the project template** (the skeleton used to start any new app includes it by default). This is the only lasting way out.

## The priority fix: detection

A bottleneck that shows up as an outage costs ten times more than one that shows up as an alert. For each entry, write the check command **before** the fix. Example, READ-ONLY (information gathering), to run in a Linux terminal or Git Bash, independent of the current folder:

```bash
df -h | awk '$5+0 > 80 {print "ALERTE disque: " $6 " a " $5}'
```

Then schedule it. Half the value of the log is here.

## Rules

- Record the **observed symptom**, not the assumed cause: the cause is often corrected afterwards, the symptom is what you will recognize next time.
- A fix that requires remembering to run it is not a fix.
- Reread the log at the start of every new app: five minutes that avoid replaying the same evenings.
- Do not turn the log into a journal of every bug: it only holds what **repeats from one project to the next** and has nothing to do with the business domain.
- For any command you provide: say whether it is READ-ONLY or CHANGES the system, and give the starting folder. End with the proposed next step or steps.
