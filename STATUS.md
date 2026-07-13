# STATUS — UniSaaS.UniCORE.LibreOffice

**Repository state as of 2026-06-04 17:45 UTC.**

---

## Scaffold-anchor only

This repository exists as a **scaffold anchor**. Its purpose at this moment is to:

1. Reserve the canonical name on `bryanunitek/`.
2. Lock the licence position (MPL-2.0 upstream; CC BY 4.0 for UniCORE additions).
3. Document the family pattern and upstream relationship in [`README.md`](https://git.unitek-systems.com/UniCORE/UniSaaS.UniCORE.LibreOffice/src/branch/main/README.md) (mirror: [GitHub](https://github.com/bryanunitek/UniSaaS.UniCORE.LibreOffice/blob/main/README.md)).
4. Document the merge discipline in [`UPSTREAM-MERGE-DISCIPLINE.md`](https://git.unitek-systems.com/UniCORE/UniSaaS.UniCORE.LibreOffice/src/branch/main/UPSTREAM-MERGE-DISCIPLINE.md) (mirror: [GitHub](https://github.com/bryanunitek/UniSaaS.UniCORE.LibreOffice/blob/main/UPSTREAM-MERGE-DISCIPLINE.md)).
5. Enrol the repository in the **UniCORE Sanity Check fleet** so it cannot be forgotten.

Nothing else is in this initial scaffold commit. Specifically:

- **Upstream codebase NOT YET present** on this repository — fork pending. The repository is empty (size=0) at scaffold time.
- **No UniCORE additions yet.** No source code modifications, no custom modules.
- **No CI configuration.**

---

## What is queued

1. Pull upstream history into the `main` branch (deferred until disk + dedicated kickoff arc).
2. Author UniCORE-specific additions on the `unicore` branch.
3. Apply the canonical upstream-merge discipline (weekly or per-release cadence).
4. Test fence before every push to `unicore`.

---

## What this initial scaffold DOES guarantee

- The repository is picked up by the next **UniCORE Sanity Check** automatically.
- The licence boundary is documented before any upstream code lands.
- The merge discipline is canonical and reusable.
- The four-repo family pattern is locked.

---

*Status owned by: main Claw. Reviewed by: Bryan Fred.*
