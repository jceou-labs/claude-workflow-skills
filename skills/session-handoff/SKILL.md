---
name: session-handoff
description: Close a work session cleanly (verified summary, unpushed work, remaining tasks, handoff file) or pick up a previous session where it stopped. Use AS SOON AS the user says "let's wrap up", "end of session", "handoff", "I'll be back later", "summarize what we did", "can I archive this chat", or conversely "pick up where we left off", "catch yourself up", "pickup". Also use on your own initiative when the conversation gets long or the context is about to be compacted.
---

# Session handoff (close and pick up)

A session that stops without a written trace is a lost session. The context gets compacted, the conversation gets archived, and the next session starts from zero. This skill covers both ends: **CLOSE** (write the state to disk) and **PICKUP** (read it back).

The reference file is `HANDOFF.md` at the project root (or `docs/HANDOFF.md` if the project has a docs folder). One file, overwritten at each close. The history is in git, not in a pile of dated files.

---

## CLOSE mode

### 1. Verify before summarizing (non-negotiable)

Never write the summary from the memory of the conversation: it over-reports the work done. Every claim must be backed by evidence on disk.

```bash
git status --porcelain              # uncommitted changes
git log --oneline @{u}..HEAD        # unpushed commits (if upstream)
git stash list                      # work set aside and forgotten
git branch --show-current
```

Then, for everything you believe you delivered: check that the code really exists (grep the symbol), that the tests pass (run them), that the docs followed. Run the command that proves it before calling it done. Any claim you cannot verify becomes "attempted, not confirmed", never "done".

### 2. Write `HANDOFF.md`

```markdown
# Handoff: <project> | <ISO date> <time>

## State
Branch: <name> · <n> uncommitted modified files · <n> unpushed commits

## Done (verified)
- <action> → evidence: <file:line | passing test | commit sha>

## Attempted, not confirmed
- <what was touched without validation>

## To do (in priority order)
1. <task> (entry point: <file/function>)
2. ...

## Pitfalls encountered
- <what wasted time, so as not to fall into it again>

## Resume prompt (paste as is into the next session)
> <2-4 sentences: goal, where things stand, first action to take>
```

### 3. Archive verdict

End with an explicit line, in plain words, in the reply:

- **Safe to archive**: nothing pending, everything is pushed or recorded.
- **Do not archive**: and say precisely what blocks it (uncommitted work, failing test, decision waiting on the user).

### 4. Record decisions

If structural choices were made during the session, they do not go in `HANDOFF.md` (which is ephemeral) but in the permanent decision log. See the `plan-first` skill.

---

## PICKUP mode

1. Read `HANDOFF.md`, then the decision log (`decisions.md` / `docs/adr/`), then the current plan if there is one.
2. **Check what is written against reality**: the repo may have moved since. `git log --oneline -10`, `git status`. If `HANDOFF.md` describes a state that no longer matches, say so before doing anything.
3. Report back in 5 lines at most: where things stand, what you are about to do, what is still unclear.
4. Do not redo work already recorded as done: verify it first.

---

## Rules

- A handoff file that is never read again is useless. If pickup is not automatic in the environment, enforce it with a line in the project's CLAUDE.md ("at startup, read HANDOFF.md"). Use a session-start hook only in Claude Code and only if the user asks for it.
- Write for a reader with no memory: no "as discussed", no pronouns without a referent, full file paths.
- Target length: one page. Beyond that, it is a plan, not a handoff.
