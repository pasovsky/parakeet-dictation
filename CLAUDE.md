# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Parakeet Dictation is a macOS menu bar dictation app using NVIDIA Parakeet (via MLX on Apple Silicon) for local speech-to-text. All processing is on-device — no cloud.

## Development Commands

```bash
# Activate venv
source .venv/bin/activate

# Run in development mode with debug logging
PYTHONPATH=src PARAKEET_LOG=debug python -m parakeet_dictation.main

# Run in background (production-style)
nohup PYTHONPATH=src python -m parakeet_dictation.main >/dev/null 2>&1 & disown

# Install dependencies
uv venv -p 3.12.5
uv add -r requirements.txt

# Install dev dependencies
pip install parakeet-dictation[dev]

# Lint
ruff check src/

# Type check
mypy src/

# Run tests
pytest

# Build for PyPI
python -m build
```

System deps: `brew install portaudio ffmpeg`

## Architecture

**macOS menu bar app** (`rumps`) with multi-threaded design:

- `WhisperDictationApp` (`main.py`) — the core class, extends `rumps.App`. Manages the full lifecycle: model loading, hotkey monitoring, audio recording, transcription, and text insertion.
- **Parakeet model** (`parakeet-mlx`): Speech-to-text. Model: `mlx-community/parakeet-tdt-0.6b-v2`. Loaded async on startup with a warm-up pass to trigger JIT compilation.
- **Hotkey system** (`pynput`): Two recording modes:
  - **Hold-to-record**: Ctrl+Alt+A via `GlobalHotKeys` chord detection + `Listener` for modifier release.
  - **Toggle-to-record**: § key press detected via the same `Listener`'s `on_press` handler.
- **Audio pipeline**: PyAudio callback stream → 16kHz mono int16 PCM, 512-byte chunks → temp WAV file → Parakeet transcription.
- **Text insertion**: Transcribed text is typed at the cursor via `pynput.keyboard.Controller`.
- `logger_config.py` — colored logging with `PARAKEET_LOG` env var control, `NO_COLOR` support.

## Key Constraints

- **macOS-only** — depends on rumps, PyObjC, Accessibility permissions
- **Python 3.12 strictly** (`>=3.12,<3.13`) due to numba/llvmlite compatibility
- **Apple Silicon optimized** — MLX model for ASR
- Parakeet model requires temp file for transcription (no BytesIO support)
- Requires macOS Accessibility and Microphone permissions

## Environment Variables

- `PARAKEET_LOG` — log level (debug, info, warning, error, critical; default: warning)
- `NO_COLOR` — disable colored log output

## Package Layout

```
src/parakeet_dictation/
├── main.py            # App entry point and WhisperDictationApp class
├── text_selection.py  # macOS text selection/replacement via clipboard (currently unused)
└── logger_config.py   # Colored logging setup
```

Entry point: `parakeet_dictation.main:main` (registered as `parakeet-dictation` CLI command).
