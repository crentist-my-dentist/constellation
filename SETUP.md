# Setup (one-time)

Plan for ~20 minutes with a helper the first time. You need: a Mac, an iPhone, a
free Claude account, and the free **Claude Desktop** app.

> **Why the Mac does the real work:** Claude can only read and write your files
> through a *local* file connector that runs on the Mac. The iPhone can't run that —
> so the phone is for *capturing* thoughts, and the Mac is where Claude actually
> files and reflects. See [docs/workflow.md](docs/workflow.md).

---

## 1. Get the framework and put it somewhere permanent (NOT in iCloud)

Download it from <https://github.com/crentist-my-dentist/constellation> — the green
**Code** button → **Download ZIP** → unzip → rename `constellation-main` to
`constellation`. No GitHub account needed. (If you already use git, `git clone` the
same URL instead; then future updates are one `git pull`.)

Keep this folder in a plain local location like `~/constellation`. **Do not put it
in iCloud Drive** — iCloud syncing a live git repo can corrupt it and make duplicate
"file 2.md" copies. (The one iCloud file we *do* use is the inbox, in step 6.)

## 2. Your data folders — Claude makes these for you

Your actual writing lives in `events/` and `candid/`, created from the templates that
ship with the framework. **You don't need to do anything here** — the first time you
talk to Claude (step 8) it will notice they're missing and create them.

If you'd rather do it by hand:

```sh
cd ~/constellation
cp -R templates/events  events
cp -R templates/candid  candid
cp    templates/inbox.md inbox.md   # (temporary; real inbox goes in iCloud, step 6)
cp    templates/feedback.md feedback.md
```

## 3. (Optional) Turn events/ and candid/ into git repos

```sh
cd ~/constellation/events && git init && git add -A && git commit -m "start events"
cd ~/constellation/candid && git init && git add -A && git commit -m "start candid"
```

- **events** may later get a *private* remote for backup (optional, step 7).
- **candid** must **never** get a remote. It stays on this machine. This is the
  privacy guarantee — read [docs/privacy.md](docs/privacy.md) before changing it.

**This step is optional and you can skip it.** Claude can't run git — the file
connector gives it files, not a shell — and the system doesn't need it. Your files
*are* the record. Doing this only means that if you ever want a snapshot (before a big
cleanup, say), you can run `git add -A && git commit -m "snapshot"` inside either
folder. If you skip it now you can always do it later.

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
| Write File, Edit File, Create Directory | **Allow** | This is the core loop — filing what you said, every session. Asking each time makes the system unusable. There's no automatic undo (see step 3), but Claude only ever *adds* to dated journal and reflection entries — it doesn't rewrite them. |
| Move File | **Needs approval** | The one tool that can move a file out from under the system. It's rarely needed, so a prompt costs nothing. |

> **Watch this one.** Setting the *group* dropdown to Allow flips **Move File** to Allow
> as well. Set the group first, then put Move File back to "ask every time"
> individually. It's easy to miss, and the screen looks correct either way.
| **Copy file to Claude** | **Deny** | It copies files out of your folder into Claude's own storage — a permanent copy outside the boundary. See [docs/privacy.md](docs/privacy.md). |

**Check it worked.** In a new chat, ask:

> *Using the Filesystem tools, list the files in my constellation folder.*

You should get a real listing (`OPERATING.md`, `events`, `candid`, …). If Claude says
it can't find the folder, or offers to let you *upload* it, then it's talking to its
own sandbox rather than your Mac — **quit the app with ⌘Q, reopen it, and ask again.**

> **If the Filesystem connector reports an error** — one adopter saw *"the Filesystem
> MCP tools are hitting a server-side schema bug and can't be used right now"* — don't
> panic and don't start over. Claude Desktop will usually offer its own **built-in file
> access** instead, with a separate permission prompt for the same folder. Accepting
> that is fine: same folder scope, same result. Just make sure it's pointed at your
> `constellation` folder and nothing wider. If it offers you your whole home directory,
> say no and narrow it.
>
> **Fallback 2:** if no Filesystem connector appears in the directory at all, configure
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
3. On the iPhone, get to it quickly. iOS **can't** put a plain file on the Home Screen
   directly, so either:
   - **Shortcuts app** → new shortcut → *Open File* → pick `inbox.md` → **Add to Home
     Screen**. One tap, but fiddly to set up.
   - **Or skip it entirely** and just open the Files app when you want to jot something.
     It'll be in Recents after the first time.

   This step is a convenience, not a requirement — the system works fine without it.
   **Only put facts-in-the-moment here — nothing you'd want to keep private.**

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

**Set it up once instead of pasting it every time.** Free accounts have Projects:

1. Click **Projects** in the left sidebar.
2. Click **New project**. Under *"What are you working on?"* type **Constellation**.
3. **Leave "What are you trying to achieve?" as a plain description** — something like
   *"my life system"*. That box is only a label for the project. **It is not the
   instructions**, and the boot prompt will do nothing there.
4. Create the project, then find the **Instructions** panel on the right of the project
   page ("Add instructions to tailor Claude's responses"). Click **+** and paste the
   boot prompt above, with your path filled in. **This is the field that matters.**
5. Make sure the **Filesystem connector is switched on for the project**, so every
   chat inside it can reach your files.
6. From now on, start each session with **a new chat inside that project**.

**Check it worked:** start a new chat in the project and just say **hi**. Claude should
come back having read the rules and checked your inbox — not with a generic "what's on
your mind?". If you get the generic answer, the instructions didn't load: paste the
boot prompt manually for now, and it'll work exactly the same.

Without either the project or the pasted prompt, Claude doesn't know the rules exist
and will just chat with you — the other way to end up with an empty journal. It won't
warn you; it'll simply be ordinary Claude.

---

That's it. From here: capture on your phone, and once in a while sit down at the Mac,
paste the boot prompt, and let Claude file everything and talk things through.
