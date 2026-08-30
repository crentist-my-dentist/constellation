# Developing Constellation

This file orients Claude Code when **developing Constellation itself**. It is *not*
the runtime rulebook — that's `OPERATING.md`, which ships to end users and is loaded
(via a boot prompt) inside the Claude Desktop app when someone actually *uses* the
system. Keep the two straight:

- **`OPERATING.md`** = the product. The rules Claude follows when a relative is
  living in their own Constellation. Edit it with care; it's user-facing.
- **`CLAUDE.md`** (this file) = the workshop. How we build and maintain the template.

## What this repo is

Constellation is a **shareable template** for a private, goal-driven life system that
someone runs with Claude on a Mac. It generalizes a career-coaching setup into a
**multi-north-star** system: each life area (friendship, school, health, craft,
career) is a star; together they form the user's constellation. You talk; Claude
maintains a small set of Markdown files that become durable memory.

This repo is the thing a relative clones/instantiates. Their *actual data* is not
here — see the two-track model below.

## Architecture (the load-bearing decisions)

- **Runtime = free Claude Desktop app + a local filesystem MCP connector.** Verified
  working on a real free account (2026-08-30): `+ > Add connector > Browse connectors
  > Filesystem`, scoped to one directory. Claude Code's `Select folder…` and Cowork
  are both **paid-only** — do not rewrite `SETUP.md` around either, however much nicer
  they look. Desktop does **not** auto-load rules, so each session starts with a **boot
  prompt** that tells Claude to read `OPERATING.md`.
- **Built-in memory is the framework's natural enemy.** Claude Desktop generates its
  own memories from chats, on by default. Left on, Claude "remembers" instead of
  writing files — it reports "Created 2 memories" and nothing hits disk, so the user's
  journal stays empty while everything looks fine. Defended twice: a SETUP step that
  turns both toggles off, and a rule in `OPERATING.md`. Re-check this after Desktop
  releases; a default flipping back would silently break every install.
- **Phone = capture only.** iOS can't run the local connector, so the phone writes to
  `inbox.md` (in iCloud); the Mac session ingests + clears it. Mirrors a hot→cold
  capture model.
- **Two-track privacy split:**
  - `events/` — facts (journal, wins, goals, north stars). May push to a *private*
    remote; may be made phone-reachable.
  - `candid/` — honest reads (feelings, people, decisions). Committed **locally only**,
    never a remote, never iCloud.
- **Three git repos, separated by `.gitignore`:** this framework/template repo ignores
  `events/` and `candid/`, which become the *user's own* repos at setup. Template
  content the user copies lives in `templates/`; a filled-in `examples/friendship/`
  shows "good."

## Conventions when editing

- **Privacy honesty is non-negotiable.** Never let the docs imply "local = hidden from
  Anthropic." `docs/privacy.md` states the real deal: local-only avoids a persistent
  third-party copy and gives timing control, but anything Claude reads is sent to
  Anthropic for that request. Preserve that candor.
- **Write for a non-technical reader** in README/SETUP/OPERATING. The maintainer
  (Michael) can be assumed to handle the technical setup for relatives, but the prose
  itself shouldn't require that.
- **Keep the advice-loop quality bar** in `OPERATING.md`: options not one answer;
  aligned / reality-based / integrity-preserving / optionality-protecting / honest
  about cost; candor over agreeableness.
- **The diagram** in `docs/workflow.md` is the source of truth for the flow; keep it
  and the prose in sync if the architecture changes.

## Layout

```
README.md            what it is + start-here
OPERATING.md         shipped runtime rules (the product)
SETUP.md             one-time setup: Desktop, connector, inbox, git
CLAUDE.md            this file — dev orientation
connector-config.example.json
docs/workflow.md     the mermaid flow diagram
docs/privacy.md      the honest privacy account
templates/           what a user copies to start (events/, candid/, inbox.md)
examples/friendship/ a filled-in area
```

## Commit hygiene

Author is Michael. Commit after meaningful changes with a short descriptive message.
End messages with the Co-Authored-By trailer. The remote is
`github.com/crentist-my-dentist/constellation` — **private**. Adopters get **Read**
access so a stray push from their clone can't reach the framework.

## Maintainer's own setup (dev vs. live)

Michael is both maintainer and adopter, so there are two clones:

- **`~/constellation`** — the dev clone. Write access to the remote. Framework edits
  and Claude Code sessions happen here. It has no `events/`/`candid/` of its own.
- **`~/constellation-live`** — his personal instance, cloned from the remote exactly
  as an adopter's is, with real `events/` and `candid/`. The Desktop connector points
  *here*, not at the dev clone.

Kept separate for two reasons: personal `candid/` content shouldn't sit in the working
directory of dev sessions, and living in a normal adopter clone means Michael feels a
broken `git pull` or a bad `OPERATING.md` change before a relative does.

**File-layout changes are migrations.** A pull updates an adopter's *rules* but not
their *data* — rename `events/wins.md` or restructure `events/areas/<name>/` and
`OPERATING.md` starts pointing at paths their files don't have. Keep layout changes
backward-compatible, or ship migration notes with them.

## Notes / open threads

- ~~Live setup test — git half~~ (2026-08-30): a full rehearsal (clone → copy
  templates → `git init` both data repos → write data → pull an upstream update)
  confirmed nothing leaks into the framework repo and updates land without touching
  adopter data.
- ~~Live setup test — connector half~~ (2026-08-30): Filesystem connector installs and
  reads/writes on a **free** account. Two gotchas found and documented: it doesn't
  start until the app is fully quit (⌘Q) and reopened — and the failure mode is Claude
  silently answering from its server-side sandbox, offering to let you "upload" the
  folder — and built-in memory hijacks the write path (see above).
- ~~Routing table~~ verified end-to-end 2026-08-30 against a real session: a run went
  to `events/journal/`, a read on a manager to `candid/people/_map.md`, and the user's
  own take to `candid/reflections/` — right tracks, dated, paraphrased not fabricated.
  The advice loop held its quality bar unprompted (options with costs, a real
  recommendation, no cheerleading).
- **DECISION NEEDED — how do commits happen?** Found 2026-08-30: **Claude cannot run
  git.** The Filesystem connector is read/write/edit only, no shell, and its bash tool
  runs in Anthropic's sandbox, not on the Mac. `OPERATING.md`'s commit protocol was
  therefore fiction; it now tells Claude it can't commit and must not offer terminal
  commands. But that leaves nothing advancing history for an adopter. Options: (a) drop
  git from the adopter experience entirely — files only; (b) a launchd agent that
  auto-commits both repos on a timer (never pushes `candid/`) — the only option that
  asks nothing of a non-technical user; (c) a double-clickable `commit.command`. (b) is
  the recommendation. Until this is decided, adopters get files but no history.
- **Still untested:** the iCloud inbox round-trip.
- **Open question — Node.js.** The one-click connector appears to bundle its own
  runtime, but this was only tested on a machine that already had Node v26. Whether it
  works on a Node-less Mac is unverified, and that's every relative's Mac. Confirm on a
  clean machine before onboarding anyone.
- ~~Publish decision~~ — settled 2026-08-30: private repo on Michael's personal
  GitHub account, adopters added as Read collaborators. GitHub Free covers this for
  everyone (unlimited private repos + collaborators); a free org with teams is the
  fallback if the group outgrows a collaborator list. Branch protection on private
  repos would need the paid Team plan — not needed here.
- Optional add-on: read-only "advice-on-the-go" phone path (events surfaced via a
  Project/connector).
- Design rationale that predates this repo lives in the Career project's memory
  (`constellation-spinoff.md`); as dev continues here, capture new decisions in this
  repo (a `decisions`/changelog doc or this file).
