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
ONBOARDING.md        agent-facing setup coach (attached to a fresh chat by the adopter)
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
- ~~How do commits happen?~~ **Settled 2026-08-30: git is out of the adopter loop.**
  Claude cannot run it — the Filesystem connector is read/write/edit only, no shell,
  and its bash tool runs in Anthropic's sandbox rather than on the Mac — so the old
  commit protocol was fiction. Michael ruled out a launchd auto-commit agent: he does
  not want background processes running on relatives' machines that he'd be
  accountable for. Files are the record; `git init` stays in setup only so a manual
  snapshot is possible later. A double-clickable `commit.command` is the deferred
  fallback if the lack of undo ever actually bites.
- **Consequence to keep in mind: there is no undo.** The permission table auto-approves
  Write File and Edit File, and its old justification ("recoverable from git") is now
  false — both `SETUP.md` and `OPERATING.md` have been corrected. The append-only rule
  for dated entries is the real mitigation now, so treat it as load-bearing rather than
  stylistic.
- ~~First-session flow~~ verified 2026-08-30 against a pristine instance with a
  scripted adopter, and again after fixes. Cold-start detection, one-area discipline,
  the stall fallback, first-person voice, candid capture, and an unprompted honest
  checkpoint all hold. It took four runs; see the failure pattern below.
- **Still untested:** the iCloud inbox round-trip (needs the phone side set up).
- ~~`ONBOARDING.md` distribution~~ — settled with the publish decision. The raw GitHub
  URL works: adopters paste one line into a fresh chat and Claude fetches it. The whole
  handoff is now "install Claude Desktop, paste this prompt", with the paste text living
  in `README.md` so only the repo link needs sending.
- **Adopter feedback path is `feedback.md`** (gitignored, created from `templates/`).
  Claude appends dated mechanical entries when the *system* misbehaves; the adopter
  pastes them to Michael by hand. Deliberately manual: no network path out of a
  relative's machine, and they see everything before it's sent. `OPERATING.md` forbids
  any personal content in it — this file is the one thing that routinely travels from
  an adopter to a third party, so treat that rule as load-bearing.
- **The characteristic failure of this system is a confident false positive**, not an
  error. Every serious bug found on 2026-08-30 looked like success: the sandbox offering
  to let you "upload" your own folder; "Created 2 memories" while nothing hit disk; a
  checkpoint listing tidy structural files while the honest material stayed in the chat;
  and a beautifully rendered document titled "Reflections — 2026-08" standing in for a
  file that was never written. **Defences that work check the disk; defences that trust
  the report do not.** Hence the read-back rule for candid writes, and the "say hi and
  see if it checks your inbox" verification in setup. Any new rule should be judged by
  whether it would catch a plausible-looking non-event.
- **A concrete script beats a conditional rule.** Twice, a rule failed because the doc
  spelled out exactly what to say for one case and left the other as an abstract
  condition — the script won every time. When a behaviour matters, write the words. The
  corollary bit too: scripting the *sentence* while leaving the *write* abstract made
  Claude produce the sentence and skip the file. Script the action, gate the speech on it.
- **Never add a second allowed directory to the connector.** With `constellation-live`
  and `constellation-test` both attached, Claude answered questions about "your files"
  by blending the two — reporting no journal (true of one) and no areas (true of the
  other) in the same breath, and taking the wrong branch as a result. `SETUP.md` already
  says pick only that folder for privacy; it matters for correctness too. Swap
  directories when testing, never stack them.
- **Project setup has a trap worth re-checking after Desktop updates.** The New Project
  dialog's "What are you trying to achieve?" is a description, not instructions; the
  boot prompt must go in the **Instructions** panel on the project page. Wrong box fails
  silently — the project simply behaves like ordinary Claude.
- **Keep `ONBOARDING.md`'s failure table current.** It's the only place the silent
  failure signatures are written down, and each one cost real debugging time to find.
  A new Desktop release that changes a symptom makes that table wrong, not just stale.
- **Open question — Node.js.** The one-click connector appears to bundle its own
  runtime, but this was only tested on a machine that already had Node v26. Whether it
  works on a Node-less Mac is unverified, and that's every relative's Mac. Confirm on a
  clean machine before onboarding anyone.
- ~~Publish decision~~ — settled 2026-08-30: **public repo** at
  `github.com/crentist-my-dentist/constellation`. Started private with Read
  collaborators, then went public because private forced every adopter through a GitHub
  account, an invite, a git install and auth before Claude had anything to read. Nothing
  private is in here — `events/` and `candid/` are gitignored and never were. Adopters
  now need no GitHub account at all: ZIP download or `git clone`, both unauthenticated.
  **Before publishing, history was rewritten** to replace two personal email addresses
  with the GitHub `noreply` address, and the repo was deleted and recreated rather than
  force-pushed — a force-push leaves the old commits reachable by SHA on a public repo,
  which was verified and then removed. Keep commits on the `noreply` identity.
- Optional add-on: read-only "advice-on-the-go" phone path (events surfaced via a
  Project/connector).
- Design rationale that predates this repo lives in the Career project's memory
  (`constellation-spinoff.md`); as dev continues here, capture new decisions in this
  repo (a `decisions`/changelog doc or this file).
