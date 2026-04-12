# Overview - claude-temper

> North star document. Written before any code. All decisions made here are
> binding until explicitly revised. Do not start coding without reading this first.

---

## What this is

A Manifest V3 Chrome extension (built for Vivaldi) that adds a lightweight project explorer to your claude.ai workflow. Its single job is bringing your local project files closer to Claude — browse them, preview them, and drag them directly into the chat. No server, no API calls, no writing to disk.

---

## Core concept: projects

Everything in the extension revolves around a **project** — a local folder you connect once via the browser's file picker. The extension reads that folder's contents recursively. There is no configuration UI, no sync, no database. The folder structure *is* the config.

You can connect multiple projects and switch between them from the panel. Whichever project is active determines which files are shown.

---

## The pill

The extension has no popup and no browser toolbar UI beyond the extension icon. On claude.ai, a small floating vertical pill appears in the right-hand whitespace alongside the centred chat column. It sits on a fixed z-index above the page — claude.ai's layout is never reflowed or touched.

**Resting state:** a slim, unobtrusive pill centred vertically in the right whitespace. No label, no chrome.

**Hover to expand:** hovering the pill triggers the panel to expand after a ~200ms intent delay. The panel grows right-anchored to fill up to 40% of viewport width and 100% of viewport height, overlaying the whitespace above the page.

**Drag to collapse:** when a file is dragged out of the panel boundary, the panel immediately snaps back to the pill — clearing the view so the chat input is fully visible and easy to drop onto.

**State** (active project, last selected file) persists across hover/collapse cycles via local storage.

---

## The panel

The expanded panel is injected into a Shadow DOM root to keep claude.ai's styles fully isolated. It has three zones:

### Toolbar

A single compact row at the top of the panel. Project switcher and search bar sit side by side in the same container — project switcher is fixed-width on the left, search input takes the remaining space. One thin divider between them. No second row.

- **Project switcher** — a dropdown showing all connected projects. Selecting one switches the active project and refreshes the file tree. A "connect folder…" option at the bottom of the dropdown opens the browser's file picker to add a new project.
- **Search** — live fuzzy search filtering the file tree as you type.

### File tree

A VSCode-style collapsible folder tree showing the full contents of the active project folder, sorted newest-first within each folder (ISO date prefixes in filenames make this automatic). Clicking any file opens it in the preview pane. Any file row is draggable.

### Preview pane

Renders the selected file inline — markdown is rendered, code files are syntax-highlighted. The preview header shows the filename and a "drag to chat" handle. Files can be dragged from either the tree row or the preview header handle.

---

## Drag and drop

Dragging a file from the panel drops it directly into Claude's native chat input, using Claude's built-in file attachment handling. No clipboard, no injection, no synthetic events — it's a standard user drag gesture onto a native drop target.

When a drag leaves the panel boundary the panel snaps back to the pill, opening up the full chat area so the drop target is easy to hit. On a successful drop, a brief confirmation toast appears and the pill returns to rest.

---

## What it intentionally doesn't do

- No writing to disk — read-only access to your project folders
- No session summariser — no prompts injected into the chat input
- No clipboard operations
- No companion server — everything runs in the browser
- No Claude API calls
- No cloud backup or sync
- No Firefox support (Chromium/Vivaldi only)

---

## Known fragility

The only dependency on claude.ai's DOM is the drag-and-drop drop target — specifically, whatever element Claude uses as its file attachment receiver. This is a native browser drag gesture rather than a programmatic injection, so it is significantly more stable than selector-based DOM manipulation. If it ever breaks, it is isolated to the drop target detection logic.
