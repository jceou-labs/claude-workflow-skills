---
name: repo-sweep
description: Sweep a repository for unfinished work (forgotten TODOs, planned functions never implemented, dead code, disabled tests, docs describing code that no longer exists), then sort it into a usable backlog. Use AS SOON AS the user says "what's been left lying around", "go through the repo", "what's not finished", "tech debt", "did we forget things", "clean up", "tidy up the project", or after several successive agent sessions on the same project.
---

# Sweep for dangling threads

Several agent sessions in a row leave a trail: `TODO`s put down then forgotten, plan steps ticked off mentally but never done, tests set to `skip` "temporarily", docs describing a deleted function. None of this breaks the tests, which is exactly why it piles up.

## Procedure

### 1. Mechanical harvest

```bash
# Explicit markers
grep -rn --include='*.*' -E '\b(TODO|FIXME|HACK|XXX|WIP)\b' . | grep -v node_modules

# Neutralized tests
grep -rn -E '\b(skip|xit|xdescribe|@pytest.mark.skip|it\.only|describe\.only)\b' .

# Plans with boxes still to tick
grep -rn '^\s*- \[ \]' *.md docs/ 2>/dev/null

# Work set aside and forgotten
git stash list
git branch -a --sort=-committerdate | head -20   # abandoned branches
```

### 2. Cross-checking harvest (where most of it hides)

The mechanical pass only finds what was declared. The real unfinished work is silent:

- **Plan vs reality**: take `PLAN.md` / the ADRs / the latest `HANDOFF.md`, and check that every item announced as done really exists in the code. A gap is a dangling thread.
- **Docs vs code**: does every symbol cited in the README or the API docs still exist? (`grep` the name.)
- **Orphan code**: exported functions/modules that nobody imports. A half-wired implementation is more dangerous than an absence.
- **Empty error paths**: `except: pass`, `catch {}`, `null` returns not handled upstream.
- **Phantom configuration**: config keys read nowhere, or read under an old name (silent fallback: changes the behavior without raising an error).

### 3. Sort: this is the step that has value

A list of 200 `TODO`s is useless to everyone. Classify:

- **Do now**: real breakage or risk (silent error path, disabled test that covered a critical case, phantom config). Quantify the impact.
- **Put in the backlog**: useful, not urgent. Create an entry with a title, file:line, and *why* it is worth doing.
- **Delete**: dead code, outdated TODO, abandoned branch. Propose the deletion, do not carry it out without agreement.
- **Ignore**: accepted noise. Say so to close the subject.

### 4. Make the result persistent

Otherwise the sweep will have to be redone identically in two weeks. Depending on the project's tooling: GitHub issues (`gh issue create`), or failing that a versioned `BACKLOG.md`. Each entry: actionable title, location, reason, severity.

## Output format

```
## Do now (n)
- <file:line> - <problem> · impact: <concrete>

## Backlog (n)  → recorded in <BACKLOG.md | issues #x-#y>
- <title> - <file:line>

## Delete (n)
- <item> - dead since <commit/date>

## Deliberately ignored (n)
```

## Rules

- Do not delete or fix anything during the sweep: take inventory first, act afterwards. A sweep that modifies the code produces a diff nobody reviews.
- A `TODO` with no author, date or context is almost always better deleted than done.
- Do not inflate the list: a credible sweep is a sorted sweep.
