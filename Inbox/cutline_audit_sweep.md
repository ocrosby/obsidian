# Cutline audit sweep — 2026-11-11

One session, eight PRs. Started from `/impeccable audit` reporting 17/20 Good with three detector findings; ended at 20/20 Excellent with zero detector findings. Both long-running test flakes in CI fixed at root cause along the way.

## PRs landed

| PR | Change |
|---|---|
| [#317](https://github.com/jedi-knights/cutline/pull/317) | ARIA combobox wiring on CommandPalette (`role="combobox"` + `aria-controls` + `aria-activedescendant` + `useId()` ids) |
| [#318](https://github.com/jedi-knights/cutline/pull/318) | Touch targets ≥44 CSS px on header controls below 720px |
| [#319](https://github.com/jedi-knights/cutline/pull/319) | Stat-card accent stripe removed (detector: `border-accent-on-rounded`) |
| [#321](https://github.com/jedi-knights/cutline/pull/321) | General Sans adopted as typographic voice; self-hosted 38 kB variable woff2 (detector: `overused-font`) |
| [#322](https://github.com/jedi-knights/cutline/pull/322) | Palette selection on `onClick` with explicit input re-focus, not `onMouseDown` + `preventDefault` |
| [#323](https://github.com/jedi-knights/cutline/pull/323) | Stale-coalesce test deterministic — stub repo now holds compute window open |
| [#324](https://github.com/jedi-knights/cutline/pull/324) | Cutoff-row left-edge stripe removed (detector: `side-tab`) |
| [#325](https://github.com/jedi-knights/cutline/pull/325) | Bootstrap tests lifted out of Linux ephemeral port range; `waitForListen` now surfaces `Run()`'s bind error |

## Content-first design — the lesson

Both detector-finding fixes (#319 stat-card, #324 cutoff-row) worked by **deleting chrome**, not by replacing it with a different visual pattern. The signal was already being carried by content:

- **Stat-card hero tier** — the hierarchy came from size (1.6rem vs 0.95rem), weight (700 vs 400), surface fill vs transparent background, and block padding. Four independent signals. The 3 px accent stripe on top of all that was reinforcement that happened to be the exact shape detectors flag as "generated-UI tell."
- **Standings cutoff row** — the row's own label ("Top N — conference tournament cutoff"), its double-rule divider, and its position between row N and row N+1 are three independent signals. The 3 px inset stripe on each qualifier's rank cell was redundant with the label below.

Rule of thumb for `/impeccable` polish work: **when a detector flags a stripe/accent/badge, first ask whether the content around it already carries the signal.** If yes, delete. If no, pick a mechanism that reads as content (glyph in a cell, inline label) rather than chrome (stripe, badge, border).

## Test flakes — both at root cause

Not test-logic bugs. Both were timing or environment assumptions that held in production but not in test:

- **`TestGetStandings_ConcurrentStaleHitsCoalesceToOneRefresh`** (#323) — `singleflight.Group.Do` only coalesces calls that arrive *while the first compute is in flight*. Production compute runs 20-45 s, so N concurrent arrivals always overlap. Stub repo's `ListFinal` returned in sub-ms, so later goroutines' `Do` calls landed after the first one returned and the group had forgotten the key. Fix: `listFinalDelay` field on the stub, set to 100 ms in the coalescing test, holds the window open long enough for the Go scheduler to run all N request-goroutines inside it.
- **`TestRun_MetricsListenerServesPromText` + 7 neighbors** (#325) — all eight bootstrap tests used hardcoded ports in 58900-58910 and 59900, squarely inside Linux's default ephemeral range (`net.ipv4.ip_local_port_range` = 32768-60999). Outbound TCP from the GH Actions runner itself (gh CLI, apt, telemetry) could grab one as source port, causing `Run()`'s bind to fail. The goroutine sent the bind error into `done`, but `waitForListen` never checked `done` — it polled for the full 2 s and reported `did not accept connections`, hiding the cause. Fix: lift ports to 61xxx / 62xxx range above the ephemeral ceiling; thread `done` into `waitForListen` so a bind error surfaces at first failure instead of silently timing out.

The waitForListen diagnostic is the generalizable half — any future flake in these tests becomes diagnosable at first failure.

## Session arithmetic

- Audit: 17/20 → 20/20 (a11y 3→4, responsive 3→4, integrity 3→4)
- Detector findings: 3 → 0
- Known CI flakes: 2 → 0
- Font bytes on first paint: ~218 kB (Inter subsets) → 38 kB (General Sans variable)
