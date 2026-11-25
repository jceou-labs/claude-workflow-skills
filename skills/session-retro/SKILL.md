---
name: session-retro
description: Retrospective of a session: what cost time or tokens, corrections to make to skills and preferences, and frank feedback on how the session was steered.
---

# Session retrospective

A badly steered session that is not analyzed will repeat itself identically tomorrow. The retro turns a bad session into a permanent improvement of the setup.

Not to be confused with `session-handoff`: the handoff describes **the state of the work**, the retro describes **the quality of the process**. They complement each other at the end of a session. Also not to be confused with `bottleneck-log`: the retro covers **one** session, the log covers frictions that come back from one project to the next.

Deliver the retro **in the chat** (the user does not read working files).

## Procedure

### 1. Reconstruct the trajectory

Look for the moments of friction, not the steps that went well:

- Where did I go down the wrong path, and **what piece of context was missing** at that moment?
- How many times did the user have to correct the same thing? Any correction repeated twice is a flaw in the setup, not a lapse of attention.
- Where did I burn tokens for nothing? A file reread three times, blind exploration for lack of an entry point, a long output never used, an avoidable round trip.
- Was there a silent drift (work outside the scope)? What allowed it?

### 2. Convert into concrete corrections

Every finding must land in a specific artifact, otherwise it is lost:

| Type of finding | Destination |
|---|---|
| A lasting preference or rule of the user | Persistent memory (it is fed after the session; if the user explicitly asks to remember it, write it right away) |
| A procedure redone by hand more than twice | A new skill, or an existing skill to extend: propose the skill change to the user (the full updated file) |
| A rule that a command can check | A check that is always run before concluding; in a Claude Code project, a hook |
| A pitfall specific to one project | That project's notes or log (`learnings.md` if the project has a repository) |
| A friction that comes back from one project to the next | `bottleneck-log` |

### 3. Frank feedback on how it was steered

This section is expected and explicitly requested: tell the user what they could have done better, with no diplomatic cushioning.

- Request too vague at the start when the context really was missing: they should have given the key information in the first message.
- Session kept going too long when a break and a clean handoff would have been faster and cheaper.
- Wrong model for the task.
- Correction made too late when the drift was visible from the start.
- Useful context they had and did not share.

Direct wording, no "maybe possibly". One sentence per point, with what should have been done instead.

### 4. Periodic review

Every few weeks: reread the recorded pitfalls, group the recurrences, and **promote** what keeps coming back. A pitfall recorded three times must become a rule in a skill or an automatic check. Propose deleting what has become obsolete.

## Output format

```
## What was expensive
- <friction>: root cause: <missing context / missing instruction / wrong tool>

## Corrections proposed or applied
- <artifact>: <what>

## Steering - direct feedback
- <what should have been done differently>

## To watch next time
- <one or two points, not ten>
```

## Rules

- Root cause, never symptom. "I forgot to run the tests" is a symptom; the absence of a safeguard is the cause.
- Three findings well handled are worth more than fifteen listed.
- Complacency makes the retro useless: if the session went badly, say so.
- End with the proposed next step or steps.
