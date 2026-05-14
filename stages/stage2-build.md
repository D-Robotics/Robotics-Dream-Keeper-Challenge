# Stage 2 — Build Challenge

| Field | Value |
|--------|--------|
| **Suggested duration** | 15 days |
| **Slogan** | Design Your Own Intelligent Robot |
| **Hardware baseline** | D-Robotics **RDK X5** (design may assume sensors / actuators you plan to use in Stage 3) |

---

## Core Objective

Advance from **“I can run demos”** to **“I can design a robot / AI system”**: clear product thinking, architecture, ROS 2–aware decomposition, and an executable engineering plan.

---

## Challenges

### Challenge 1 — Concept & Application Design

Produce a **one-page–level** (can be longer if structured) design brief that is **unambiguous** to a reviewer:

- **Scenario:** operating environment and constraints (indoor / outdoor, lighting, latency).  
- **User:** who benefits and primary interaction mode.  
- **Core AI capabilities:** perception, decision, and actuation at a high level.  
- **Innovation / differentiation:** what is non-trivial compared to a stock demo.

**Standard of completion:** A **Markdown section** in your GitHub repo or PR with the four bullets above filled in with **measurable goals** (e.g. target FPS, max latency, success criteria for a task).

---

### Challenge 2 — AI System Architecture

Deliver architecture artifacts suitable for engineering review:

1. **System flow diagram** — data from sensors → AI → planning / state → actuators.  
2. **Module design** — responsibilities, inputs/outputs, and failure modes per module.  
3. **Compute allocation** — what runs on **BPU**, **CPU**, and (if any) **host offload**; expected utilisation ranges.  
4. **ROS 2 node graph** — node names, topics/services/actions, message types, and approximate rates.

**Standard of completion:**  
- Diagrams in **PNG/SVG** or **Mermaid** inside Markdown (renderable on GitHub).  
- A **table** mapping each major module to **thread/process**, **CPU core affinity** (if used), and **real-time constraints**.

---

### Challenge 3 — Engineering Plan

Ship planning artifacts that a team could execute:

- **BOM (Bill of Materials)** — part name, qty, supplier link or SKU, notes (voltage, interface).  
- **Timeline / Roadmap** — week-by-week milestones through Stage 3 demo.  
- **Risk analysis** — top 5 risks with **mitigation** and **trigger** (when to pivot).  
- **GitHub project structure** — folder tree and conventions (e.g. `src/`, `launch/`, `models/`, `docs/`).

**Standard of completion:** All four subsections present in repo `docs/` (or root) with **version and date** in the header.

---

## Core Skills (Competency Checklist)

Tie your work to the official **stage competency table**. Stage 2 typically emphasizes:

- Requirements → architecture traceability  
- ROS 2 graph design and message design  
- On-device resource budgeting (BPU NPU vs CPU)  
- Risk-driven iteration and test planning  

---

## Deliverables (Submission Spec)

| # | Item | Requirement |
|---|------|-------------|
| 1 | **GitHub Project** | Use **GitHub Projects** (board or table) **or** a `ROADMAP.md` with equivalent milestones; link must be public. |
| 2 | **Markdown proposal** | Single entry doc (e.g. `PROPOSAL.md`) aggregating Challenge 1–3 content with clear headings. |
| 3 | **Architecture diagrams** | Embedded in Markdown or under `docs/images/`. |
| 4 | **Roadmap** | Dates or week numbers through final demo. |
| 5 | **Community share** | Post summary + link to repo; submit **permalink** in your `projects/` PR file. |

**Format rules**

- All diagrams: **legible font**, dark-on-light or high-contrast theme.  
- Prefer **English** for diagrams and tables to match program review.  
- BOM: include **estimated cost** row optional but encouraged.

**PR to this repo:** showcase file under `projects/` per [projects/README.md](../projects/README.md).

---

## Completion Rewards

- Title: **RDK Builder**  
- **Official project recommendations** shortlist eligibility  
- **Office Hour** deep-dive slots  
- **Creator Badge** and elevated **community role** (as announced)
