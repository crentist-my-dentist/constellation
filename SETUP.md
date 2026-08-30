# Setup (one-time)

Plan for ~20 minutes with a helper the first time. You need: a Mac, an iPhone, a
free Claude account, and the free **Claude Desktop** app.

> **Why the Mac does the real work:** Claude can only read and write your files
> through a *local* file connector that runs on the Mac. The iPhone can't run that —
> so the phone is for *capturing* thoughts, and the Mac is where Claude actually
> files and reflects. See [docs/workflow.md](docs/workflow.md).

---

## 1. Put the framework somewhere permanent (NOT in iCloud)

Keep this folder in a plain local location like `~/constellation`. **Do not put it
in iCloud Drive** — iCloud syncing a live git repo can corrupt it and make duplicate
"file 2.md" copies. (The one iCloud file we *do* use is the inbox, in step 6.)

## 2. Create your two data folders from the templates

The framework ships templates; your actual data lives in `events/` and `candid/`,
which are ignored by the framework's own git so they stay yours.

```sh
cd ~/constellation
cp -R templates/events  events
cp -R templates/candid  candid
cp    templates/inbox.md inbox.md   # (temporary; real inbox goes in iCloud, step 6)
```

## 3. Turn events/ and candid/ into their own git repos

```sh
cd ~/constellation/events && git init && git add -A && git commit -m "start events"
cd ~/constellation/candid && git init && git add -A && git commit -m "start candid"
```

- **events** may later get a *private* remote for backup (optional, step 7).
- **candid** must **never** get a remote. It stays on this machine. This is the
  privacy guarantee — read [docs/privacy.md](docs/privacy.md) before changing it.

## 4. Install Claude Desktop and connect it to your folder

1. Install the **Claude Desktop** app and sign in. **The free plan is fine** —
   everything in this guide works on it. (Claude Code's "Select folder…" and Cowork
   are paid features. You don't need either one.)
2. In a chat, click the **+** in the message box → **Add connector** → **Browse
   connectors**.
3. Search for **Filesystem** and open it.
4. Under **Allowed Directories**, click **Add directory** and pick your
   `~/constellation` folder. Choose *only* that folder — this is the privacy
   boundary. Claude cannot see anything outside it.
5. Set the tool permissions using the table below.
6. Click **Save**.
7. **Fully quit Claude Desktop (⌘Q) and reopen it.** The connector doesn't start
   until the app restarts. Skipping this is the most common setup failure, and the
   symptom is confusing — see "Check it worked" below.

### Tool permissions

Each tool can be set to allow (✓), ask every time (✋), or deny (⃠).

| Tools | Set to | Why |
|---|---|---|
| All read-only tools | **Allow** | Claude reads your inbox, journal, goals and north stars every session. Approval prompts here make the system exhausting to use. |
| Write File, Edit File, Create Directory | **Allow** | This is the core loop — filing what you said. Mistakes are recoverable, because `events/` and `candid/` are git repos. |
| Move File | **Needs approval** | The one tool that can move a file out from under the system. It's rarely needed, so a prompt costs nothing. |
| **Copy file to Claude** | **Deny** | It copies files out of your folder into Claude's own storage — a permanent copy outside the boundary. See [docs/privacy.md](docs/privacy.md). |

**Check it worked.** In a new chat, ask:

> *Using the Filesystem tools, list the files in my constellation folder.*

You should get a real listing (`OPERATING.md`, `events`, `candid`, …). If Claude says
it can't find the folder, or offers to let you *upload* it, then it's talking to its
own sandbox rather than your Mac — **quit the app with ⌘Q, reopen it, and ask again.**

> **Fallback:** if the connector directory doesn't offer Filesystem, you can configure
> it by hand — see [connector-config.example.json](connector-config.example.json).
> That route requires Node.js on the Mac; the one-click connector does not.

## 5. Turn off Claude's built-in memory

**Don't skip this one.** Claude Desktop has its own memory feature and it is **on by
default**. Left on, Claude will "remember" what you tell it *instead of writing it to
your files* — you'll see "Created 2 memories" and nothing will land on disk. Your
journal stays empty while everything looks like it's working.

Go to **Settings → Memory** and switch **off**:

- **Generate memory from chats**
- **Include sensitive topics in memory** — its own description covers health
  conditions and religious beliefs, which is exactly what `candid/` exists for.

This is a per-account setting, so it has to be done on every account that uses the
system.

## 6. Set up the iCloud capture inbox

This is the only thing that lives in iCloud, so you can capture from your phone.

1. Create a plain text note or file called `inbox.md` in **iCloud Drive** (Files app
   works on both Mac and iPhone). Apple Notes works too if you prefer.
2. On the Mac, either move `~/constellation/inbox.md` into iCloud and replace it with
   a link, or just tell Claude in step 8 where the iCloud inbox lives so it reads
   from there.
3. On the iPhone, add that file/note to your Home Screen or Share sheet for one-tap
   capture. **Only put facts-in-the-moment here — nothing you'd want to keep private.**

## 7. (Optional) Back up events to a private repo

Only `events/`, and only if you want off-machine backup:

```sh
cd ~/constellation/events
git remote add origin <your-private-repo-url>
git push -u origin main
```

Do **not** do this for `candid/`.

## 8. Set up a Project so the rules load themselves

The Desktop app does not auto-load the rules — every new chat starts blank. The boot
prompt is this, **with your real path filled in**:

> **Read /Users/YOUR_USERNAME/constellation/OPERATING.md using the Filesystem tools
> and follow it. Check the inbox, then let's talk.**

Both details matter. The **full path** and the words **"using the Filesystem tools"**
are what make Claude look on your Mac. Vaguer phrasing — "read OPERATING.md in my
constellation folder" — often makes it search its own sandbox instead, and it will
tell you, confidently, that the file doesn't exist.

**Do this once instead of pasting it every time:** make a **Project** called
Constellation (free accounts have Projects), put that line in the project's custom
instructions, and start every session as a new chat inside that project. Then the
rules load on their own and there is nothing to remember.

Without this, Claude doesn't know the rules exist and will just chat with you — the
other way to end up with an empty journal.

---

That's it. From here: capture on your phone, and once in a while sit down at the Mac,
paste the boot prompt, and let Claude file everything and talk things through.
