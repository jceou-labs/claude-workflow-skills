---
name: adversarial-review
description: Have independent reviewers attack a plan, a diff, an architecture or an infrastructure change, and keep only the problems that are demonstrated.
---

# Adversarial panel

An agent that rereads its own work approves it. The only review that finds anything is one by a reviewer who **does not have** the production context and whose mission is to tear it down, not to approve.

## Procedure

### 1. Isolate the artifact

The reviewer receives only the deliverable (file, diff, plan, change procedure) and the stated goal. **Never** the reasoning that led there: that reasoning is where the bias lives.

For code under git, produce the diff (READ-ONLY on the history, creates a temporary file). Prerequisite: open a terminal, then `cd <project_root>`:

```bash
git diff main...HEAD > /tmp/review-target.diff
```

For a plan or a procedure, pass the text or the file as is.

### 2. Run the panel: distinct angles, not clones

Identical reviewers find the same thing. Assign one angle per reviewer, in parallel (independent sub-agents, with no access to the conversation):

**For code:**

- **Correctness**: does this code do what it claims? Edge cases, null values, off-by-one, silently swallowed errors.
- **Breaking existing behavior**: which current behavior does this change alter without saying so? API contracts, data formats, side effects.
- **Adversary**: I am a malicious or clumsy user, how do I make this fall over? Unvalidated inputs, injection, concurrency, resource exhaustion.
- **Operations**: it breaks at 3 a.m., what do I see? Logs, observability, whether rollback is possible.
- **Simplicity**: what part can be removed without losing the result? Needless complexity is a defect.

**For a plan (not code):** replace the first two angles with **unverified assumptions** and **hidden costs / future lock-in**.

**For an infrastructure change (technical shutdown, cluster migration, network change, mass deployment):**

- **Rollback**: can we go back, how long does it take, with what loss? Is the rollback plan tested or only written?
- **Dependencies and order**: which systems, accounts, licenses, flows or teams does this change assume without saying so? What happens if a step is done out of order?
- **Field constraints**: bandwidth, intervention window, remote access lost midway, nobody on site.
- **User impact and communication**: who is affected, who must be warned, who signs off?

Adapt the number of angles to the stakes: 2 for a modest change, all of them for a migration.

### 3. Verify each finding before reporting it

A panel produces noise: findings that are plausible but wrong. Every finding passes a test before it lands in the report:

- **Is the failure scenario written down?** Concrete inputs leading to observed wrong behavior. Without that, it is not a finding, it is a hunch.
- **Does the incriminated element really exist as described?** Reread the cited lines or steps.
- A finding that cannot be reproduced or is already handled elsewhere: throw it away, do not "flag it to be safe".

### 4. Report, sorted by severity

```
BLOCKING   <file:line or step>: <defect in one sentence>
           Scenario: <inputs leading to wrong result>
IMPORTANT  ...
MINOR      ...
```

End with a clear verdict: **go ahead** / **fix X and Y first** / **the plan needs to be redone**. No vague summary.

If nothing serious is found, say so plainly: an empty report is a valid result and is better than padding with cosmetic remarks.

## Rules

- The reviewer rates the artifact, never its author.
- The threshold is *demonstrable*, not *possible*. "This could cause a problem" without a scenario is not reported.
- Do not fix things on the spot without agreement: review and repair are two separate steps.
- Much cheaper in tokens than an exhaustive exploration for a comparable result: this is the right default reflex before a risky change.
- Do not write sensitive internal data (network configuration, server names, vulnerabilities) in the report: use placeholders.
- End with the proposed next step or steps.
