# Todo — claude-temper

> Tasks are ordered by build phase. Complete each phase before moving to the next.
> Update this file as decisions are made or scope changes.

---

## Phase 1 · Scaffolding

- [ ] Initialise repo with Manifest V3 structure (`manifest.json`, `background.js`, `content.js`)
- [ ] Configure `host_permissions` for `claude.ai`
- [ ] Add `"fileSystem"` / File System Access API permissions
- [ ] Set up `content_scripts` entry to inject on `claude.ai` pages
- [ ] Confirm extension loads in Vivaldi without errors

---

## Phase 2 · Pill & panel shell

- [ ] Inject a Shadow DOM root into `claude.ai` via content script
- [ ] Render the resting pill — fixed position, right-hand whitespace, vertically centred
- [ ] Implement hover-to-expand with ~200ms intent delay
- [ ] Panel expands right-anchored, up to 40% viewport width, full viewport height, overlays page
- [ ] Panel snaps back to pill when drag leaves panel boundary
- [ ] Persist panel state (active project, last selected file) via `localStorage`
- [ ] Restore state correctly on page reload / tab reopen

---

## Phase 3 · Project management

- [ ] Implement "Connect project" flow via `window.showDirectoryPicker()`
- [ ] Store directory handle in `chrome.storage.local` (verify persistence across sessions)
- [ ] Request and re-verify read permission on each session start
- [ ] Support multiple connected projects
- [ ] Project switcher dropdown in toolbar — switches active project and refreshes file tree
- [ ] "Connect folder…" option at bottom of dropdown opens file picker
- [ ] Active project selection persists across panel collapse/expand cycles

---

## Phase 4 · File tree

- [ ] Recursively read the active project folder on project select
- [ ] Render a VSCode-style collapsible folder tree
- [ ] Sort files newest-first within each folder (relies on `YYYY-MM-DD` filename prefixes)
- [ ] Clicking a file opens it in the preview pane
- [ ] Every file row is draggable
- [ ] Live fuzzy search in toolbar filters the tree as you type

---

## Phase 5 · Preview pane

- [ ] Render selected file inline — markdown rendered, code files syntax-highlighted
- [ ] Preview header shows filename and a "drag to chat" handle
- [ ] Files are draggable from both the tree row and the preview header handle

---

## Phase 6 · Drag and drop

- [ ] File drag from panel drops into Claude's native chat input via standard browser drag gesture
- [ ] Panel snaps to pill when drag leaves panel boundary (exposes chat drop target)
- [ ] Show brief confirmation toast on successful drop
- [ ] Pill returns to resting state after toast
- [ ] Isolate drop-target detection logic in a single location for easy patching

---

## Phase 7 · Polish & hardening

- [ ] Edge case: panel behaviour when multiple claude.ai tabs are open
- [ ] Edge case: project folder moved/deleted between sessions — surface clear error in panel
- [ ] Edge case: directory handle permission revoked mid-session
- [ ] Basic end-to-end test: connect project → browse tree → preview file → drag to chat
- [ ] Review all `console.error` paths — nothing should fail silently
- [ ] Strip debug logging before first real use

---

## Backlog / future

- [ ] Firefox / cross-browser support (explicitly out of scope for v1)
- [ ] Delete or archive notes from panel

---

## Notes & decisions

- No companion server, no API calls, no cloud sync — intentional, keep it that way
- No writing to disk — read-only access to project folders
- No clipboard operations, no prompt injection, no synthetic events
- Panel is Shadow DOM injected into claude.ai — no popup, no `chrome.windows.create`
- Drop-target detection is the single fragility point — isolate and document it clearly
- Phases 4–6 from the original TODO (note filer, session summariser, review pane) are cut — out of scope
