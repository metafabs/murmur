# Murmur

**Dictation that stays on your machine. The quiet, local alternative to cloud dictation.**

Murmur lets you hold a hotkey, speak, and get clean, filler-free text on your
clipboard — with transcription *and* AI cleanup running entirely on your own Mac.
No cloud, no subscription, no audio leaving your computer.

> A murmur is a quieter whisper. That's the idea: the privacy-first, local,
> free-as-in-freedom take on AI dictation.

---

## What Murmur does

```
hold hotkey -> record mic -> faster-whisper locally -> Ollama cleanup locally -> clipboard -> Cmd-V
```

- **Transcription:** [faster-whisper](https://github.com/SYSTRAN/faster-whisper), local.
- **Cleanup:** a local LLM via [Ollama](https://ollama.com), local.
- **Output:** cleaned text is copied to your clipboard. You paste it with Cmd-V.

The deliberate tradeoff: Murmur does **not** auto-type into every app. Manual paste
keeps the project small, debuggable, and less fragile.

## Requirements

- macOS, Apple Silicon recommended
- [Homebrew](https://brew.sh)
- [Ollama](https://ollama.com)
- Python **3.12 recommended**

Murmur works with modern Python 3 versions, but the recommended install pins the
virtual environment to `python3.12`. This avoids a common macOS problem where
`python3` later points to a different interpreter after a system or Homebrew update.

## Developer setup

Start from your home folder so the alias below works unchanged.

```bash
cd ~
git clone https://github.com/metafabs/murmur.git
cd murmur
```

Install system dependencies:

```bash
brew install portaudio
brew install python@3.12
```

Create a project-local Python environment with a specific interpreter:

```bash
python3.12 -m venv .venv
.venv/bin/python -m pip install --upgrade pip
.venv/bin/python -m pip install -r requirements.txt
```

Why this matters: always using `.venv/bin/python` means Murmur runs with the
same isolated Python environment every time instead of whatever `python3` happens
to mean on your Mac that day.

Install and start Ollama:

```bash
brew install --cask ollama
open -a Ollama
```

Then pull the cleanup model:

```bash
ollama pull llama3.1:8b
```

## Run

```bash
cd ~/murmur
.venv/bin/python murmur.py
```

Hold **Right Option**, speak, release. When Murmur says the text is on your
clipboard, paste with **Cmd-V**. Press **Esc** to quit.

On first launch, Murmur downloads the Whisper transcription model. macOS may ask
for privacy permissions. Grant your Terminal app:

- Microphone
- Input Monitoring
- Accessibility

Find these in **System Settings -> Privacy & Security**. After changing permissions,
quit Terminal and run Murmur again.

## Optional: launch with one word

```bash
echo 'alias murmur="cd ~/murmur && .venv/bin/python murmur.py"' >> ~/.zshrc
source ~/.zshrc
```

Now you can run:

```bash
murmur
```

This alias intentionally uses `.venv/bin/python`, not `python3`.

## Updating or rebuilding the Python environment

If Murmur stops working after a Python or Homebrew update, rebuild the local
environment with the pinned Python version:

```bash
cd ~/murmur
rm -rf .venv
brew install python@3.12
python3.12 -m venv .venv
.venv/bin/python -m pip install --upgrade pip
.venv/bin/python -m pip install -r requirements.txt
```

You do not need to download Ollama or the Ollama model again unless Ollama itself
was removed.

## Troubleshooting

### `zsh: command not found: ollama`

Ollama is not installed, or the command-line tool is not available.

```bash
brew install --cask ollama
open -a Ollama
which ollama
ollama --version
```

### `could not connect to ollama server`

Ollama is installed but not running.

```bash
open -a Ollama
```

Then retry:

```bash
ollama pull llama3.1:8b
```

### `llama-server binary not found`

The Homebrew Ollama install is probably broken. Do not try to compile Ollama with
`cmake` unless you intentionally want to debug Ollama internals. Reinstall it:

```bash
brew uninstall ollama
brew cleanup
brew install --cask ollama
open -a Ollama
ollama pull llama3.1:8b
```

### Wrong Python version

Check what Murmur is using:

```bash
cd ~/murmur
.venv/bin/python --version
.venv/bin/python -c "import sys; print(sys.executable)"
```

Expected: Python 3.12.x and a path ending in `~/murmur/.venv/bin/python`.

### Microphone or hotkey permissions

Go to **System Settings -> Privacy & Security** and grant your Terminal app:

- Microphone
- Input Monitoring
- Accessibility

Then quit and reopen Terminal.

## Configuration

Edit these constants near the top of `murmur.py`:

- `HOTKEY` — default is Right Option.
- `WHISPER_MODEL` — default is `small.en`.
- `WHISPER_COMPUTE` — default is `int8`.
- `OLLAMA_MODEL` — default is `llama3.1:8b`.
- `CLEANUP_PROMPT` — controls how aggressively Murmur cleans dictation.

## Build story

Murmur started as a weekend question: can a private version of a cloud dictation
app be small enough to understand, modify, and trust?

Full writeup: **[link to your Substack post]**

## Credits

Inspired by [Handy](https://github.com/cjpais/handy), an excellent open-source
local dictation app. Murmur is an independent Python project, not a fork.

## License

MIT (c) 2026 Fabien Hameline. Free to use, modify, and distribute.
