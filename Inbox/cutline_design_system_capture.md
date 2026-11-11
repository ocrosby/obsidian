# Cutline design-system capture — 2026-11-11

Continuation of the earlier [[cutline_audit_sweep]] session. The audit-sweep arc was *find and fix*; this arc was *write down what's actually there*. Two PRs landed, both docs.

## PRs landed

| PR | Change |
|---|---|
| [#326](https://github.com/jedi-knights/cutline/pull/326) | `PRODUCT.md` at the repo root (93 lines) — product truth from `/impeccable init` interview |
| [#327](https://github.com/jedi-knights/cutline/pull/327) | `DESIGN.md` + `.impeccable/design.json` sidecar (621 lines total) — visual system from `/impeccable document` in Scan mode |

## The audience correction worth remembering

Mid-interview I framed the primary user as "college soccer superfans" and got corrected: **plural sports, not soccer-specific**. The codebase (multi-sport plugin architecture, scoreboard/standings UI for every sport) made this obvious in retrospect, but the day-one seed data (`seed/d1-wsoc.yaml`) biases the language toward soccer. Saved to [[project memory]] so future sessions don't repeat the mistake.

**Rule of thumb:** when a product has seed data narrower than its architecture, the audience is sized by the architecture, not the seed. Day-one scope ≠ audience scope.

## The twelve named rules

The most citable artefact from DESIGN.md — grouped by section:

- **Colors:** The One Band Rule · The Theme-Stable Pair · The Gold-is-Earned Rule
- **Typography:** The Tabular Rule · The 16px Floor · The Weight-and-Size Rule
- **Layout:** The One Column Rule · The 720px Reshape
- **Elevation:** The Flat-By-Default Rule · The Tonal-Step Rule
- **Shapes:** The 4px Default Rule · The Hairline Rule

Each one is a short declarative sentence tied to a specific invariant in the code. Audits can cite them by name; AI-generated surfaces have a vocabulary that matches the implementation.

## The capture itself is the win

Running `/impeccable audit` *before* writing PRODUCT + DESIGN gave 20/20 Excellent against inferred intent. Running it *after* gave the same 20/20 — but against the brief this time, and now I could enumerate "12/12 named rules verified by the implementation." Same number, different authority.

**Rule of thumb for design-system work:** a brief that describes code as-is is worth writing even when nothing changes. It converts "the code happens to work" into "the code honors a written contract." The next time someone proposes a stripe on a hero card, "The Gold-is-Earned Rule says no" is a cleaner refusal than "it looks like generated UI."

## North Star + brand color names (confirmed via interview)

- **Creative North Star:** *The Sports Almanac* — reference-work feel, dense tabular data, methodology over flourish.
- **Varsity Navy** (`#18264a`) — theme-stable, masthead only. The One Band Rule.
- **Pennant Gold** (`#f5b700`) — theme-stable, reserved for masthead's 3px bottom border and the tournament-cutoff indicator family. The Gold-is-Earned Rule.
- **Elevation philosophy:** flat by default, lifted only for overlays (the ⌘K palette is the single `box-shadow` in the system).
- **Component philosophy:** precise and restrained — 4px default radius, 1px borders, tabular numerals, no decorative gradients.

## Session arithmetic

- Audit score: held at 20/20 Excellent (+ gained rule-compliance score: 12/12)
- Lines of durable context captured: 452 (PRODUCT.md 93 + DESIGN.md 359)
- Lines of machine-readable sidecar: 262 (.impeccable/design.json)
- Interview rounds with the user: 2 rounds of structured questions, both accepted recommendations
- Detector findings: still 0

## What's next for cutline

Next natural beat is **not** another `/impeccable` command. The system is captured; the audit is empty. Natural next steps when the user wants them:

- `/impeccable critique` on a specific surface if a page feels off — grades heuristically against both briefs
- `/impeccable shape <surface>` when designing a new page — now has both briefs to work from
- Promote this note and [[cutline_audit_sweep]] out of Inbox once the content-first design pattern graduates to a durable [[Resources]] entry
