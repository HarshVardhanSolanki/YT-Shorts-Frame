# 🎬 YT Shorts Frame by HVS

> Frame-by-frame navigation for YouTube Shorts — a control YouTube doesn't give you.

![Version](https://img.shields.io/badge/version-1.0.0-ff4444?style=flat-square)
![Manifest](https://img.shields.io/badge/manifest-v3-orange?style=flat-square)
![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)
![Platform](https://img.shields.io/badge/platform-Chrome-blue?style=flat-square)

---

## What It Does

YouTube Shorts plays at 30 fps. This extension lets you pause any Short and step through it **one frame at a time** using your keyboard — backward or forward — with a live HUD showing exactly which frame you're on.

```
[ Paused Short ]  →  ,  ◀◀ back one frame
                  →  .  ▶▶ forward one frame
```

Works on the Shorts page and when the **miniplayer** is open on a Shorts video. Completely inactive everywhere else.

---

## Installation

### From source (Developer Mode)

1. [Download the ZIP](../../releases) and unzip it, or clone this repo
2. Open Chrome and navigate to `chrome://extensions/`
3. Enable **Developer Mode** using the toggle in the top-right corner
4. Click **Load unpacked**
5. Select the `yt-shorts-framer` folder
6. The extension icon appears in your toolbar — you're ready

> **After any code update:** click the ↺ refresh icon on the extension card, then hard-reload the YouTube tab with `Ctrl+Shift+R`.

---

## Usage

| Key | Action |
|-----|--------|
| `,` (comma) | ◀◀ Step back one frame |
| `.` (period) | ▶▶ Step forward one frame |

1. Open any YouTube Short (`youtube.com/shorts/…`)
2. The video can be playing or already paused
3. Press `,` or `.` — if the video is playing, **first press pauses it**
4. Keep pressing to step frame by frame
5. A small HUD overlay at the bottom of the screen shows:

```
◀◀  Frame 47 / 450   1.567s
```

### Miniplayer support

If you open a Short and then navigate elsewhere on YouTube, the Shorts video moves into the miniplayer. The keyboard shortcuts continue to work — the extension detects the miniplayer context automatically.

---

## How It Works

```
Keyboard Event (capture phase)
        │
        ▼
  isShortsActive()?
  ├── pathname = /shorts/*  ──────────────────────┐
  └── ytd-miniplayer[active] + canonical = /shorts ┘
        │
        ▼
  getShortsVideo()
  └── video.video-stream.html5-main-video
        │
        ▼
  video.pause()  →  video.currentTime ± (1/30)s
        │
        ▼
  HUD overlay: "▶▶ Frame N / Total   X.XXXs"
```

The content script runs in **capture phase**, intercepting `,` and `.` before YouTube's own keyboard handlers, preventing any conflicts.

---

## File Structure

```
yt-shorts-framer/
├── manifest.json       # Extension config (Manifest V3)
├── content.js          # Frame-step logic + HUD overlay
├── popup.html          # Toolbar popup UI
├── popup.js            # Popup status detection
└── icons/
    ├── icon16.png
    ├── icon48.png
    └── icon128.png
```

---

## Permissions

| Permission | Why it's needed |
|---|---|
| `activeTab` | Reads the current tab URL so the popup can show whether you're on a Shorts page |
| `scripting` | Injects the keyboard listener and HUD overlay into the YouTube page |
| `host_permissions: youtube.com/*` | Required to run the content script on YouTube — the script only activates on `/shorts/` paths or a Shorts miniplayer |

No data is collected. No network requests are made. Nothing is tracked. All logic runs locally in your browser.

---

## Limitations

- Frame rate is fixed at **30 fps** — YouTube Shorts are standardized at 30 fps but there's no browser API to read the actual encoded fps, so this assumption holds for all Shorts
- Requires the video to be **paused** before frame stepping (first keypress auto-pauses)
- Does not work on regular YouTube videos — Shorts only, by design

---

## License

MIT — do whatever you want with it.

---

<p align="center">
  Built because YouTube should have shipped this years ago.
</p>
