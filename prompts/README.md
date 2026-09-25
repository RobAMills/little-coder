# Modular system prompt

little-coder's system prompt is **composed from layers**, not hand-edited.
`AGENTS.md` at the repo root is **generated** — edit the layers, then run the composer.

```
prompts/
├── base.md                  identity-agnostic core: autonomy, runtime invariants
├── tools.md                 tool-emission rules (anti "helper persona" drift)
├── providers/
│   ├── default.md           persona overlay for local/small model backends
│   └── m365.md              persona bridge for the Microsoft 365 Copilot backend
└── modes/
    └── reanchor.md          short post-compaction re-anchor (injected separately,
                             not part of the main assembly)
```

Sections still maintained in the legacy AGENTS.md (Available Tools, task
guidelines, etc.) are carried into every composed prompt automatically —
the composer greps them out by heading.

## Composing

```sh
node scripts/compose-prompt.mjs              # default persona -> AGENTS.md (legacy behavior)
node scripts/compose-prompt.mjs --list       # known providers
node scripts/compose-prompt.mjs m365         # print the m365-assembled prompt
node scripts/compose-prompt.mjs m365 --write # write it to AGENTS.md
```

Unknown providers fall back to `default` with a warning.

## Why layers

Different provider backends need *different, sometimes opposite* persona
framing. Small local models need confidence ("you are a coding agent, act
autonomous"); the M365 Copilot foundation persona is a chat "helper" whose
identity contradicts that claim — a contradicting prompt produced
"I can't execute tools" refusals and empty "paste this into little-coder"
code fences in real sessions (see the note in `providers/m365.md`). The
overlay bridges the identity instead of fighting it.

## Wiring into the launcher

`bin/little-coder.mjs` currently passes `AGENTS.md` whole via `--system-prompt`.
To select a provider-specific assembly at launch, resolve the provider from
the chosen model (e.g. `m365/...`) and run
`node scripts/compose-prompt.mjs <provider> --write` before building
`piArgs`, or point `--system-prompt` at a composed temp file per session.

## Post-compaction re-anchor

Compaction summaries don't preserve tool-call alignment. After a compaction
event (or when a session starts misaligned), inject `prompts/modes/reanchor.md`
as a user/system message. It is deliberately short so re-sending is cheap.
