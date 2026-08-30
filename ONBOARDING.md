# Onboarding guide — *for Claude, not for the user*

**You are being handed this file by someone who is about to set up Constellation on
their Mac.** They are not a developer. They may never have opened Terminal. Your job
is to walk them through setup one step at a time, patiently, and to diagnose what goes
wrong — because several things reliably do, and every one of them fails *silently* with
a plausible wrong explanation.

This file is not `OPERATING.md`. `OPERATING.md` is the rulebook for running the system
day to day, and you will not have access to it until setup succeeds. Right now you have
no access to their files at all. That's expected — creating that access is the goal.

## How to behave

- **One step at a time.** Give a single step, then stop and wait. Never paste the whole
  guide at them.
- **Ask for a screenshot at every checkpoint.** You can read images. A screenshot of
  their actual screen beats any description they can give you, and they will often
  describe the wrong thing. Say "send me a screenshot of that window" freely.
- **Exact clicks and exact commands.** Not "open settings and find the connector" but
  "click the **+** at the bottom left of the message box, then **Add connector**."
  Terminal commands go in a code block, complete, ready to paste.
- **Never assume they know a term.** Not "clone the repo," but "copy the folder."
- **Verify before advancing.** Every step below has a check. Don't move on until it
  passes. A step that silently didn't work is the whole problem here.
- **Their username.** Ask early: *"Open Terminal and paste `whoami`, then tell me what
  it says."* Use that everywhere a path appears, so every command you give is
  copy-pasteable rather than a template they have to edit.
- **Pin down one folder path and reuse it forever.** Once they've chosen where the
  folder lives (step 2), that absolute path goes into *everything* after it: the
  connector's allowed directory, the boot prompt, every command. Most failures
  downstream are really a path mismatch.

## The setup, step by step

Work from `SETUP.md` in the folder they were given — it is the source of truth, and
this file is the coaching layer over it. In order:

1. **Get the framework onto their Mac.** It lives at
   <https://github.com/crentist-my-dentist/constellation>. Prefer the first option —
   it needs no GitHub account and no git:
   - **Download ZIP**: green **Code** button → **Download ZIP** → double-click to
     unzip → rename the unzipped `constellation-main` folder to `constellation`.
   - **Clone** (only if they already have git):
     `git clone https://github.com/crentist-my-dentist/constellation.git` — worth it if
     they're comfortable, because later updates become one `git pull`. With the ZIP,
     updating means downloading again.
2. **Ask where they want the folder to live**, then check that the choice is safe.
   Don't assume — some people keep everything in Documents, some have a filing system
   they care about. Offer the default and let them redirect you:

   > *"I'd suggest putting it straight in your home folder, so it ends up at
   > `/Users/NAME/constellation`. Anywhere else you'd rather keep it?"*

   **Then apply this rule, which they cannot check just by looking:** the folder must
   not sit inside iCloud Drive. iCloud syncing a live git repo can corrupt it and leave
   duplicate "file 2.md" copies.

   The trap: on many Macs **Desktop and Documents *are* iCloud folders**, because
   "Desktop & Documents Folders" sync is switched on. So if they choose either, have
   them check **System Settings → Apple Account → iCloud → iCloud Drive → Desktop &
   Documents Folders**. If it's on, those locations are out — steer them to the home
   folder or another local spot.

   If they're unsure, have them run `pwd` in Terminal from inside the folder and send
   you the result. A path starting `/Users/NAME/Library/Mobile Documents/` is iCloud.

   **Confirm the final absolute path back to them, and use it for the rest of setup.**
3. **Create their two data folders** from `templates/` (`events/`, `candid/`,
   `inbox.md`, and `feedback.md`).
4. **Turn `events/` and `candid/` into git repos.** Tell them plainly that they will
   not touch git again — it's there only so a snapshot is possible later.
5. **Install Claude Desktop, sign in** (free plan is fine), **and add the Filesystem
   connector**: `+` → **Add connector** → **Browse connectors** → search **Filesystem**
   → **Add directory** → choose *only* their `constellation` folder. Then set tool
   permissions per the table in `SETUP.md` — reads Allow, Write/Edit/Create Directory
   Allow, Move File ask, **Copy file to Claude Deny**. Then **Save**.
6. **Have them fully quit Claude Desktop (⌘Q) and reopen it.** Not close the window —
   quit. The connector does not start until the app restarts.
7. **Turn off built-in memory**: Settings → Memory → switch off *Generate memory from
   chats* **and** *Include sensitive topics in memory*.
8. **Set up the iCloud inbox** for phone capture.
9. **Create a Project** called Constellation with the boot prompt as its custom
   instructions, so the rules load automatically every session.

## Known failure modes — check these before theorising

These are real, all observed. Match the symptom, apply the fix.

| Symptom | What's actually wrong | Fix |
|---|---|---|
| Claude says the folder doesn't exist, offers to let them **upload** it, or mentions "your uploads" / "my sandbox" / "Ran a command" | It's answering from the server-side sandbox, not their Mac. The connector isn't attached or didn't start. | Fully quit with **⌘Q** and reopen. Then retry with the absolute path. |
| Same symptom, and they already restarted | The prompt was too vague. "My constellation folder" often fails. | Have them say: *"Using the Filesystem tools, list the files in /Users/NAME/constellation."* The absolute path **and** naming the tools both matter. |
| Reply says **"Created N memories"** and no file appears | Built-in memory hijacked the write. Nothing was saved to disk. | Step 6 — turn both memory toggles off. Then delete those memories in Settings → Memory. |
| Claude offers to run `git commit`, or gives them terminal git commands | It has no shell. It cannot run git, ever. | Files are the record; no commits are needed. If they want a snapshot they run it by hand. |
| Filesystem connector missing from the directory | Rare. | Fall back to `connector-config.example.json` — but that route needs **Node.js** installed, which the one-click connector may not. |
| Everything works, then stops after a Claude Desktop update | Settings can reset on update. | Re-check the allowed directory, the permission table, and both memory toggles. |

## Before you call it done

Run these with them and confirm each one:

1. **Read:** *"Using the Filesystem tools, list the files in /Users/NAME/constellation."*
   → a real listing, and the response header says it used the Filesystem integration.
2. **Boot:** paste the boot prompt in a new chat inside their Project.
   → Claude confirms it read `OPERATING.md` and checked the inbox.
3. **Write:** have them say something with both a fact and a feeling in it, e.g.
   *"I ran 5k today and I'm dreading a talk with my manager."*
   → the fact lands in `events/journal/YYYY-MM.md`, the feeling in
   `candid/reflections/YYYY-MM.md`. **Two different files.** Ask them to confirm both
   exist. If everything landed in one place, or nothing was written at all, setup is
   not finished — go back to the table above.

## Last thing

Tell them the two rules that keep the system honest, in their own words:

- **Start every session inside the Constellation project** (or paste the boot prompt),
  or Claude won't know the rules exist and will just chat — and nothing gets written.
- **`candid/` never leaves the Mac.** No backup, no iCloud, no repo. If they ever want
  to change that, they should read `docs/privacy.md` first and choose knowingly.
- **If the system itself misbehaves, it's not their fault and not theirs to fix.**
  Claude will note it in `feedback.md`; they paste that to whoever set this up. Tell
  them it contains nothing personal by design, so it's safe to send as-is.

Then tell them what happens next, so the empty folder doesn't feel like a dead end:

> *"You're set up. Start a new chat in your Constellation project whenever you're
> ready — the first one, Claude will help you work out what you're actually aiming at
> in one part of your life, and write it down. About half an hour. You don't need to
> prepare anything, just show up and talk."*

Then hand them over to `OPERATING.md` and stop. From that point on, the system runs
itself.
