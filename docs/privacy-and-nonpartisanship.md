# Privacy and nonpartisanship — read this first

## Privacy: what's actually local, and what isn't

Be honest with yourself about this distinction:

- **The tracker file** — your notes, your picks, your reasoning — is just a plain text file on your own disk.
  Nothing about this methodology sends it anywhere. Keep it in a folder that **isn't synced to a cloud drive you
  don't fully trust, isn't inside a git repo you might push publicly, and isn't shared with anyone you don't want
  reading your ballot choices.**
- **The conversation itself**, if you're using a cloud-hosted AI assistant (Claude, ChatGPT, Gemini, or anything
  similar), is processed by that provider the same way any other conversation with it is. This methodology
  doesn't change that, and nothing here can promise otherwise. If true end-to-end privacy of the conversation
  itself matters to you — not just the saved output — look into running a local, offline LLM instead of a cloud
  assistant; that's outside the scope of what this repo sets up, but it's compatible with `SKILL.md`'s
  instructions if your local model can browse the web and read/write files.
- **Web searches** your assistant runs to research candidates and measures go to whatever search provider it
  uses, same as any other search you'd do yourself.
- If you enable voice and use a cloud TTS API (see `docs/voice-setup.md`), the text you're having read aloud is
  sent to that TTS provider too. The free, fully-local `say` option (macOS) avoids this entirely if it matters to
  you.

**The honest summary: this methodology keeps your *output* — your actual recorded decisions — local and under
your control. It doesn't make a cloud AI conversation itself private in some way a normal conversation with that
assistant wouldn't already be.**

## An optional extra guardrail: keep ballot files out of the wrong folder automatically

If you're using an AI coding/agent tool that supports pre-action hooks (Claude Code does; check your own tool's
docs for an equivalent), you can set up a simple mechanical check that catches it if your assistant (or you,
absent-mindedly) tries to write ballot-related notes somewhere other than your designated private folder — a
backstop beyond just remembering to be careful. Here's a minimal example for Claude Code
(`~/.claude/hooks/ballot-privacy-check.py`, wired up as a `PreToolUse` hook on `Write`/`Edit` in your settings):

```python
#!/usr/bin/env python3
import sys, json, re

# Adjust the keywords and your designated private-folder marker to your own setup.
KEYWORDS = re.compile(r"ballot|election|voter-?guide|proposition", re.IGNORECASE)
PRIVATE_FOLDER_MARKER = "ballot-notes"  # change to match CONFIG.yaml's tracker_path folder

def main():
    try:
        payload = json.load(sys.stdin)
    except Exception:
        return
    file_path = payload.get("tool_input", {}).get("file_path", "")
    if file_path and KEYWORDS.search(file_path) and PRIVATE_FOLDER_MARKER not in file_path:
        print(json.dumps({
            "hookSpecificOutput": {
                "hookEventName": "PreToolUse",
                "permissionDecision": "ask",
                "permissionDecisionReason": f"'{file_path}' looks ballot-related but isn't in your "
                                             "designated private folder. Confirm this is intentional.",
            }
        }))

if __name__ == "__main__":
    main()
```

This is optional and specific to tools that support this kind of hook. The simpler, universal version of this
same guardrail is just: **pick a folder, keep your ballot notes there, and don't put that folder in anything you
sync or share.**

## Nonpartisanship — what to expect from the assistant, and what to watch for

`SKILL.md` instructs the assistant to:
- Never adopt or advocate a political position of its own.
- Present documented arguments from multiple sides of every contested item.
- Apply *your* stated values framework, if you give it one — never invent one on your behalf, and never
  substitute its own judgment for yours.
- Cite real sources, flag when sourcing is weak, and say plainly when it couldn't find something.

No AI assistant is perfectly neutral — all of them have some bias baked in from their training, same as any news
outlet, any encyclopedia, any individual journalist does. The way this methodology manages that isn't by claiming
perfect neutrality is achievable, it's by:
1. Requiring sourced claims you can independently verify, not just the assistant's unsupported assertion.
2. Explicitly asking it to present *both* sides of contested questions, not just the side it might otherwise lean
   toward.
3. Keeping you, the human, as the actual decision-maker on every single item — the assistant's job is to inform
   that decision, never to make it for you.

If at any point it feels like you're being nudged toward a conclusion rather than given the facts to reach your
own — push back on that in the moment, the same way you would with a human advisor. That's a feature of talking
to it like a collaborator, not a flaw in the methodology.
