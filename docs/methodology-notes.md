# Methodology notes — why it's built this way

Optional reading. These are the design lessons the core `SKILL.md` instructions are built from, drawn from an
actual multi-hour working session on a real ballot. Nothing here references what was actually decided in that
session — just what worked and what didn't, as a process.

## Why strictly one item at a time

The single biggest failure mode in an early, less disciplined version of this process was trying to summarize
multiple races or measures in one response. It reads as faster, but it's actually slower in practice — people
lose track of which item they're deciding, context from three items back gets forgotten, and the temptation to
rubber-stamp a batch of "boring" items (down-ballot judicial seats, minor local boards) creeps in. Ballots are
long specifically in the places most voters already skip — forcing one-at-a-time pacing is what makes it possible
to actually give those items real attention instead of defaulting to "probably fine, next."

## Why restate context every single item

Chat interfaces scroll. A live conversation — especially one with research-heavy replies and a back-and-forth
with more than one person — accumulates length fast. Making the assistant restate "here's what we're deciding and
why it matters" at the top of every item, every time, costs almost nothing and saves a lot of "wait, which race
is this again?" friction.

## Why proactively flag conflicts of interest and measure-vs-measure interactions

Real ballots sometimes contain a measure that quietly nullifies or weakens another measure depending on relative
vote totals — a mechanism most voters have no way to notice just reading ballot text, because it's usually buried
in cross-referenced legal language, not the plain-English summary. Same with funder self-dealing: a measure whose
own financial backers stand to benefit directly from its passage. These are exactly the kind of thing a quick skim
misses and a few extra minutes of research catches — worth building into the default research standard rather
than relying on the user to think to ask.

## Why capture "standing values" instead of re-deriving them every time

People don't hand over their entire values framework in one tidy paragraph at the start — it comes out naturally,
in pieces, as specific items trigger specific reactions ("I care about X" surfacing at item 12, not item 1).
The fix is simple: the moment something like that gets said, write it down as a standing instruction and actually
apply it to every later relevant item without making the person repeat themselves. The alternative — the
assistant quietly forgetting by item 30 what was said at item 12 — is a real failure mode worth designing against
explicitly, not something to leave to chance.

## Why voice is optional, not core

A long ballot read purely as text is a wall of reading for anyone, let alone two people trying to discuss it
together. Having items read aloud turns it into something closer to a conversation at a table than solo homework
— genuinely changes the experience for the better when more than one person is deciding together. But it's not
load-bearing to the methodology itself, and it adds either a paid API dependency or at minimum extra setup, so
it's built as a clearly optional layer rather than a requirement, with a free/local fallback documented.

## Why the closing synthesis is opt-in and never offered unprompted

It's genuinely interesting to see a pattern reflected back after a long session of real decisions — but it's also
exactly the kind of thing that can feel presumptuous or reductive if offered without being asked, especially on
a topic as personal as political identity. The instruction is explicit: only build this if asked, build it from
the actual recorded decisions rather than assumptions, and treat the first attempt as a draft the person can push
back on and refine — not a verdict to defend.
