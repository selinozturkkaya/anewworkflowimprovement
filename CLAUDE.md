# CLAUDE.md

All project rules live in **AGENTS.md** — single source of truth. Read it before doing anything.
Honor the operating mode it declares. Do not duplicate rules here; if this file and AGENTS.md ever
disagree, AGENTS.md wins.

**Design artifact in Claude Code** (tool extra for ANEW-0013; the rule is in `prompts/spec.md`):
publish the drawing as a Claude artifact, and write its address and the version id that
`get_artifact` returns into `- Design artifact: <address> (version <id>)`. Artifacts start
private and you cannot share them: at the spec gate, remind the human to share it with the team
before approving. After approval, do not republish it — the tools open only the live version, so
VERIFY checks that the live version id still equals the spec's; any change goes through R-08.

**This repo develops ANEW itself:** do not run bootstrap here — first read `.anew-dev/README.md`,
section "Bu çalışma kopyası" (mode, language, approver, verification). Template users: delete this
paragraph together with `.anew-dev/`.
