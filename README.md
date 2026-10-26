# Civic Ballot Assistant

**A nonpartisan AI-assisted ballot review workflow** — not a product, not a platform, and not an "agentic suite."
It's one skill (a set of instructions for an AI assistant) plus a few supporting docs and example scripts, for
working through a real ballot: one race or measure at a time, real sourced research on each, arguments for *and*
against, spoken aloud if you want, and nothing leaves your own machine.

It was built from an actual multi-hour session working through a real general-election ballot together at a
kitchen table. Everything specific to that session — who was in the room, what they believed, how they voted —
has been stripped out. What's left is the *process*, offered so other voters can use it too.

**Read this before anything else:** it worked well in that one real session, but it's unproven at scale — no
controlled study, no validation across multiple users or AI models. It's designed for nonpartisanship (sourced
claims, both-sides-by-default, never adopting a position), not proven nonpartisan — no AI assistant is perfectly
neutral, and this one hasn't been independently tested the way some funded civic-tech projects have (see "Where
this fits," below). And the depth of engagement you get out of it depends heavily on the depth you put in — the
workflow scaffolds a thorough pass through your ballot, it can't force one. A user who answers "yes to everything"
at every item gets a shallow pass through the same process.

## Why this exists

Most people don't have time to deeply research a ballot. A 2020 Cornerstone OnDemand/YouGov survey found that
**80% of US adults spend under two hours total** researching their entire ballot — which, nationally, can run
100+ measures — and **22% say they didn't feel they had enough information** when they voted. That's not a
judgment on anyone; it's a real constraint of modern life. This methodology doesn't fix the time problem by
itself, but it makes the time you *do* spend go much further — real primary-sourced research, both sides of every
contested question, delivered in a format you can listen to while doing something else, one decision at a time
instead of an overwhelming wall of text.

## Where this fits (and doesn't)

This isn't filling an empty niche — AI-assisted ballot research is an active, crowded space right now, this same
election cycle. A national survey found **13.9% of US adults already report using AI for voter information**, and
roughly half say they'd use a validated nonpartisan AI voter guide. There are serious, better-resourced
alternatives worth knowing about before you reach for this repo:

- **[VoteMate](https://impact.stanford.edu/index%2Ephp/organization/votemate)** (Stanford-partnered nonprofit,
  free, live in all 50 states) — enter your address, answer a short values questionnaire, get a personalized
  plain-language guide built on structured ballot data from Democracy Works, Ballotpedia, and Congress.gov. More
  complete, more polished, zero setup, and it covers jurisdictions this repo's OCR-and-search approach can't match
  for data quality. **If you just want fast, reliable answers, use that instead.**
- A Stanford/Ballotpedia-grounded voter chatbot has an actual published validation study behind it (N=2,474,
  measuring trust and accuracy across party lines) — a level of evidence this repo doesn't have.

**What this repo actually offers that those don't:** it's a *portable method*, not a hosted product — no account,
no third-party platform holding your ballot research, works with whatever AI assistant you already have, and the
`docs/methodology-notes.md` file explains *why* it's built the way it is so you can adapt or audit it rather than
trust a black box. That's a real, legitimate use case — but a narrower, more technical one ("I already use an AI
assistant and want full control over my own data") than "the best tool for the average voter," which is honestly
VoteMate or something like it.

## What this is *not*

- **Designed never to tell you how to vote** — not a guarantee that it never will. The instructions require it to
  surface facts, records, affiliations, funding sources, and documented arguments on both sides of every contested
  item, then ask what you think, rather than adopting its own position. But no AI assistant is perfectly neutral
  (see `docs/privacy-and-nonpartisanship.md` for the honest version of this). If you want it to apply a specific
  values framework, *you* supply that framework — the tool doesn't come with one built in.
- **Not "an agentic suite."** It's one skill plus some example scripts and docs — it directs how an AI assistant
  uses capability it already has (web search, reading/writing files), it doesn't add new autonomous capability,
  and nothing runs without you confirming each decision. That's intentional for something this consequential, but
  worth being precise about rather than overselling the engineering.
- **Not a data-collection product.** There's no server, no account, no analytics. It's a set of instructions for
  an AI assistant plus a local notes file. Your ballot choices live on your own disk, in a format you control, and
  nowhere else — not even a log this tool's author can see.
- **Not tied to any one AI model or company.** See "Model independence," below.

## How it works, in short

1. You photograph or type out your ballot and voter guide.
2. You and the assistant agree on some ground rules up front (see below) — how to handle disagreement if more
   than one person is deciding together, how fast to move, whether you want it read aloud.
3. The assistant works through your ballot **one item at a time** — never batching ahead — giving you real
   research (candidate records, funding sources, who's backing a measure and why, documented arguments on both
   sides, proactive flags for self-dealing or conflicts between different ballot measures) before asking for your
   decision.
4. Everything gets logged to a local tracker file as you go, so you can stop and resume, or come back to it next
   election with the same process.

## Model independence

This is written as **instructions a capable AI assistant can follow**, not code tied to one vendor. It was built
and tested in Claude Code, but nothing about the methodology requires Claude specifically. To use it with another
assistant:

- You need an assistant that can **browse/search the web** for current information (candidate records, ballot
  measure text, news coverage) — without that, it can only work from its training data, which will be stale for
  any live election.
- You need an assistant that can **read a local file** (your ballot photo, or OCR'd/typed-out text) and **write to
  a local file** (the tracker), or you can paste things back and forth manually if it can't.
- Give it the file `SKILL.md` in this repo as its instructions (paste it into the system prompt, a custom
  instructions field, or — if your assistant supports loadable skills/plugins the way Claude Code does — install
  it as one).
- Voice read-aloud is **entirely optional** and swappable — see `docs/voice-setup.md` for options ranging from
  free and local to paid and higher-quality. The methodology works fine as plain text-only chat if you skip this.

## Source independence

The methodology doesn't hardcode any particular country, state, or news outlet. "Good sources" for a ballot are
whatever your own jurisdiction's equivalent of a nonpartisan legislative analyst's office, an official voter
guide, and credible local/national journalism are — `SKILL.md` tells the assistant to find and cite real sources
for *your* ballot, not to rely on a fixed list built for somewhere else.

## Privacy

Read `docs/privacy-and-nonpartisanship.md` before you start. Short version: keep your tracker file in a folder you
don't sync to a public repo or share drive, and if you're using a cloud AI assistant, understand that your
conversation is processed by that provider the same way any other conversation with it would be — this
methodology doesn't change that, it just makes sure *the output* (your notes, your picks) stays local and under
your control.

## Getting started

1. Read `docs/privacy-and-nonpartisanship.md`.
2. (Optional) Set up voice — `docs/voice-setup.md`.
3. Copy `CONFIG.example.yaml` to `CONFIG.yaml` and fill in a couple of basics (where your tracker file should live,
   whether voice is on).
4. Give your assistant `SKILL.md` as its instructions, and your ballot photos/text.
5. Say something like: "Let's work through my ballot" — and go one item at a time.

## Files in this repo

- `README.md` — this file
- `SKILL.md` — the actual methodology, written as portable AI-assistant instructions
- `CONFIG.example.yaml` — a config template (copy to `CONFIG.yaml`, don't commit the real one if you fork this).
  Note: this is a reference your assistant reads and follows per `SKILL.md`'s instructions, not code that
  automatically enforces anything on its own.
- `docs/voice-setup.md` — optional read-aloud setup, multiple backends, free and paid
- `docs/privacy-and-nonpartisanship.md` — the guardrails, read this first
- `docs/methodology-notes.md` — deeper background on why the process is designed the way it is, optional read

## License / use

Take it, adapt it, use it for your own ballot, fork it, hand it to a friend. No attribution required, no warranty
offered — verify everything an AI assistant tells you against a primary source before you actually vote on it;
this tool is a research aid, not an authority.
