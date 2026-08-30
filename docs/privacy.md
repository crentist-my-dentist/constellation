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

## How the boundary is actually enforced

Three settings do the real work here. If they're wrong, everything above is just
intention:

1. **The connector's allowed directory.** The Filesystem connector is scoped to your
   `constellation` folder and nothing else, so Claude can't read the rest of your Mac.
2. **"Copy file to Claude" is denied.** That tool copies a file *out* of your folder
   into Claude's own storage. Claude reading a candid file sends its text to Anthropic
   for that one request (see above) — copying it leaves a durable copy on their side.
   Those are different exposures, and only the second one is avoidable. So it's denied.
3. **Claude's built-in memory is turned off.** Claude Desktop can generate its own
   memories from your chats, including a setting specifically for sensitive topics.
   Left on, honest reflection you meant for `candid/` lands in a permanent cloud store
   instead of a local file — and you won't be told. Both toggles go off in setup.

These are set once during setup ([SETUP.md](../SETUP.md) steps 4 and 5) and are worth
re-checking after a Claude Desktop update, since defaults can come back.

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
