# Docs Standard — claude-helper

## Philosophy

Docs capture **what's worth knowing about this project** — findings, decisions,
reusable prompts, and session records. Written for anyone reading cold, including
future-you. They answer:

- What was found, decided, or built, and why did it matter?
- What is the current state of this thing?
- What comes next, if anything?

Same folder as session notes (`notes/`). Distinguishable by type prefix and
filename at a glance — no need to open the file to know what it is.

---

## Structure

```text
type: short description of the work or finding

Body — the dump. What you found, decided, tried, or built. No polish required,
no minimum length. Write however you think. Include raw prompts, links, errors,
half-ideas — anything that would help someone reconstruct the context.

Next:
    - Unresolved question or follow-up task.
    - Omit entirely if nothing is outstanding.
```

---

## Rules

- **Header** — commit convention style, required always. Scannable without opening the file.
- **Body** — required always. No format enforced. Dense beats polished.
- **Next** — omit if nothing is outstanding.
- One note per thing. Don't combine a finding and a session into one file.

---

## Types

| Type | Use for |
| --- | --- |
| `session:` | Work sessions spanning multiple concerns — what was done, what changed |
| `finding:` | Isolated discoveries worth preserving — API quirks, browser behaviour, gotchas |
| `research:` | Deeper investigations with conclusions — when you spent real time looking into something |
| `prompt:` | Reusable Claude prompts and context setters for this project |

`decision:` and `fix:` are intentionally excluded — folds naturally into `session:` or `finding:` for this project.

---

## File Naming

```text
YYYY-MM-DD_type_short-slug.md
```

- **Date** — ISO format, chronological order for free.
- **Type** — matches the header type exactly.
- **Slug** — 2–5 words, hyphens, derived from the header. The filename alone
  should tell you what's inside.

```text
2026-04-11_session_arch-scope-decisions.md
2026-04-11_finding_vivaldi-sidepanel-broken.md
2026-04-11_research_file-system-access-api.md
2026-04-12_prompt_project-context-setter.md
```

---

## Examples

### finding — a browser quirk discovered during research

```text
finding: vivaldi does not support chrome.sidePanel.setOptions dynamically

chrome.sidePanel.setOptions() is silently ignored in Vivaldi — the panel opens
as about:blank. confirmed by open bug VB-120826. workaround is chrome.windows.create
with type "popup" which works correctly in all chromium-based browsers including
Vivaldi. side effect: floating window instead of docked panel, which is acceptable
for this use case.
```

### session — a work session across multiple concerns

```text
session: panel window lifecycle and tab detection

wired up background.js to auto-open the panel when the active tab is claude.ai.
uses chrome.tabs.onActivated + chrome.tabs.onUpdated + chrome.windows.onFocusChanged.
panel state persisted to chrome.storage.local so close/reopen is seamless.
one panel window at a time — tracked by windowId, focused rather than duplicated.

Next:
    - Test behaviour when switching between multiple claude.ai tabs.
    - Handle the edge case where the panel window is manually closed by the user.
```

### prompt — a reusable context setter

```text
prompt: context setter for resuming claude-helper work

"Here's where we left off on claude-helper: it's a Manifest V3 Chrome extension
for claude.ai. Vanilla JS, no build step, no DOM injection except prompt submit.
Panel is a persistent floating window via chrome.windows.create. Active feature
is [X]. Open issue is [Y]. SPEC.md is the north star. Pick up from there."
```

### research — a real investigation with conclusions

```text
research: file system access api — handle persistence across sessions

investigated whether FileSystemDirectoryHandle can survive browser restarts when
stored in chrome.storage.local. answer: yes, with caveats. the handle serialises
fine but permission must be re-requested after a cold start — queryPermission()
returns "prompt" until the user re-grants it. workaround is to call requestPermission()
on first use each session rather than assuming it's still valid. MDN docs confirmed
this is expected behaviour, not a bug.

Next:
    - Decide whether to request permission silently on panel open or lazily on
      first file operation.
```
