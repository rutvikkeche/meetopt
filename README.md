# MeetOpt — Meeting Optimization System

> Minimize meetings. Maximize relevance.

MeetOpt is a lightweight, single-file web app that helps managers reduce meeting overload by intelligently grouping projects based on shared Points of Contact (POCs). Instead of scheduling one meeting per project, MeetOpt calculates the **minimum number of valid meetings** where every attendee is relevant to every discussion.

---

## Live Demo

Deploy instantly via GitHub Pages — no build step, no dependencies, no server required.

---

## The Problem

Cross-functional managers often handle multiple projects simultaneously, each with its own set of stakeholders. The default approach — one meeting per project — creates:

- Redundant meetings with overlapping attendees
- People sitting through discussions irrelevant to them
- Scheduling inefficiency that compounds with team size

**Example:**

| Project | POCs |
|---|---|
| Project 1 | P1, P2, P3 |
| Project 2 | P1, P4, P2 |
| Project 3 | P4, P3 |

Instead of 3 separate meetings, MeetOpt finds the optimal grouping — and ensures no one attends a meeting for a project they're not involved in.

---

## How It Works

### 1. Add Projects
Each project gets a name and a list of POCs (Points of Contact).

### 2. Choose Attendance Mode

Each project has two modes:

| Mode | Behavior |
|---|---|
| **ALL attend** | Every POC of this project must attend its meeting |
| **1 mandatory** | Only one chosen POC attends on behalf of the project |

### 3. Optimize

MeetOpt applies a **strict relevance rule** when grouping:

> Two projects may share a meeting **only if every attendee is a POC of every project** discussed in that meeting.

This guarantees nobody sits through an irrelevant discussion.

### 4. Get Results

The output shows:
- Number of meetings required
- Which projects are covered in each meeting
- Who is invited to each meeting
- The discussion agenda

---

## Optimization Logic

Grouping uses a **greedy merge algorithm** with the strict relevance constraint:

```
for each project:
    find the first existing group where:
        union(effective_attendees(group), effective_attendees(project))
        ⊆ intersection(pocs(all projects in merged group))
    if found → add to group
    else     → create new group
```

**Effective attendees** are determined by mode:
- `ALL` mode → all POCs are effective attendees
- `ONE` mode → only the mandatory POC is the effective attendee

**Key insight:** Use `1 mandatory` mode to control which person "bridges" two projects into a shared meeting. This is the primary lever for reducing meeting count while respecting relevance.

---

## Example Output

```
2 meetings cover all 3 projects — 1 meeting saved

Meeting 01
Projects:   Project Alpha, Project Beta
Invite:     P1
Agenda:     Update on Project Alpha
            Update on Project Beta

Meeting 02
Projects:   Project Gamma
Invite:     P3, P4
Agenda:     Update on Project Gamma
```

*(Alpha and Beta both set to "1 mandatory → P1"; Gamma set to "ALL attend")*

---

## Deployment

### GitHub Pages (recommended)

1. Fork or clone this repository
2. Ensure `index.html` is in the root of the `main` branch
3. Go to **Settings → Pages → Source → Deploy from branch**
4. Select `main` branch, `/ (root)` folder
5. Click **Save** — your app will be live at `https://<username>.github.io/<repo>`

### Local

No build step needed. Just open the file directly:

```bash
open index.html
# or
python3 -m http.server 8080
```

---

## Project Structure

```
meetopt/
├── index.html    # Entire application — HTML, CSS, and JS in one file
└── README.md
```

MeetOpt is intentionally a **single self-contained file** for easy deployment, sharing, and auditing.

---

## Tech Stack

| Layer | Choice |
|---|---|
| Framework | Vanilla HTML/CSS/JS — no dependencies |
| Fonts | DM Sans, DM Mono, Instrument Serif (Google Fonts) |
| Algorithm | Greedy grouping with strict relevance constraint |
| Deployment | GitHub Pages (static) |

---

## Contributing

Contributions are welcome. Some areas for improvement:

- **Export to calendar** — generate `.ics` files for each optimized meeting
- **Drag-to-reorder** projects before optimization
- **Save/load** project configurations via JSON export
- **Conflict detection** — flag when no valid grouping exists across all projects
- **Time slot suggestions** — integrate with calendar APIs

---

## License

MIT — free to use, modify, and deploy.
