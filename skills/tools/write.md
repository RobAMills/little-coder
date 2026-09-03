---
name: write-guidance
type: tool-guidance
target_tool: write
priority: 10
token_cost: 180
user-invocable: false
---
## `write` Tool
Create a **new** file with the given content. Creates parent directories automatically.

REQUIRED: path (absolute), content (full file content)

**`write` is for creating new files only.** If the file already exists, `write` will be **refused** by the tool and return an error telling you to use `edit` instead. Do not retry `write` on the same path — it will be refused again.

WHEN TO USE `write`:
- The file does not exist yet and you are creating it from scratch

WHEN TO USE `edit` INSTEAD:
- ANY change to an existing file — bug fixes, refactors, format tweaks, adding a function, renaming a variable, everything. `edit` takes `path` + `edits: [{oldText, newText}]` and patches in place.
- Iterating after a failed test — never retype the whole file

If you need to completely replace an existing file's content, `edit` can still do that: pass the entire current content as `oldText` and the full new content as `newText`. Use `read` first if you don't already have the file's current content.

EXAMPLE:
```tool
{"name": "write", "input": {"path": "/tmp/example/new_module.py", "content": "def hello():\n    return 'hi'\n"}}
```
NOTE: Always use the EXACT file path given in the task, never a placeholder.

### M365 Backend Note (when provider is the M365 Copilot server)
The M365 backend truncates very long tool-call payloads mid-generation. To
write large files reliably:

- Keep `content` under **~6,000 characters** per `write` call.
- For longer files: `write` the first ~6,000 chars, then use `edit` to append
  the remainder in chunks (oldText = the file's current final line, newText =
  that line plus the next chunk).
- Inside string arguments, escape every newline as `\n` and every double
  quote as `\"` — malformed escaping is the most common cause of failed
  tool-call parsing on this backend.
