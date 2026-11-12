# ANEW-0013 — UI work is drawn in ANALYZE and approved with the spec

- Status: Accepted
- Date: 2026-11-12

## Context
ANEW-0008 put user experience into the spec as text: flow, states, a design link. In practice the
link is often "none — the flow above is the design", so what a screen looks like stays open until
PLAN or BUILD, and VERIFY compares its screenshots with a written flow rather than an approved
picture. ANEW-0008 named the signal to reopen it: "teams need design approval as a separate human
gate before ANALYZE ends". Internal teams adopting ANEW asked for exactly that.

## Decision
When a spec's "User experience" is anything but "none — no user interface", the Analyst produces a
**design artifact** in ANALYZE: versioned, opened by address, drawing every screen the acceptance
criteria touch in every state the spec lists, each drawing labelled with its criterion (`AC-3`).
An existing approved design is linked instead, never redrawn. The spec names it in one field,
`- Design artifact: <http(s) address> (version <id>)` (a mini-spec's `- Design:` uses the same
form). Spec approval is the approval of the spec **and** that artifact version together; a change
to the artifact after approval is a spec change (R-08), and the approved version must stay
openable — where the tool cannot open an older version, the approved one is not republished.
VERIFY compares each UI screenshot with the drawing that carries the same label.
`scripts/doctor` checks the field on Approved / In progress specs (gate: WARN, FAIL under
`--strict`). The core names no tool (ANEW-0011); each adapter says how its tool produces and
shares the artifact.

## Consequences
- Benefits: the look of every UI state is decided and approved before the plan; VERIFY has a
  picture to compare with; the spec gate catches a missing screen as early as a missing criterion.
- Costs: UI specs take longer to approve (drawing + review); an agent now draws, so the human must
  actually look — approval of a drawing is only as good as that look. doctor checks the field's
  form, not the artifact's coverage, access or visual quality. Upgrading teams with Approved /
  In progress UI specs must add the field first, or `doctor --strict` turns CI red.

## Alternatives considered
- A DESIGN segment with a Designer role and its own gate: rejected again (ANEW-0008) — the
  existing spec gate widens instead; no new stage.
- Naming one design tool in the core: rejected — ANEW-0011; the adapter owns the tool.
- An escape value (`Design artifact: none — <reason>`): rejected — the rule would leak; the only
  way out is "none — no user interface".

## Revisit triggers
- Approved drawings repeatedly miss states the criteria name (consider a coverage check).
- A team's design tool cannot give a stable address or version.
- Drawing cost outweighs its value on small UI changes (consider a narrower mini-spec rule).
