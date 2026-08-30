# Privacy — the honest version

Please read this before trusting the system with anything sensitive. It does not
promise more than it can deliver.

## The one thing to understand

**Claude runs in the cloud.** Any file Claude reads — whether it lives on your Mac or
in a private repo — is sent to Anthropic's servers to be processed, for that request.
Keeping a file "local" does **not** hide its contents from Anthropic the moment you
ask Claude about it.

So "local-only" is not about hiding candid data from Anthropic. It's about something
narrower and still worth having.

## What "local-only" for `candid/` actually buys you

1. **No second, permanent copy off your machine.** A private repo (GitHub, etc.) or
   iCloud keeps a durable copy on a company's servers 24/7 — independent of whether
   Claude ever reads it. Local-only means the one lasting copy sits on hardware you
   physically control. Anthropic sees the text only *transiently*, when you invoke it.
2. **You control the timing.** A candid entry reaches Anthropic *only if and when you
   choose to bring it into a conversation.* Something you write but never discuss
   never leaves the Mac. Push it to a cloud and it's uploaded regardless.
3. **Fewer parties, smaller standing surface.** Local-only = your Mac + Anthropic
   when invoked. A cloud copy = your Mac + that company (always) + Anthropic (when
   invoked).

## Where the line is drawn in this system

- **`events/` (facts):** fine to back up to a *private* repo, and fine to make
  phone-reachable. Low sensitivity.
- **`candid/` (feelings, people reads, hard forks):** committed to git **locally
  only**. No remote, ever. Never written to `inbox.md` or iCloud.
- **`inbox.md`:** lives in iCloud for phone capture, so **facts only** go there.

## If even transient processing is too much

If a piece of content is so sensitive that you don't want Anthropic to process it
*at all*, then **Claude is the wrong tool for it** — local files don't change that,
because using Claude means sending the text to the cloud. Your options for that tier:

- Keep it in a plain note Claude never reads, or
- Process it with a fully **local, on-device model** (e.g. Ollama on the Mac), which
  never sends anything out.

## The cost of local-only (name it honestly)

Anything kept local-only has **no off-machine backup**. If the Mac dies, that candid
history is gone. If that risk matters more to you than the exposure, you can choose to
back candid up to a *private, access-controlled* repo — knowingly accepting the
second cloud copy. That's a personal call. The default here is local-only because for
most people the privacy of honest reflection outweighs the backup risk — but it is a
choice, not a law.
