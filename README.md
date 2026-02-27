# Parakeet Dictation (macOS)

Local, fast, privacy-friendly dictation for macOS using NVIDIA Parakeet (MLX on Apple Silicon) with push-to-talk hotkeys.

---

## Table of Contents

- [Why this project?](#why-this-project)
- [Features](#features)
- [Demo](#demo)
- [Requirements](#requirements)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
  - [Hold-to-record (Ctrl+Alt+A)](#hold-to-record-ctrlalta)
  - [Toggle-to-record (§)](#toggle-to-record-)
  - [Menu bar controls](#menu-bar-controls)
- [Permissions (macOS)](#permissions-macos)
- [Run in the background](#run-in-the-background)
- [Troubleshooting](#troubleshooting)
- [Development](#development)
- [Roadmap](#roadmap)
- [FAQ](#faq)
- [Credits](#credits)
- [License](#license)

---

## Why this project?

Parakeet Dictation gives you **on-device** speech-to-text on macOS with a **single push-to-talk key**. It’s built to be:

- **Private**: Audio is processed locally on your Mac.
- **Fast**: Parakeet models are optimized and run great on Apple Silicon via MLX.
- **Practical**: Dictate into *any* app with two hotkey modes — hold-to-record or toggle-to-record.

---

## Features

- 🖥️ **Menu bar** app (stays out of your way)
- 🎙️ **Hold-to-record**: Hold **Ctrl+Alt+A** to record, release to transcribe & paste
- 🔁 **Toggle-to-record**: Press **§** to start recording, press again to stop & transcribe
- ⚡ **Local ASR** with **NVIDIA Parakeet** (Apple Silicon via MLX)
- ⌨️ **Auto-paste at cursor** in the foreground app
- ✅ Clear **recording status** via the menu bar icon
- 🧰 Simple **background mode** (no UI) for power users

---

## Demo

TBD

---

## Requirements

- **macOS 12+** (Apple Silicon recommended for speed)
- **Python 3.10+**
- **Microphone**
- **Accessibility permission** (to paste text programmatically)
- **PortAudio** (for PyAudio)

---

## Installation

### 1. Install system deps

```bash
brew install portaudio ffmpeg
```

### 2. Clone & install Python deps

```bash
git clone https://github.com/osadalakmal/parakeet-dictation.git
cd parakeet-dictation

uv venv -p 3.12.5
source .venv/bin/activate
uv add -r requirements.txt
```

---

## Usage

### Hold-to-record (Ctrl+Alt+A)

1. Launch the app (see Development or Background sections below).
2. Press and **hold** **Ctrl+Alt+A** to start recording.
3. Speak normally.
4. **Release** the keys to stop. The app will transcribe and paste the text at your current cursor position.

### Toggle-to-record (§)

1. Press the **§** key once to start recording.
2. Speak normally.
3. Press **§** again to stop. The app will transcribe and paste the text at your current cursor position.

### Menu bar controls

- Start/Stop Listening - toggles recording
- Settings - (future) configuration UI
- Quit - exits the app

---

## Permissions (macOS)

- **Microphone**: System Settings → Privacy & Security → Microphone → allow your Terminal/app
- **Accessibility**: System Settings → Privacy & Security → Accessibility → allow your Terminal/app

Without Accessibility permission, the app cannot paste text for you.

---

## Run in the background

```bash
nohup PYTHONPATH=src python -m parakeet_dictation.main >/dev/null 2>&1 & disown
```

Stop it later:

```bash
ps aux | grep 'parakeet_dictation'
kill -9 <PID>
```

---

## Troubleshooting

- No audio: ensure `portaudio` is installed
- Nothing pastes: check Accessibility permissions
- High CPU usage: first run warms up the model

---

## Development

```bash
source .venv/bin/activate
PYTHONPATH=src PARAKEET_LOG=debug python -m parakeet_dictation.main
```

- Menu bar UI via `rumps`
- Hotkey via `pynput`
- Audio capture via `pyaudio`
- ASR via `parakeet-mlx`

---

## Roadmap

- Preferences UI (Status bar is pretty finicky)
- Streaming/partial results (It seems nice to start pasting things soon as we can)
- macOS app packaging (Non techie folks can use this as well)
- Latency/quality settings

---

## FAQ

**Does dictation send audio to the cloud?** No. Local only.
**What languages are supported?** English. That's the only one I know :). Feel free to try other languages parkeet supports.

---

## Credits

- Parakeet MLX (NVIDIA Parakeet on Apple Silicon)
- Originally forked from a [Whisper-based dictation app](https://github.com/ashwin-pc/whisper-dictation)

---

## License

MIT
