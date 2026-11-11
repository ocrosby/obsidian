# Content-first design

When a decorative element (a stripe, badge, border-accent, kicker, icon chip, hover-glow, shadow) is doing work that *content* could do — the label, the heading weight, the surface fill, the position, the size — the chrome is redundant. Delete it. The content was already carrying the signal; the chrome was reinforcement.

This is the single most useful heuristic I've found for cleaning up over-styled UIs. It's not about minimalism as an aesthetic; it's about recognizing which carrier the message is actually riding on.

## The test

For any decorative element, ask three questions in order. If you can answer "yes" to all three, delete the chrome:

1. **If I remove this element, what signal is lost?**
2. **Is that signal already carried by something else on the page — a label, a weight shift, a surface fill, a position, a size, a divider?**
3. **If someone saw this page for the first time, would they understand the signal from the content alone?**

If every answer is yes, the chrome is tautological. Delete it. The content was always doing the work.

The failure mode the test prevents: treating "visible" as "effective." A 3px accent stripe on a hero card is visible. That doesn't mean it's communicating anything a reader isn't already getting from the 1.6rem bold number and the filled surface sitting on a page of transparent sibling cards.

## Why chrome accretes

Chrome accretes because *drawing* feels like *designing*. Adding a stripe takes thirty seconds and produces a visible artifact; interrogating whether the artifact is load-bearing takes longer and produces nothing visible. The incentives favor adding.

Generated UI (AI-produced designs, template-driven dashboards, agency pitches) suffers the same trap on a larger scale. A pattern detector running over the typical AI-generated UI finds the same tells every time: the side-tab accent border, the border-top on a rounded card, the icon-in-colored-circle, the gradient CTA with a glow. These are shapes that look *like* design because they appear in designed artifacts, not shapes that carry a specific signal for a specific product.

## Case studies (from cutline, 2026-11-11)

Two near-identical fixes in one audit session — see [[cutline_audit_sweep]] for full context. Both worked by removing chrome because content was already carrying the message.

### The stat-card hero accent

Hero stat cards on the Team page had `border-top: 3px solid var(--accent)` on a 6px-radius card — exactly the shape the detector flags as `border-accent-on-rounded`. The question: what signal does the stripe carry? Answer: "these are the hero stats."

What else carries that signal?

- **Column width.** Hero cards use `grid-template-columns: repeat(auto-fit, minmax(10rem, 1fr))`; grid cards use `minmax(8rem, 1fr)`. Hero cards are visibly wider.
- **Value size and weight.** Hero values are `1.6rem / 700`; grid values are `0.95rem / 400`. Big and bold.
- **Surface fill.** Hero cards inherit `--surface`; grid cards drop to `transparent`. Hero cards *look* like cards; grid cards look like aligned text.
- **Block padding.** Hero cards get `0.75rem` block padding; grid cards get `0.4rem`.

Four independent signals of "these are the hero stats." Deleting the stripe loses *none* of them. Fix: delete the rule. One line gone, no behavior lost.

### The cutoff-row qualifier stripe

Standings tables inserted a full-width cutoff row between row N (last qualifier) and row N+1 (first out). *Every qualifier row above it* also got a 3px gold `inset box-shadow` on its first cell — classic `side-tab` shape. The question: what signal does the stripe carry? Answer: "these are the teams in the field."

What else carries that signal?

- **The cutoff row's label.** Reads: "Top N — conference tournament cutoff" — explicit text naming exactly the field.
- **The cutoff row's double-rule divider.** 2px solid + 1px dashed in Pennant Gold, visually unambiguous.
- **Row position.** Everything above the cutoff row is in; everything below is out. Spatial arrangement is the signal.

Three independent signals. Deleting the stripe loses *none* of them. Fix: delete the rule, and rewrite the adjacent CSS comment so the rationale ("the label + divider + position carry the signal") is in the code, not just in the deleted stripe.

## When content is NOT already carrying the signal

The test cuts both ways. If content *isn't* carrying the signal — if removing the chrome leaves a reader with no way to understand what they're looking at — the chrome is load-bearing. Keep it, and consider whether it should be *promoted* to content (a glyph, a label, a leading cell) rather than left as pure styling.

Signals that **are** chrome and should stay as chrome: focus outlines, hover states, loading spinners, the pulsing live-badge animation. These are feedback for a specific interaction moment; they can't become content because they're about transient state, not standing information.

Signals that **look like** chrome but should be promoted to content: group membership ("which teams are in the field"), status ("this item is archived"), semantic role ("this is a required field"). When these ship as pure color-or-stroke chrome, screen readers miss them, print loses them, high-contrast mode erases them. Promote them to content: a `★` glyph, an "archived" label, an asterisk.

## Rule of thumb

> **Decoration is the enemy; methodology is the point.** — paraphrasing cutline's `DESIGN.md` north star

The brief line from a design system that embraces this: *The Gold-is-Earned Rule.* Reserve the accent color for the specific earned jobs, don't sprinkle it. Every rule of this shape — one color, one weight, one stroke, one job — enforces content-first design by closing off a path toward chrome accretion.

## Related

- [[cutline_audit_sweep]] — the full session this surfaced in
- [[cutline_design_system_capture]] — where the lesson got codified into named rules
