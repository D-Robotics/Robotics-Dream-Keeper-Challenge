# Frequently Asked Questions (FAQ)

---

## Repository & Access

### I cannot open the official repository or links return 404.

- Confirm you are using the **canonical URL** announced for the season.  
- If you are in a restricted network, try another network or VPN policy-compliant connection.  
- Ask in **Discord** with a **screenshot** (hide personal tokens).

### Should I fork the official repo or only create my own project repo?

- **Both:** your **code and experiments** live in **your personal repo**.  
- The **showcase Markdown** is submitted via **PR** to the official repo’s **`projects/`** folder.

---

## Pull Requests

### My PR shows merge conflicts.

```bash
git fetch upstream
git checkout <your-branch>
git merge upstream/main
# resolve conflicts in projects/<your-file>.md
git add .
git commit -m "Merge upstream and resolve conflicts"
git push
```

### Maintainers asked to rename my file.

Rename locally, `git mv` preferred:

```bash
git mv projects/Old-Name.md projects/Correct-Project-Name.md
git commit -m "Rename showcase file per maintainer request"
git push
```

### Can I submit binary images or PDFs in the PR?

- **Small images** (screenshots) embedded via **external hosting** or **repo** are OK if total size is reasonable.  
- **Large PDFs / videos:** host on **GitHub Releases**, **Google Drive (link)**, or **YouTube** — link them in the Markdown.

---

## Tasks & Rules

### Stage requirements seem ambiguous for my hardware.

- Post in **Discord** `#hardware-questions` with: board revision, OS image version, sensor model, and **what you tried**.  
- Prefer **officially supported** peripherals for fastest review.

### Do I have to use ROS 2 in Stage 1?

- **Stage 1:** ROS 2 is **not mandatory** unless the specific sensor path you choose requires it.  
- **Stage 2–3:** ROS 2 design and integration are **required** as written in stage docs.

---

## Awards & Identity

### How do I earn RDK Explorer / RDK Builder / RDK Creator titles?

- Complete the corresponding challenge stage and submission requirements listed in the stage docs and [awards.md](../awards.md).  
- Each title is tied directly to challenge completion: **Stage 1 → RDK Explorer**, **Stage 2 → RDK Builder**, **Stage 3 → RDK Creator**.

### When are prizes distributed?

- Announced **end-of-season** dates on Discord and the main README.  
- Physical items may require **shipping form**; grants may require **tax / eligibility** documents.

---

## Technical (RDK X5)

### Camera works in tool A but not in ROS 2.

- Check **device permissions** and **user groups**.  
- Match **sensor format** in driver vs ROS node parameters.  
- Share `v4l2-ctl --all` or equivalent in Discord (redact serials if needed).

### BPU demo fails with version mismatch.

- Pin **firmware / OS / toolchain** versions exactly as in the working sample README.  
- Re-flash a **clean image** if dependency drift is suspected.

---

## Legal & Content

### Who owns my code?

- **Your repository code:** **you** own it (subject to your license).  
- **Showcase Markdown merged here:** usable by the **program organizers** for promotion and judging as stated in [README.md](../README.md).

### Can I use third-party models?

- Yes, if **license-compatible**; state the **license** and **attribution** in your README.

---

## Still Stuck?

Open a **thread in Discord** with: **stage**, **goal**, **commands run**, **full error text**, and **photos of wiring** if relevant.
