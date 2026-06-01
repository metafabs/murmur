# Murmur

**Dictation that stays on your machine. The quiet, local alternative to cloud dictation.**

Murmur lets you hold a hotkey, speak, and get clean, filler-free text on your
clipboard — with transcription *and* AI cleanup running entirely on your own Mac.
No cloud, no subscription, no audio leaving your computer.

> A murmur is a quieter whisper. That's the idea: the privacy-first, local,
> free-as-in-freedom take on AI dictation.

---

## Why this exists

Cloud dictation apps are excellent but they send your voice to someone else's
servers and charge a monthly fee. Murmur does the two things those apps do —
transcribe speech and clean it up (remove the "um"s, false starts, fix grammar) —
but **100% locally**:

- **Private** — audio and text never leave your machine.
- **Free** — open source, MIT licensed.
- **Works in any app** — the cleaned text lands on your clipboard; paste with Cmd-V.

It's intentionally simple. The one tradeoff (manual paste instead of
auto-insert-anywhere) is what keeps it a small, hackable, ~220-line script
instead of a sprawling project.

## How it works

```
hold hotkey -> record mic -> faster-whisper (local) -> LLM cleanup (local) -> clipboard -> Cmd-V
```

- **Transcription:** [faster-whisper](https://github.com/SYSTRAN/faster-whisper), local.
- **Cleanup:** a local LLM via [Ollama](https://ollama.com) — removes fillers,
  fixes grammar, keeps your meaning. This is the part you'd otherwise pay for.

> **Models are not bundled.** You install Ollama and pull a model yourself;
> Whisper downloads its model on first run. Murmur ships only the glue code.

## Requirements

- macOS (Apple Silicon recommended)
- Python 3.10+
- [Homebrew](https://brew.sh), [Ollama](https://ollama.com)

## Setup

```bash
# system audio lib
brew install portaudio

# python env — use a 3.10+ interpreter explicitly.
# macOS ships an old system python3 (3.9); if `python3 --version` shows 3.9,
# install a newer one (`brew install python@3.12`) and use it by name:
python3.12 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# local cleanup model (~5 GB, one-time)
ollama pull llama3.1:8b
```

## Run

```bash
source .venv/bin/activate
python3 murmur.py
```

**Hold Right-Option, speak, release. Press Cmd-V to paste.** Esc to quit.

First run downloads the Whisper model and asks for **Microphone** + **Input
Monitoring** and **Accessibility** permissions (System Settings ->
Privacy & Security). Grant them, then quit and rerun once.

### Optional: launch with a one-word command

Tired of typing the two startup lines each time? Add a shell alias so you can
just type `murmur` from anywhere. For zsh (the macOS default):

```bash
echo 'alias murmur="cd ~/murmur && source .venv/bin/activate && python3 murmur.py"' >> ~/.zshrc
source ~/.zshrc
```

Now just run:

```bash
murmur
```

(Adjust the path if you cloned Murmur somewhere other than `~/murmur`. Note the
alias uses `python3`, not `python` — macOS venvs expose only `python3`.)

## Tuning

Two knobs at the top of `murmur.py`:
- **`CLEANUP_PROMPT`** — the heart of the tool. Adjust how aggressively it edits.
- **`WHISPER_MODEL`** — `small.en` (default) balances speed and accuracy.

Want better cleanup or lower latency? Swap the local model for a cloud API —
see the **ESCAPE HATCH** comment inside the `cleanup()` function. ~5-line change.

## The build story

Murmur started as a "can I build the private version of a cloud dictation app in
a weekend?" experiment. The interesting part wasn't the AI — it was the scoping
decision that kept it small. Full writeup: **[link to your Substack post]**

## Credits

Inspired by [Handy](https://github.com/cjpais/handy), an excellent open-source
local dictation app. Murmur is an independent Python project, not a fork.

## License

MIT (c) 2026 Fabien Hameline. Free to use, modify, and distribute.
