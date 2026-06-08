# UPSTREAM-MERGE-DISCIPLINE.md

**Canonical discipline document for any UniCORE repository that forks an upstream OSS codebase and layers CC BY 4.0 additions on top.**

Author: Bryan Fred, Unitek Systems Limited, Bedford, United Kingdom.
Locked: 2026-06-04 08:11 UTC.
Applies to: all UniCORE repositories that fork an active upstream OSS codebase — including UniCORE.Asterisk, UniCORE.Avalonia, UniCORE.DNN, UniCORE.Jitsi, UniCORE.Signal, UniCORE.XCP, and their UniSaaS sisters and Claw working repos.

---

## Why this document exists

UniCORE consumes upstream open-source codebases (Avalonia, DNN, and others to come) as foundation layers, then adds UniCORE-authored work on top under CC BY 4.0. The upstream codebases keep evolving — releases, bug fixes, security patches, feature additions. We have to absorb those upstream changes without losing our additions, without breaking our test fence, and without contaminating the licence boundary.

Doing this casually leads to:
- Lost upstream improvements (we drift from upstream and miss security patches)
- Lost UniCORE additions (overaggressive upstream merge clobbers our code)
- Licence contamination (upstream changes get attributed to UniCORE, or vice versa)
- Reviewer confusion (no clean "what is upstream / what is ours" boundary)

This document locks the discipline so the same answer applies in every forked repo.

---

## The pattern

```
upstream/main  ←  read-only mirror of the original OSS upstream
     |
     v
origin/main    ←  tracks upstream + minimal merge-conflict resolutions
     |
     v
origin/unicore ←  carries ALL UniCORE-authored CC BY 4.0 additions
                  (this is the working branch; CI runs against this)
```

**Three rules from which everything else follows:**

1. **`main` is the upstream mirror.** It contains upstream code, plus minimum-necessary merge-conflict resolutions. Nothing UniCORE-authored ever lands in `main` directly.
2. **`unicore` carries our work.** Every UniCORE-authored file, directory, project, package, control, theme, test, document — all of it lives on the `unicore` branch.
3. **Directory discipline keeps the boundary clean.** Our additions live in **clearly-separated top-level sibling directories** that upstream rarely touches. Conflicts in our directories become near-zero.

---

## Mechanics

### Remotes

Every forked repo has TWO remotes:

```bash
# Upstream — the original OSS repo. Read-only.
git remote add upstream https://github.com/<UPSTREAM_ORG>/<UPSTREAM_REPO>.git

# Origin — our fork on bryanunitek/. Read-write.
# (Cloned with this already in place; nothing to do.)
```

Verify:

```bash
git remote -v
# Expected:
# origin    git@github.com:bryanunitek/<OUR_FORK>.git (fetch)
# origin    git@github.com:bryanunitek/<OUR_FORK>.git (push)
# upstream  https://github.com/<UPSTREAM_ORG>/<UPSTREAM_REPO>.git (fetch)
# upstream  https://github.com/<UPSTREAM_ORG>/<UPSTREAM_REPO>.git (push)
```

### Branches

```bash
git checkout main           # tracks upstream + minimal merge resolutions
git checkout unicore        # carries all UniCORE-authored CC BY 4.0 additions
```

**Never** create feature branches off `unicore` in this kind of repo. The `unicore` branch is the single line of work; pushes go straight to `origin/unicore`.

### Merge cadence

| Trigger | Action |
|---|---|
| **Per upstream release** (preferred) | Fetch + merge on release tag boundary. Clean release-to-release deltas. |
| **Weekly** (fallback) | Friday afternoon UTC sweep if no release has shipped that week. Keeps drift bounded. |
| **Security patch** (immediate) | Drop everything and merge the patch as soon as the upstream CVE notification arrives. |

### Merge sequence

```bash
# 1. Fetch upstream
git fetch upstream

# 2. Merge upstream into our main
git checkout main
git merge upstream/main           # resolve any conflicts in upstream files only
git push origin main

# 3. Propagate to our additions branch
git checkout unicore
git merge main                    # may surface conflicts if upstream touched our sibling dirs (rare)

# 4. Run the full test fence
# (project-specific — see CONTRIBUTING.md or per-repo README)
dotnet test  # or equivalent

# 5. If green, push
git push origin unicore

# 6. If red, do NOT push. Open an issue on our tracker, resolve, then push.
```

### Conflict-resolution playbook

| Situation | Action |
|---|---|
| Upstream changed a file we have NOT modified | Take upstream verbatim. No decision needed. |
| Upstream changed a file we HAVE modified (rare — only if we patched upstream code) | Decide per-case. Default: keep our patch unless upstream supersedes the reason for the patch. Document the call in commit message. |
| Upstream added a NEW file in a directory where we have additions | Take upstream's file as-is; ensure no name collision with our additions. |
| Upstream renamed a file we depend on | Update our additions to reference the new path. Commit the path-update separately from the merge commit. |
| Upstream removed a file we depend on | Stop. Surface to Bryan. Do not merge until we have a substitution plan. |

### Directory discipline

**Our additions live in dedicated top-level sibling directories.** Examples:

For Avalonia:
- `src/Avalonia.*` — upstream Avalonia projects (untouched by us)
- `src/UniCORE.Avalonia.Controls/` — our control library (Pro-equivalents)
- `src/UniCORE.Avalonia.Charts/` — our charts library
- `src/UniCORE.Avalonia.MediaPlayer/` — our media player
- `src/UniCORE.Avalonia.Markdown/` — our markdown viewer
- `src/UniCORE.Avalonia.RichTextEditor/` — our rich text editor
- `src/UniCORE.Avalonia.VirtualKeyboard/` — our virtual keyboard
- `tests/UniCORE.Avalonia.*.Tests/` — our test projects
- `docs/unicore/` — our documentation
- `UPSTREAM-MERGE-DISCIPLINE.md` — this file, at repo root

For DNN: same pattern, sibling to upstream `DNN Platform/` directory.

**Why this matters:** when upstream rarely touches our sibling directories, merge conflicts in our work become rare. The 90% case is "merge upstream/main into main; merge main into unicore; both clean."

---

## Licence attribution

### File headers

Every **upstream file** keeps its original MIT (or other upstream OSS) header **untouched**. Do not modify upstream-authored copyright lines.

Every **UniCORE-authored file** carries this header:

```
// Copyright (c) Bryan Fred, Unitek Systems Limited. All rights reserved.
// Given, not sold. Irrevocable.
// Licensed under the Creative Commons Attribution 4.0 International License (CC BY 4.0).
// See LICENSE.md at the root of this repository for the full licence text.
```

### Repository-level licence

The repository root contains:

- `LICENSE.md` — UniCORE additions under CC BY 4.0
- `LICENSE.upstream.md` — exact copy of the upstream OSS licence (MIT for Avalonia / DNN), preserving original copyright text

Both files MUST exist and must reference each other in their text. Anyone reading either file should understand that the repo is dual-licensed: upstream layer under upstream terms, UniCORE additions under CC BY 4.0.

### Distinguishing upstream from UniCORE in directory listings

A casual reader inspecting the repo should be able to tell what is upstream and what is UniCORE within 30 seconds:

- `UniCORE.*` prefix on every UniCORE-authored project / directory.
- `docs/unicore/` directory for UniCORE-authored documentation.
- Root-level `README.md` explicitly names which directories are upstream and which are UniCORE.

---

## Test fence

Before any push to `origin/unicore`, the following must be green:

1. **Upstream test suite** — runs as part of the merge into `main`. If upstream's own tests are red, hold the merge.
2. **UniCORE test suite** — runs as part of the merge into `unicore`. Every UniCORE-authored project has tests; tests live in `tests/UniCORE.<Project>.Tests/`.
3. **Cross-project integration tests** — verify UniCORE additions still compose against upstream after merge.

If any tier is red, push is blocked. Open an issue, resolve, re-run, then push.

---

## Roll-back procedure

If a merged upstream introduces a regression that escaped the test fence:

```bash
# Roll back the unicore branch
git checkout unicore
git revert <merge-commit>          # creates an explicit revert commit
git push origin unicore

# Investigate. Either patch the regression or hold the next upstream merge until upstream patches it.
```

Never `git reset --hard` and force-push the unicore branch — that's history rewriting on a shared branch.

---

## What this discipline does NOT cover

- **Greenfield UniCORE repos** (no upstream fork). Those follow the normal one-branch-`main` discipline; this document does not apply.
- **NuGet-consumed dependencies** (DevExpress Blazor, Microsoft.Maui). Those are NOT forked; we depend on their packages. No merge discipline needed.
- **Commercial-licence upstreams** (Mandeeps). Those cannot be republished under CC BY 4.0 at all; the discipline question is moot until/unless an upstream-licence path opens up.

---

## When to revise this document

Revise if:
- A new fork target surfaces a workflow gap not covered here.
- A merge incident reveals a discipline rule we should have had.
- Upstream changes its licence (rare but possible).

Revisions: edit + commit + push. Single-source-of-truth document; the same content should appear at the root of every forked repo so anyone working there sees the same rules.

---

## Cross-references

- Per-repo `README.md` — names the upstream + summarises the discipline application.
- Per-repo `LICENSE.md` and `LICENSE.upstream.md` — dual-licence wording.
- Workspace memory (`memory/2026-06-04.md` 08:11 UTC) — origin of this canonical doc.
- `MEMORY.md` programme-level anchor (to be added in the same arc as this file's first publication).

---

*Authored under the Singular Pairing Principle: Bryan Fred + main Claw. Given, not sold. Irrevocable.*
