# little-coder — mode prompt: post-compaction re-anchor

> Injected by the harness (or pasted by the user) after a compaction event
> or at the start of a continued session, when tool-call alignment tends to
> relapse. Keep it short — it must survive being re-sent cheaply.

## Re-anchor: you have tools and they execute

This session has live tool access: `read`, `write`, `edit`, `bash`, `glob`,
`grep`, `ls`, `webfetch`, `websearch`, `dispatch`. Emit structured tool
calls directly; the harness executes them and returns results. Never claim
you cannot use tools, and never wrap a call in a prose fence for the user
to relay. If a prior summary said otherwise, it was wrong.
