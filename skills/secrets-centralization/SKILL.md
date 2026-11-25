---
name: secrets-centralization
description: Centralize the API keys and environment variables of a portfolio of apps into a single partitioned source (inventory without displaying secrets, rotation).
---

# Centralizing secrets and configuration

Scattered secrets cause three problems: the same key copied into five `.env` files (so practically impossible to rotate), values that silently diverge between local and production, and the secret forgotten in a repository. Centralizing solves that, but creates a single target. The trade-off has to be managed, not ignored.

## 1. Inventory first (never displaying a value)

You do not centralize what you have not listed. All the commands below are **READ-ONLY (information gathering)** and only print file names or commit identifiers, never the content of a secret.

Prerequisite: open a Git Bash terminal (or PowerShell with Git installed), then go to the project root:

```bash
cd <project_root>
```

Local configuration files:

```bash
find . -name '.env*' -not -path '*/node_modules/*' -not -name '.env.example'
```

`.env` files that have already been in the git history (names only, no content):

```bash
git log --all --name-only --diff-filter=A --pretty=format: | grep -E '(^|/)\.env' | sort -u
```

Commits that added or removed a string that looks like a key (commit identifiers only, NO `-p` option):

```bash
git log --all --oneline -S'sk-'
```

Code files containing a secret pattern (`-l` option: file names only, never the line):

```bash
grep -rlE '(api[_-]?key|secret|token|password)[[:space:]]*[:=][[:space:]]*["'"'"'][A-Za-z0-9_-]{16,}' \
  --include='*.js' --include='*.ts' --include='*.py' --include='*.go' --include='*.rb' \
  --include='*.php' --include='*.yml' --include='*.yaml' --include='*.json' \
  --exclude-dir=node_modules --exclude-dir=.git --exclude-dir=venv .
```

For a more reliable scan, a dedicated tool such as gitleaks or trufflehog is preferable; run them with the option that masks values (`--redact`).

Produce a table: **secret (name only) > what it is used for > which apps use it > where it lives today > is it in a git history**.

Any secret found in a git history is **compromised**, period: removing it from the repository is not enough, it must be **revoked and replaced**. This line comes before everything else.

## 2. Choose the single source

Depending on the deployment target (see `stack-discipline`: the secret store should be the one of the platform already chosen, not one more provider):

| Option | When |
|---|---|
| Secret store built into the hosting platform | Default: nothing to administer, already partitioned by app and by environment |
| Dedicated vault, self-hosted or managed | Many apps, need for audit, automatic rotation, several people |
| Encrypted versioned file (key-based encryption) | Solo, few apps; the history is in git, decryption is local |

Avoid: a personal password manager as the source for deployments (no machine access), and manual copy-paste as a method.

## 3. Partition: this is where the trade-off is decided

Centralizing does not mean putting everything in the same basket:

- **One space per app**, and **one per environment** (dev / staging / prod). An app must not be able to read another app's secrets.
- **No production secret on the development machine.** Development uses test keys, even if some features are degraded locally.
- **Minimal scope at creation**: one key per app and per use, with the narrowest possible rights, rather than a shared admin key. This is what makes a leak repairable instead of catastrophic.
- **Write access reserved to a human**, read-only for deployments.

## 4. Set the rule on the code side

- The code reads its configuration **at startup, in a single place** (a config module), and fails immediately and loudly if a required variable is missing. Nothing is worse than a silent fallback to a default value: the behavior changes without an error.
- No secret in the code, in a URL, in a log or in an error message.
- `.env` in `.gitignore`, `.env.example` versioned with **the names without the values**: it is the documentation of the expected variables.
- Reusable bricks **receive** their configuration, they do not read it (see `reusable-bricks`).

## 5. Rotation

A secret that is centralized but can never be rotated is only half centralized. Check that replacing a key is possible **without downtime**: two keys valid in parallel during the switchover. Test it once, in a calm moment, before you need it in an emergency.

## Rules

- A key copied into two apps is two keys to revoke on the day of the incident: prefer one key per app.
- Never display the value of a secret in a conversation, a report or a screenshot, not even to "check". Every inventory command must be chosen to output only file names or identifiers.
- Any revocation or rotation is done by the user themselves on the provider's console: prepare the procedure, do not handle the credentials for them.
- For each command provided, say whether it is READ-ONLY or CHANGES the system, and give the starting folder.
- End with the proposed next step or steps.
