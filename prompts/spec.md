# Prompt: Write the Spec

```
Role: Analyst. Using <specs/TEMPLATE.md | specs/TEMPLATE-mini.md for a change request or incident>,
write specs/active/<NNNN>-<name>.md for: "<feature>". Fold in the intent and every clarify
decision we made. (Mini: Changed behavior + Preserved behavior criteria, Source mandatory.)

Requirements must describe BEHAVIOR — no technical solutions (no endpoints, tables, caches).
Acceptance criteria: each line independently testable; cover the happy path, boundaries,
empty states, invalid inputs, and authorization. Constraints include explicit OUT OF SCOPE
items, and `Sensitive area: <name>` when the work touches one (docs/domain.md).
User experience: fill flow, states, design artifact and accessibility, or "none — no user
interface". Every listed state gets a criterion. UI work gets a DESIGN ARTIFACT (ANEW-0013):
versioned and opened by address, drawing every screen the criteria touch in every listed state,
each drawing labelled with its criterion (AC-n). An approved design already exists → link its
fixed version, never redraw it. Otherwise you draw it — a proposal until the spec gate approves
it. Name it in `- Design artifact: <http(s) address> (version <id>)` (mini-spec: `- Design:`,
changed screens only). Spec approval = the spec and that artifact version together: never
present the spec without it, or with a criterion's screen or state missing. A change to the
artifact after approval is a spec change (R-08).
Write in the docs language (AGENTS.md, Language); field labels and Status values stay English.

Then run a SELF-CRITIQUE pass as a hostile reader: which questions would a developer still have?
(ambiguous criteria, missing boundary values, undefined error behavior). List each gap with your
recommendation; I will approve which ones get folded in. Then set `- Self-critique: done`; if
the critique waits on something (e.g. a split spec that waits for another to ship), set
`pending (<condition>)` — doctor keeps it visible on a Draft and flags it as a gate once the
spec is Approved.
SIZE: more than one user capability, or more than ~8–10 acceptance criteria → recommend a split:
name the specs and their order. If I keep it as one, write that into Constraints & out of scope
(Split: proposed <specs>; kept as one — <who>, <reason>). Do not write a plan or code.
```
