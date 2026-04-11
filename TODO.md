# Todo — claude-temper

> Tasks are ordered by build phase. Complete each phase before moving to the next.
> Update this file as decisions are made or scope changes.

---

## Phase 1 · Scaffolding

- [ ] Initialise repo with Manifest V3 structure (`manifest.json`, `background.js`, `content.js`)
- [ ] Configure `host_permissions` for `claude.ai`
- [ ] Add `"fileSystem"` / File System Access API permissions
- [ ] Set up `content_scripts` entry to inject panel on `claude.ai` pages
- [ ] Add toolbar button (no popup — button triggers panel open/focus)
- [ ] Confirm extension loads in Vivaldi without errors

---

## Phase 2 · Floating panel

- [ ] Create panel as a standalone HTML window opened via `chrome.windows.create`
- [ ] Ensure only one panel exists at a time (focus existing if already open)
- [ ] Auto-open panel when navigating to `claude.ai`
- [ ] Auto-close panel when leaving `claude.ai`
- [ ] Persist panel state (active project, review pane content) via `chrome.storage.local`
- [ ] Restore state correctly on close/reopen

---

## Phase 3 · Project management

- [ ] Implement "Connect project" flow via `window.showDirectoryPicker()`
- [ ] Store directory handle in `chrome.storage.local` (verify persistence across sessions)
- [ ] Request and re-verify read/write permission on each session start
- [ ] Support multiple connected projects
- [ ] Add project switcher UI in panel (dropdown or list)
- [ ] Active project selection persists across close/reopen

---

## Phase 4 · Note filer

- [ ] Read all `.md` files from `<project>/notes/` on panel open and project switch
- [ ] Sort notes newest-first (rely on `YYYY-MM-DD` filename prefix)
- [ ] Display note list in panel with filename and truncated first line
- [ ] Click note → copy full contents to clipboard
- [ ] Show brief confirmation toast on copy
- [ ] Refresh note list after a new note is saved

---

## Phase 5 · Session summariser

- [ ] Write the summarise prompt (per `NOTES_STANDARD.md` format)
- [ ] Implement DOM injection: find claude.ai chat input, insert prompt, submit
- [ ] Define selector constant in a single isolated location (easy to patch if claude.ai changes markup)
- [ ] Handle failure gracefully if selector not found (surface error in panel, don't silently fail)
- [ ] Detect when Claude finishes responding and capture the output
- [ ] Parse response into: header/type, body, optional `prompt:` block

---

## Phase 6 · Review pane & save

- [ ] Show review pane in panel after summarise completes
- [ ] Editable filename field (pre-filled by Claude, following `YYYY-MM-DD_type_short-slug.md`)
- [ ] Editable note header field
- [ ] Toggle to strip `prompt:` continuation block before saving
- [ ] **Save to notes/** button — write file via File System Access API
- [ ] Confirmation toast on successful save
- [ ] Clear review pane and refresh notes list after save
- [ ] Handle write errors (permissions revoked, disk full, etc.)

---

## Phase 7 · Polish & hardening

- [ ] Write `NOTES_STANDARD.md` and bake final version into summarise prompt
- [ ] Edge case: panel behaviour when multiple claude.ai tabs are open
- [ ] Edge case: summarise triggered before Claude has finished a previous response
- [ ] Edge case: project folder moved/deleted between sessions — surface clear error
- [ ] Basic end-to-end test: connect project → summarise session → save note → note appears in list
- [ ] Review all `console.error` paths — nothing should fail silently
- [ ] Strip any debug logging before first real use

---

## Backlog / future

- [ ] Drag-and-drop note loading into chat (deferred — clipboard covers workflow for now)
- [ ] Firefox / cross-browser support (explicitly out of scope for v1)
- [ ] Note search / filter in panel
- [ ] Delete or archive notes from panel

---

## Notes & decisions

- No companion server, no API calls, no cloud sync — intentional, keep it that way
- File System Access API is the only mechanism for reading/writing notes
- DOM selector for chat input is the single fragility point — isolate and document it clearly
