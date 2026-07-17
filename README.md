# HTML Editor

A standalone, dependency-free WYSIWYG editor for static HTML pages — runs entirely in the browser with a single double-click. No installation, no server, no build step.

Built by [Cortex AI Solutions](https://www.cortex-ai.de/).

---

## What it does

Load a local HTML file, edit it visually, and save it back — without touching source code. Designed for non-technical users who maintain static websites.

- **Click** any element to select it (smart block-level selection)
- **Double-click** to edit text inline
- **Insert, replace, move, copy or delete** elements via toolbar
- **Undo** any change — up to 20 steps (Strg+Z)
- **Two modes** — Text Mode for editing, Photo Mode for image placement
- **Insert images** via drag & drop from Windows Explorer or via toolbar
- **Blue insertion cursor** shows exactly where a photo will land before confirming
- **Replace images** with one click when an image is selected
- **Set hyperlinks** — internal pages or external URLs, with automatic relative-path calculation; links are directly selectable, and re-linking a teaser button updates the existing `href` instead of nesting links
- **Create new pages from templates** — **Save As** (`showSaveFilePicker`) turns any existing page into a new one; subsequent saves write to the new file
- **Insertion-position preview** in the photo dialog, with a warning when the target is a heading
- **Scroll position preserved** across edits; newly inserted photos are scrolled into view
- **Live preview** with correct CSS styling and images (via folder loading)
- **Save in place** — files opened via the working-folder picker are overwritten directly, no dialog, no Downloads-folder copies; files opened from elsewhere fall back to the native save dialog, remembered for subsequent saves

---

## How to use

1. Download `HTML-Editor.html`
2. Open it with any modern browser (Chrome, Edge, Firefox)
3. Click **📁 Arbeitspfad festlegen** → select your website folder (loads CSS + images)
4. Click **📂 Öffnen** → open an HTML file from that folder
5. Choose mode: **✎ Text-Modus** for text editing, **📷 Foto-Modus** for inserting photos
6. Edit visually → **💾 Speichern** overwrites the original directly (no dialog, since the file's location is known from steps 3–4); **💾 Speichern unter …** creates a new page from the current one

> The UI is currently in German. An English version is not planned at this time.

---

## Technical highlights

| Feature | Implementation |
|---|---|
| No dependencies | Single `.html` file, vanilla JS only |
| Live preview | `<iframe srcdoc>` + `postMessage` |
| CSS in preview | `<link>` tags replaced with inlined `<style>` blocks (preview only) |
| Image preview | Relative `src` paths replaced with temporary Blob URLs (preview only) |
| Folder access | `showDirectoryPicker({mode:'readwrite'})`, walked recursively; falls back to `<input webkitdirectory>` (read-only) where unsupported |
| Inline editing | `contentEditable` inside the iframe |
| HTML parsing | `DOMParser` — no regex on markup |
| Block selection | `nearestBlock()` walks up the DOM to the first meaningful block element |
| Photo insertion | Blob URL lifecycle managed via `blobMap`; URLs survive multiple insertions |
| Drag & drop | `dragover`/`drop` events in iframe → `postMessage` to parent with `File` object |
| Insertion cursor | Blue line rendered in iframe via `position:absolute` div, updated on click/drag |
| Two-mode system | `editorMode` embedded in iframe script at build time; updated via `postMessage` |
| Undo | `undoStack` array (max 20), saves `currentHtml` snapshots before each mutation |
| Save integrity | Original `<link>` tags and image paths preserved; Blob URLs replaced before saving |
| Direct saving | Writable handle resolved from the working-folder handle for files opened from it (`getDirectoryHandle`/`getFileHandle`), or a retained `FileSystemFileHandle` from `showOpenFilePicker`/`showSaveFilePicker`; Blob download fallback for browsers without the API |
| Encoding | UTF-8 read and write |

---

## Files

| File | Description |
|---|---|
| `HTML-Editor.html` | The editor — this is the only file you need |
| `Bedienungsanleitung-HTML-Editor.html` | Step-by-step user guide (German) |
| `documentation.md` | Development log (German) |

---

## Browser support

Chrome, Edge, Firefox — any modern browser with File API and `webkitdirectory` support.
Safari has limited `webkitdirectory` support; folder loading may not work correctly.

---

## License

MIT License — see [LICENSE](LICENSE) for details.

---

## Background

Originally developed for the [Südthüringer Literaturverein](https://holgeuske.de) to allow non-technical staff to maintain their static HTML website without a code editor. Iterated over multiple development sessions with Claude (Anthropic).
