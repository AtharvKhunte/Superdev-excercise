# NOTES

## Summary of changes
Fixed 8 bugs in 4 files. Details in `handwritten/`.
- `TaskController.java`: removed inverted `Thread.sleep` (empty search 1.02s, now 0.03s). Invalid `status` returns 400, not 500. `page`/`pageSize` validated (400), `pageSize` capped at 100, `start` computed as `long`.
- `TaskRepository.java`: fixed `AND`/`OR` precedence. Status filter was bypassed and archived rows leaked. Added `id DESC` tie-break for stable paging.
- `useTasks.js`: `ignore` flag stops stale responses overwriting newer ones. Loading ends on error, error clears on retry.
- `App.jsx`: 300ms search debounce. Page resets to 1 when query or status changes.

## What I chose not to change
- In-memory pagination: bigger than a patch.
- `status` as `String`, no enum or `CHECK`: needs a schema change.
- `%` and `_` in search act as wildcards: needs escaping in two places.
- `AbortController`: `ignore` flag was the smaller change.
- `db/oracle/` PL/SQL: not reviewed. May contain the same `AND`/`OR` bug.
- `StatusFilter.jsx`: not reviewed. Filter worked in manual testing.

## Biggest remaining risk
Pagination loads every matching row, then slices in Java. Will not scale. Should use `LIMIT/OFFSET` plus a `COUNT` query, with indexes on `status`, `archived`, `created_at`. H2 console is also enabled with no auth.

## Tools used
Used Claude to review code, find the bugs, and draft patches and explanations. I reproduced each bug before patching (curl, browser DevTools, run in GitHub Codespaces because my network blocked Maven), applied the fixes, and re-ran the same tests to confirm. Handwritten notes are in my own words.
