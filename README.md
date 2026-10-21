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
- **Sign in with Google:** one private workspace per Google account, with an account switcher (hosted version; see [Set up Google sign-in](#set-up-google-sign-in))
- **Inbox (Gmail):** search and read threads, turn an email into a task or request, save senders as contacts, draft a reply and save it to Gmail Drafts (never sent automatically)
- **Contacts:** clients and collaborators linked to projects, with imports from people you email and from Google Contacts

### Notes, library, reports
- **Notes:** folders, tags, pins, colors, archive and trash, search and sort, edit/split/preview modes, formatting toolbar, clickable checklists, `[[wiki links]]` with backlinks, 7 templates, Markdown export
- **Library:** books, articles, videos, podcasts, courses, papers, tools, templates, links and uploaded files, with status, progress, rating, tags and project links
- **Reports:** portfolio status, tasks, time log, billing, milestones, client requests, workload; filter by project and date range; export to CSV, Markdown or a styled HTML report

### Planning agent
An assistant panel (Ctrl+K) that reads the open project and can plan from a brief, estimate tasks, review risks, plan your week, triage a client request, draft client emails and write specs. Every change it suggests appears as a list you apply or dismiss.

## Two ways to run it

The app is built to run as a [Claude](https://claude.ai) artifact, where it uses Claude's runtime for storage, the agent, Gmail and file handling. It also runs as a plain web page, with some features turned off.

| Feature | Inside Claude | Standalone with Google sign-in | Standalone, no sign-in |
|---|---|---|---|
| Projects, tasks, all project views | Yes | Yes | Yes |
| Estimates, Gantt, critical path, workload | Yes | Yes | Yes |
| Time tracking, invoices | Yes | Yes | Yes |
| Notes, library (links), contacts, reports | Yes | Yes | Yes |
| Sign-in | Your Claude account | Sign in with Google, several accounts per device | None |
| Where data is saved | Your Claude workspace, synced across devices | This browser, one workspace per Google account | This browser |
| Gmail inbox, Gmail reply drafts | Yes (Gmail connector) | Yes (Gmail API) | No |
| Import Google Contacts | No | Yes (People API) | No |
| Planning agent and note/report AI actions | Yes | No | No |
| File uploads in the library | Yes | No (links still work) | No (links still work) |
| Report and note exports | Downloads a file | Copies the content to your clipboard | Copies the content to your clipboard |

When a feature isn't available, the page says so instead of failing.

## Set up Google sign-in

Sign in with Google, Gmail and Google Contacts work on your hosted copy once you create a free Google OAuth client ID and paste it into `index.html`. It takes about 10 minutes.

### 1. Create a Google Cloud project
1. Open [Google Cloud Console](https://console.cloud.google.com/) and sign in with your Google account.
2. Click the project picker at the top, then **New project**. Name it `Workbench` and click **Create**.

### 2. Turn on the APIs
1. Go to **APIs & Services → Library**.
2. Search for **Gmail API** and click **Enable**.
3. Search for **People API** and click **Enable** (used for importing Google Contacts).

### 3. Configure the consent screen
1. Go to **APIs & Services → OAuth consent screen** (shown as **Google Auth Platform** in newer consoles).
2. Choose **External** as the user type, and fill in the app name (`Workbench`), your support email and your developer contact email.
3. Under **Data access** (or **Scopes**), add:
   - `https://www.googleapis.com/auth/gmail.readonly` (read mail)
   - `https://www.googleapis.com/auth/gmail.compose` (create drafts)
   - `https://www.googleapis.com/auth/contacts.readonly` (read contacts)
4. Under **Audience** (or **Test users**), keep the app in **Testing** and add every Gmail address you will sign in with.

### 4. Create the client ID
1. Go to **APIs & Services → Credentials → Create credentials → OAuth client ID**.
2. Application type: **Web application**.
3. Under **Authorized JavaScript origins**, add the address you host the page on, with no path and no trailing slash:
   - `https://<your-username>.github.io`
   - `http://localhost:8000` if you also want to test locally
4. Click **Create** and copy the **Client ID** (it ends in `.apps.googleusercontent.com`). No client secret is needed.

### 5. Paste it into index.html
Near the top of the `<script>` in `index.html`, find this line and paste your ID between the quotes:

```js
const GOOGLE_CLIENT_ID='1234567890-abc123.apps.googleusercontent.com';
```

Commit and push. Your site now opens on a sign-in screen.

### How it works
- **Sign in:** the "Continue with Google" button uses [Google Identity Services](https://developers.google.com/identity/gsi/web). Google returns your name, email and photo, and the page checks that the sign-in was issued for your client ID and hasn't expired.
- **Several accounts:** each Google account you sign in with appears on the sign-in screen and in **Account**, and gets its own separate workspace (projects, notes, library, contacts) in this browser. Switch between them in one click; **Sign out** returns to the sign-in screen.
- **Your existing data:** the first account you sign in with takes over anything you created before sign-in was set up.
- **Gmail and Contacts:** connected separately with **Connect Gmail**, which opens Google's own permission window. Google grants access for about an hour; it is kept only for the open tab and you reconnect when it expires. Workbench can read mail, create drafts and read contacts. It has no permission to send email.
- **What "Testing" mode means:** Gmail scopes are restricted by Google, so an app in Testing works only for the test users you listed (up to 100). Google shows a "Google hasn't verified this app" screen. Click **Continue**: it's your own app. You only need Google's verification if you want strangers to use it.

### Important
- **Sign-in is not a lock.** This is a static page with no server: sign-in decides which workspace you see, but the data itself is stored in the browser on that device. Anyone using the same browser profile could open it. Don't use a shared computer for sensitive client data.
- **The client ID is not a secret.** It is public by design and only works on the origins you authorized.
- **Troubleshooting:**
  - `Error 400: redirect_uri_mismatch` or `origin_mismatch` means the page's address isn't listed under **Authorized JavaScript origins**.
  - `access_denied` means your Gmail address isn't a test user.
  - A button that never appears usually means a browser extension is blocking `accounts.google.com`.

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

- **Standalone:** everything is stored in your browser's `localStorage` under the keys `fw.data`, `fw.cols` and `fw.ui` (with `:<email>` added for each signed-in Google account, plus `fw.accounts` and `fw.active` for the account list). Nothing is sent to a server except your own requests to Google's APIs when you use Gmail or Contacts. Clearing site data, or using a private window, removes it, and data does not move between browsers or devices.
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
