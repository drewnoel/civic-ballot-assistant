# Voice setup (optional)

The methodology works fine as plain text chat. Voice read-aloud is a nice-to-have for going through a long ballot
hands-free or at a table with more than one person — not a requirement. Pick whichever of these fits your budget
and platform; none of them are required by `SKILL.md`, which just says "if voice is enabled, speak the item's
exact text."

## Option 1 — macOS built-in `say` (free, fully local, no account)

If you're on a Mac, this needs nothing beyond what's already installed.

```bash
say "This is a test."
```

To see available voices: `say -v '?'`. British English voices tend to read as more "narration-friendly" for long
passages than the US default; pick whatever you like.

Minimal script to speak arbitrary text, reading from a file to avoid shell-escaping problems with quotes/special
characters:

```bash
#!/usr/bin/env bash
# usage: ./speak-say.sh "text to speak"
echo "$1" > /tmp/speak-input.txt
say -f /tmp/speak-input.txt -v Samantha -r 180
rm -f /tmp/speak-input.txt
```

Pros: free, private (nothing leaves your machine), no setup. Cons: robotic-sounding compared to the paid options
below.

## Option 2 — A cloud TTS API (ElevenLabs, OpenAI TTS, Azure, Google, etc.)

Any of these will sound dramatically more natural than `say`. They all work the same way at a high level: you
send text to an HTTP API with your key, you get back an audio file, you play it. Example using ElevenLabs (swap
the URL/auth/response-handling for whichever provider you pick — they're all similar REST APIs):

```python
#!/usr/bin/env python3
import sys, subprocess, tempfile, urllib.request, json, os

API_KEY = os.environ.get("TTS_API_KEY")  # never hardcode a key in a script you might share
VOICE_ID = "your-chosen-voice-id"

def speak(text):
    payload = json.dumps({"text": text, "model_id": "eleven_multilingual_v2"}).encode()
    req = urllib.request.Request(
        f"https://api.elevenlabs.io/v1/text-to-speech/{VOICE_ID}",
        data=payload,
        headers={"xi-api-key": API_KEY, "Content-Type": "application/json", "Accept": "audio/mpeg"},
        method="POST",
    )
    with urllib.request.urlopen(req, timeout=60) as resp:
        audio = resp.read()
    with tempfile.NamedTemporaryFile(suffix=".mp3", delete=False) as tf:
        tf.write(audio)
        path = tf.name
    subprocess.run(["afplay", path], check=False)  # macOS; use `mpg123`/`ffplay`/etc. on Linux
    os.unlink(path)

if __name__ == "__main__":
    speak(sys.argv[1])
```

**Store your API key in your OS's secret manager (macOS Keychain, a password manager, an environment variable
you don't commit to any repo) — never paste it directly into a script or a chat.** On macOS:

```bash
# store it once:
security add-generic-password -a "$USER" -s my-tts-api-key -w
# (this will prompt you interactively — or use a GUI password dialog, see below, to avoid
#  ever typing a secret into a terminal command line where it could end up in shell history)

# retrieve it at runtime:
TTS_API_KEY=$(security find-generic-password -a "$USER" -s my-tts-api-key -w)
```

If you're pasting a key into any kind of terminal command, **don't type it directly into the command line** —
that can end up in your shell history or, if you're working with an AI assistant in a terminal, in that session's
own saved transcript. A safer pattern on macOS is a native password dialog that never touches the terminal:

```bash
key=$(osascript -e 'text returned of (display dialog "API key:" default answer "" with hidden answer)')
security add-generic-password -a "$USER" -s my-tts-api-key -w "$key"
unset key
```

## Avoiding overlapping/garbled audio

If your assistant might trigger more than one speech request close together (e.g. it reads something, then you
ask a follow-up before the first one finishes), two audio clips can start playing on top of each other. A simple
fix: use a lock file so only one plays at a time, and anything else waits its turn.

```python
import os, time

LOCK_DIR = "/tmp/civic-ballot-speak.lockdir"

def acquire_lock(timeout=120):
    start = time.time()
    while True:
        try:
            os.mkdir(LOCK_DIR)
            return True
        except FileExistsError:
            if time.time() - start > timeout:
                return False
            time.sleep(0.3)

def release_lock():
    try:
        os.rmdir(LOCK_DIR)
    except OSError:
        pass
```

Wrap your actual speech-playing call with `acquire_lock()` / `release_lock()` and you'll never get two things
talking over each other.

## A gotcha worth knowing about

If your assistant's "read the last thing I said" mechanism works by re-reading its own conversation transcript
rather than being handed exact text directly, it can end up reading the *wrong* thing if anything else gets
written in between — a status update, an aside, anything. Prefer passing the exact text you want spoken directly
to your TTS script/function rather than relying on "whatever was said most recently" (`SKILL.md` §5 already
accounts for this).
