# AppsWeb

Small, focused web tools that run in the browser. No install, no account, no tracking — everything stays on your device.

**Live site:** https://storecode-hub.github.io/AppsWeb/

## Tools

| Tool | Status | What it does |
|---|---|---|
| **Time Note** | Live — [`time-note.html`](time-note.html) | Log video timestamps on a 20-key pad, attach one link per clip, export the lot as a clean `.txt` |
| Smart Collector | Soon | Type, collect, format lists |
| Quick Write | Soon | Free-form notepad |

## Time Note

A mobile-first rebuild of **Card 3 ("Time Note")** from the NoteBox 2 Android app (`com.notebox.app2`, v2.16), ported from the original Java so the behaviour matches the app keypress for keypress.

**Logging.** You never type `(` `)` or `:`. A 20-key pad drives an 8-state machine: the second end-SS digit closes the clip atomically, a leading digit of 6 or more auto-pads (`6` → `06:`), `OK` zero-fills the current field, `+` chains another segment into the same clip, `ENT` closes the entry and starts the next.

**Nudge keys.** The bottom row is `+4 +5 +6 +7` (violet). They only answer right after the dash — type `0808`, the pad inserts ` - `, then tap `+4` and the clip closes as `(08:08 - 08:12)`, i.e. end = start + 4 s. The timeline is a sub-hour log, so anything past `59:59` is capped there (`59:57 +4` → `59:59`), and a start already at `59:59` is refused. Pressed in any other state they change nothing and explain why.

**Links.** Each entry holds one URL. The chip on the left opens the editor; the blue button on the right pastes straight from the clipboard in a single tap and cleans the URL (`youtu.be`, `/shorts`, `/live` → canonical `watch?v=`, tracking params stripped). If the browser blocks clipboard reads, the next paste gesture is applied directly instead. Duplicate links raise the red ⚠️ counter.

**Export.** Three formats, with a live preview of the exact file:

```
/* Video 1 */
URL: https://www.youtube.com/watch
Clips: (00:10 - 00:30) + (00:40 - 01:00), (01:40 - 02:00)

/* Video 2 */
URL: (no link)
Clips: (00:20 - 00:40)
```

Files download as `TimeNote_9-41am_(2L0D)_02-11-26.txt` — the same naming the app uses. Copy and the native share sheet are also there.

### What differs from the Android app

- Camera QR scanning is replaced by clipboard paste (the app's only camera use).
- The action bar is sized to fit a 320 px viewport, so it never scrolls sideways.
- Scheme-less pastes (`youtu.be/xyz`) are normalised too; the app deliberately left them alone.
- Storage is `localStorage` instead of `SharedPreferences`, and old damaged logs are repaired on load using the same routine as v2.16.

### Verification

The engine (`assets`-free, inlined in the page) is a direct port of `handleTnKey`, `rederiveStateSafe`, `sweepTnContent`, `buildTnExport`, `repairTnContent` and `sanitizeUrl`. It is covered by:

- **58 Python checks** against the original spec — bug reproduction on the old `countLines`, the v2.16 fix, repair of 7 damage classes, byte-exact export for both original formats, 18 keypad edge cases, deep-`DEL` self-healing, a full re-entry session, and the URL sanitizer table.
- **28 nudge checks** — all four buttons, the `59:55`/`59:57`/`59:58`/`59:59` cap boundaries, every refusal path, chaining, export and undo.
- **DOM checks** against the built page — keypad row order and column alignment, nudge keys, clip entry and chaining, direct paste, blocked-clipboard fallback, export previews, undo/redo, persistence, clear and the zoom lock.

## Layout

```
index.html          home
time-note.html      the Time Note tool (self-contained, no build step, works offline)
.nojekyll           serve files as-is on GitHub Pages
LICENSE             MIT
```

## Run locally

Open `index.html` in a browser, or serve the folder:

```bash
python3 -m http.server 8000
```

Clipboard reading requires `https://` or `localhost` — on a `file://` page the paste button falls back to the paste gesture.

## Licence

MIT — see [LICENSE](LICENSE).
