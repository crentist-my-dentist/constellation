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
"file 2.md" copies. (The one iCloud file we *do* use is the inbox, in step 5.)

## 2. Create your two data folders from the templates

The framework ships templates; your actual data lives in `events/` and `candid/`,
which are ignored by the framework's own git so they stay yours.

```sh
cd ~/constellation
cp -R templates/events  events
cp -R templates/candid  candid
cp    templates/inbox.md inbox.md   # (temporary; real inbox goes in iCloud, step 5)
```

## 3. Turn events/ and candid/ into their own git repos

```sh
cd ~/constellation/events && git init && git add -A && git commit -m "start events"
cd ~/constellation/candid && git init && git add -A && git commit -m "start candid"
```

- **events** may later get a *private* remote for backup (optional, step 6).
- **candid** must **never** get a remote. It stays on this machine. This is the
  privacy guarantee — read [docs/privacy.md](docs/privacy.md) before changing it.

## 4. Install Claude Desktop and give it access to this folder

1. Install the **Claude Desktop** app and sign in (the free plan is fine).
2. Give Claude read/write access to `~/constellation`:
   - If your app offers one-click **Desktop Extensions / connectors**, add the
     **Filesystem** connector and point it at `~/constellation`.
   - Otherwise, edit `claude_desktop_config.json` using
     [connector-config.example.json](connector-config.example.json) as a template
     (replace `YOUR_USERNAME`), then restart the app.
3. In a new chat, confirm it works: *"List the files in my constellation folder."*

## 5. Set up the iCloud capture inbox

This is the only thing that lives in iCloud, so you can capture from your phone.

1. Create a plain text note or file called `inbox.md` in **iCloud Drive** (Files app
   works on both Mac and iPhone). Apple Notes works too if you prefer.
2. On the Mac, either move `~/constellation/inbox.md` into iCloud and replace it with
   a link, or just tell Claude in step 7 where the iCloud inbox lives so it reads
   from there.
3. On the iPhone, add that file/note to your Home Screen or Share sheet for one-tap
   capture. **Only put facts-in-the-moment here — nothing you'd want to keep private.**

## 6. (Optional) Back up events to a private repo

Only `events/`, and only if you want off-machine backup:

```sh
cd ~/constellation/events
git remote add origin <your-private-repo-url>
git push -u origin main
```

Do **not** do this for `candid/`.

## 7. Start every session with the boot prompt

The Desktop app does not auto-load the rules. Begin each chat by pasting:

> **Read OPERATING.md in my constellation folder and follow it. Check the inbox,
> then let's talk.**

If your app has **Projects**, put that line in the project's custom instructions so
it happens automatically. (If it doesn't, the paste works every time.)

---

That's it. From here: capture on your phone, and once in a while sit down at the Mac,
paste the boot prompt, and let Claude file everything and talk things through.
