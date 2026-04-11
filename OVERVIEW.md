# claude-temper

> North star document. Written before any code. All decisions made here are
> binding until explicitly revised. Do not start coding without reading this first.

---

## What this is

A Manifest V3 Chrome extension (built for Vivaldi) that adds a persistent floating panel to your claude.ai workflow. The panel has two jobs: letting you load saved notes into chat as context, and automatically turning a session into a structured note you can save directly to your filesystem — no server, no API calls, no clipboard gymnastics beyond the minimum.

---

## Core concept: projects and the notes folder

Everything in the extension revolves around a **project** — a local folder you connect once via the browser's file picker. Inside that folder, the extension reads and writes a `notes/` subfolder. That's it. There's no configuration UI, no sync, no database. The folder structure *is* the config.

You can have multiple projects connected and switch between them from the panel. Whichever project is active determines which notes are shown and where new ones get saved.

---

## The panel

The extension has no popup. When you're on claude.ai, a floating window opens automatically alongside it. It closes when you leave. If you close it manually, the toolbar button reopens it. Only one panel exists at a time — reopening focuses it rather than creating a duplicate.

State (active project, any in-progress note review) persists across close/reopen via local storage, so nothing is lost if you accidentally dismiss it.

---

## Feature 1: Note filer

The notes browser shows all `.md` files in your active project's `notes/` folder, sorted newest-first (ISO date prefixes make this automatic). Clicking a note copies its content to your clipboard with a brief confirmation toast — paste it into chat to give Claude context.

That's the entire feature. Simple by design. Drag-and-drop loading may come in a future version, but clipboard copy covers the workflow cleanly.

---

## Feature 2: Session summariser

A single **Summarise session** button injects a pre-written prompt into the current chat and submits it. Claude reads the conversation and produces a structured note — the right note type, a dense enough body to cold-restart the session, and a `prompt:` continuation block ready to paste into the next chat.

Once Claude finishes, the note appears in a review pane inside the panel:

- Editable header and filename (Claude pre-fills both based on the session content)
- A toggle to strip the `prompt:` continuation block before saving, if you don't want it
- A **Save to notes/** button that writes the file directly to your project folder via the File System Access API

On save: confirmation toast, review pane clears, notes list refreshes.

---

## Note format

Notes follow a shared standard (defined in `NOTES_STANDARD.md`, baked into the summarise prompt). Each note has a typed header, e.g. `session:`, `fix:`, `research:`, `decision:`, `finding:`, or `prompt:` — and a dense body. Filenames follow `YYYY-MM-DD_type_short-slug.md`.

The `prompt:` continuation block at the end of a session note is a ready-to-paste context setter — open a new chat, paste it in, and Claude has enough to pick up where you left off.

---

## What it intentionally doesn't do

- No companion server — everything runs in the browser
- No Claude API calls — the extension only talks to the tab that's already open
- No injection into claude.ai's UI beyond submitting the summarise prompt into the chat input (the one unavoidable touch point)
- No cloud backup or sync
- No Firefox support (Chromium/Vivaldi only)

---

## Known fragility

The summarise button works by finding claude.ai's chat input element and submitting text into it. This is the one place the extension depends on claude.ai's DOM structure. If claude.ai changes their markup, this breaks — but it's isolated to a single selector constant, so it's a one-line fix.
