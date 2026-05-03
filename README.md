# HTML Editor

A standalone, dependency-free WYSIWYG editor for static HTML pages — runs entirely in the browser with a single double-click. No installation, no server, no build step.

Built by [Cortex AI Solutions](https://www.cortex-ai.de/).

---

## What it does

Load a local HTML file, edit it visually, and save it back — without touching source code. Designed for non-technical users who maintain static websites.

- **Click** any element to select it
- **Double-click** to edit text inline
- **Insert, replace, move, copy or delete** elements via toolbar
- **Insert or replace images** from your local file system
- **Set hyperlinks** — internal pages or external URLs, with automatic relative-path calculation
- **Live preview** with correct CSS styling and images (via folder loading)
- **Save** downloads the edited file, original `<link>` tags preserved

---

## How to use

1. Download `HTML-Editor.html`
2. Open it with any modern browser (Chrome, Edge, Firefox)
3. Click **📁 Arbeitspfad festlegen** → select your website folder (loads CSS + images)
4. Click **📂 Öffnen** → open an HTML file from that folder
5. Edit visually → **💾 Speichern** to download

> The UI is currently in German. An English version is not planned at this time.

---

## Technical highlights

| Feature | Implementation |
|---|---|
| No dependencies | Single `.html` file, vanilla JS only |
| Live preview | `<iframe srcdoc>` + `postMessage` |
| CSS in preview | `<link>` tags replaced with inlined `<style>` blocks (preview only) |
| Image preview | Relative `src` paths replaced with temporary Blob URLs (preview only) |
| Folder access | `<input webkitdirectory>` — works in Chrome, Edge, Firefox |
| Inline editing | `contentEditable` inside the iframe |
| HTML parsing | `DOMParser` — no regex on markup |
| Save integrity | Original `<link>` tags and image paths preserved in saved file |
| Encoding | UTF-8 read and write |

---

## Files

| File | Description |
|---|---|
| `HTML-Editor.html` | The editor — this is the only file you need |
| `Bedienungsanleitung-HTML-Editor.html` | Step-by-step user guide (German) |
| `documentation.md` | Development log (14 build steps, German) |

---

## Browser support

Chrome, Edge, Firefox — any modern browser with File API and `webkitdirectory` support.
Safari has limited `webkitdirectory` support; folder loading may not work correctly.

---

## License

MIT License — see [LICENSE](LICENSE) for details.

---

## Background

Originally developed for the [Südthüringer Literaturverein](https://holgeuske.de) to allow non-technical staff to maintain their static HTML website without a code editor. Iterated over 14 development sessions with Claude (Anthropic).
