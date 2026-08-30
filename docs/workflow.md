# How a day flows

Capture anywhere on your phone. Reflect on the Mac, where Claude can actually file
things. The `inbox.md` note is the bridge between the two.

```mermaid
flowchart TD
    subgraph CAP["📱 Capture — anywhere, any device"]
        A["In-the-moment note<br/>win · struggle · thought"] --> B["inbox.md<br/>(Apple Notes / iCloud)"]
    end

    subgraph SYNC["☁️ iCloud — capture channel ONLY"]
        C["inbox.md synced to all devices"]
    end
    B -->|iCloud sync| C

    subgraph MAC["💻 Mac — where reflection happens (local, NOT in iCloud)"]
        D["Claude Desktop<br/>+ filesystem connector"]
        E{"Route each item<br/>via the advice loop"}
        F["events/ — facts"]
        G["candid/ — feelings, people reads"]
        H["areas/* — goals & north stars"]
        I["clear inbox.md"]
        D --> E
        E --> F
        E --> G
        E --> H
        D --> I
    end
    C -->|Mac session reads it| D
    I -.->|emptied back| C

    subgraph GIT["🔒 git — version control, on the Mac"]
        J[("events commit")]
        K[("candid commit<br/>local only · no remote")]
    end
    F --> J
    H --> J
    G --> K

    J -->|optional push| L["🔐 Private repo<br/>(events only)"]

    D -.->|transient — sent ONLY<br/>when Claude reads a file| M["⚙️ Anthropic cloud inference"]

    classDef cloud fill:#fff3e0,stroke:#ef6c00,color:#000;
    classDef candid fill:#ffebee,stroke:#c62828,color:#000;
    class C,L,M cloud;
    class G,K candid;
```

## Reading it

1. **Capture (phone):** a thought goes into `inbox.md` via Apple Notes/iCloud. No
   framework, no thinking — just dump it. Facts only; nothing you'd want private.
2. **iCloud carries only the inbox** (orange). Nothing else touches iCloud.
3. **Mac session:** you paste the boot prompt; Claude reads `inbox.md`, runs the
   advice loop, **routes** each item — facts → `events/`, candid → `candid/`, goals →
   `areas/*` — then **empties the inbox**.
4. **git** versions everything locally. `events` can optionally push to a private
   repo; `candid` (red) is committed locally and **never gets a remote or iCloud**.
5. **Anthropic (dotted)** sees content **only transiently, only when Claude reads a
   file** during a session — never a stored copy, and candid only if you bring it
   into that session.

The two red nodes are the privacy floor: candid data lives on exactly one machine you
control. Everything cloud-colored is either facts or a transient processing hop —
explained honestly in [privacy.md](privacy.md).
