# Freelance Workbench

A planning and project management tool for freelancers, modeled on Teamwork.com. It keeps task lists, estimates, schedules, time, invoices, client requests, notes, contacts and reference materials in one place, with a planning agent that proposes changes you review before they apply.

The whole app is a single `index.html` file: no build step, no backend, no dependencies to install.

## Features

### Projects and tasks
- **Task lists** (phases, tracks, sprints) with subtasks, drag-and-drop between lists, and an "All lists" picker with counts
- **Task details:** assignees, start and due dates, priority, tags, milestone, parent task, repeat schedule (daily, weekly, monthly), progress, acceptance criteria, comments, technical notes, billable and private flags, custom fields
- **Views:** Dashboard, List, Board (drag cards between statuses), Table (spreadsheet-style inline editing), Gantt, Calendar, Milestones, Time, Finance, Schedule, Requests, Messages, Notebooks, Links
- **Filters:** search, assignee, tag, priority, due date (overdue, today, next 7 days, none), show completed
- **Layers:** show or hide groups of task details (estimates, dates, dependencies, risk, billing, criteria) and add your own custom fields
- **Templates:** Web app build, API integration, Marketing website, or a blank project

### Planning depth
- **Three-point estimates (PERT):** optimistic, most likely and pessimistic hours per task, rolled up into an expected total, a standard deviation and a P90 ("90% sure") figure
- **Dependencies and critical path:** finish-to-start links drawn on the Gantt chart; tasks with no slack are flagged as the critical path
- **Schedule health:** remaining effort compared with your focus hours per day and the deadline (On track, Tight, At risk)
- **Workload:** remaining hours per person for the next 4 weeks against weekly capacity

### Time and money
- Start/stop timer, manual time entries, weekly timesheet
- Hourly rate, budget hours, fixed fee and effective rate
- Invoices created from billable, uninvoiced time, with draft, sent and paid status

### Client work
- **Requests:** log client asks, mark them in scope or as a change order, link them to tasks
- **Inbox (Gmail):** search and read threads, turn an email into a task or request, save senders as contacts, draft a reply and save it to Gmail Drafts (never sent automatically)
- **Contacts:** clients and collaborators linked to projects, with an import that suggests people you email

### Notes, library, reports
- **Notes:** folders, tags, pins, colors, archive and trash, search and sort, edit/split/preview modes, formatting toolbar, clickable checklists, `[[wiki links]]` with backlinks, 7 templates, Markdown export
- **Library:** books, articles, videos, podcasts, courses, papers, tools, templates, links and uploaded files, with status, progress, rating, tags and project links
- **Reports:** portfolio status, tasks, time log, billing, milestones, client requests, workload; filter by project and date range; export to CSV, Markdown or a styled HTML report

### Planning agent
An assistant panel (Ctrl+K) that reads the open project and can plan from a brief, estimate tasks, review risks, plan your week, triage a client request, draft client emails and write specs. Every change it suggests appears as a list you apply or dismiss.

## Two ways to run it

The app is built to run as a [Claude](https://claude.ai) artifact, where it uses Claude's runtime for storage, the agent, Gmail and file handling. It also runs as a plain web page, with some features turned off.

| Feature | Inside Claude | Standalone (GitHub Pages, local file) |
|---|---|---|
| Projects, tasks, all project views | Yes | Yes |
| Estimates, Gantt, critical path, workload | Yes | Yes |
| Time tracking, invoices | Yes | Yes |
| Notes, library (links), contacts, reports | Yes | Yes |
| Where data is saved | Your Claude workspace, synced across devices | This browser only (`localStorage`) |
| Planning agent and note/report AI actions | Yes | No |
| Gmail inbox, contact import, Gmail drafts | Yes (Gmail connector) | No |
| File uploads in the library | Yes | No (links still work) |
| Report and note exports | Downloads a file | Copies the content to your clipboard |

When a feature isn't available, the page says so instead of failing.

## Host it on GitHub Pages

1. Create a new repository on GitHub, for example `freelance-workbench`.
2. Add `index.html` and `README.md` to the repository root:
   ```bash
   git init
   ```
   ```bash
   git add index.html README.md
   ```
   ```bash
   git commit -m "Add Freelance Workbench"
   ```
   ```bash
   git branch -M main
   ```
   ```bash
   git remote add origin https://github.com/<your-username>/freelance-workbench.git
   ```
   ```bash
   git push -u origin main
   ```
3. In the repository, open **Settings → Pages**.
4. Under **Build and deployment**, set **Source** to *Deploy from a branch*, choose `main` and `/ (root)`, then **Save**.
5. After a minute the site is live at `https://<your-username>.github.io/freelance-workbench/`.

To try it without GitHub, open `index.html` directly in a browser.

## Data and privacy

- **Standalone:** everything is stored in your browser's `localStorage` under the keys `fw.data`, `fw.cols` and `fw.ui`. Nothing is sent to a server. Clearing site data, or using a private window, removes it, and data does not move between browsers or devices.
- **Inside Claude:** data is stored in the artifact's private database, readable only by the owner by default. Gmail is accessed through your own Claude Gmail connector with your permission.
- **Fonts:** loaded from Google Fonts (Bricolage Grotesque, IBM Plex Sans, IBM Plex Mono) with system font fallbacks.
- **Secrets:** don't store passwords or API keys in notes, links or task fields.

## Keyboard shortcuts

| Keys | Action |
|---|---|
| `N` | New task (in a project, when not typing) |
| `Ctrl` / `Cmd` + `K` | Open or close the planning agent |
| `Esc` | Close the open dialog, panel or task |
| `Enter` in the agent box | Send (`Shift+Enter` for a new line) |

## How the numbers are calculated

- **Expected hours per task:** `E = (o + 4m + p) / 6`, standard deviation `σ = (p − o) / 6`
- **Remaining effort:** sum of `max(0, E − logged hours)` over open tasks
- **P90:** `remaining + 1.2816 × √(Σσ²)`
- **Schedule:** tasks with dates keep them; tasks without dates start after their dependencies finish, lasting `ceil(E / focus hours per day)` days
- **Critical path:** forward and backward pass over dependencies; tasks with zero slack are critical
- **Health:** *At risk* if P50 working days exceed days left, *Tight* if P90 does, otherwise *On track*

## Project structure

```
freelance-workbench/
├── index.html   # the whole app: HTML, CSS and JavaScript in one file
└── README.md
```

Inside `index.html`, the code is organized in sections: constants and templates, planning math, persistence, renderers for each view, the task drawer, dialogs, the agent, then the Gmail, notes, library and reports modules, and finally event handling and startup.

## Customizing

- **Colors and fonts:** edit the CSS variables at the top of the `<style>` block. Light and dark themes are both defined there.
- **Project templates:** edit the `TEMPLATES` object (task lists, tasks, estimates, dependencies, risk, criteria).
- **Note templates:** edit `NOTE_TPL`.
- **Library types and statuses:** edit `LIB_TYPES` and `LIB_STATUS`.
- **Currencies:** edit `CURRENCIES`.

## Browser support

Current versions of Chrome, Edge, Firefox and Safari, on desktop and mobile. Layouts adapt down to phone width.

## License

No license has been chosen yet. Add a `LICENSE` file (for example MIT) if you want others to reuse the code.
