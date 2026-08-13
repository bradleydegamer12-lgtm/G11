# g11 — Grade 11 class hub

A free, mobile-first web app for your class: announcements, timetable, resources, assignment tracker, grade estimator, and a study lounge. Built as plain HTML/CSS/JS so it needs **zero build step** — you can host it on GitHub Pages for free, forever.

## Recommended tech stack

| Layer | Choice | Why |
|---|---|---|
| Frontend | Vanilla HTML/CSS/JS | No build step = GitHub Pages can serve it directly. Works instantly on iOS and Android via any browser (or "Add to Home Screen"). |
| Hosting | **GitHub Pages** (free) | Free static hosting straight from your repo, custom domain optional, HTTPS included. |
| Shared data (chat, tasks) | **Firebase Realtime Database** (free Spark plan) *or* **Supabase** (free tier) | Both have generous free tiers and work from pure client-side JS — no server needed. Firebase is slightly simpler to wire into a Realtime Database chat; Supabase gives you a real Postgres database if you'd rather write SQL later. |
| Fonts | Google Fonts CDN | Space Grotesk (headings), Inter (body), JetBrains Mono (countdowns/numbers) |

You do **not** need React, Vue, or a bundler for this — for a class site of this size, plain JS is faster to build, easier to debug, and one less thing to break.

## Page / navigation structure

```
Top bar: [g11 logo]                              [☰ menu — top right]
                                                          │
                                     ┌────────────────────┴───────────────────┐
                                     │  Dashboard                              │
                                     │  Timetable                              │
                                     │  Resource Hub                           │
                                     │  Assignments                            │
                                     │  Grade Calculator                       │
                                     │  Study Lounge                           │
                                     │  ── Dark/Light toggle ──                │
                                     └──────────────────────────────────────────┘

Bottom tab bar (mobile only): Home · Timetable · Resources · Tasks · Lounge
Floating mascot: draggable anywhere, tap for a random study tip
```

Each page is its own `.html` file (multi-page site, not a single-page app) — simplest possible setup for GitHub Pages, and each page loads fast since there's no framework to parse.

```
g11/
├── index.html            → Dashboard (announcements, exam countdown, quick stats)
├── timetable.html        → Weekly schedule + live "up next" countdown
├── resources.html        → Resource hub, grouped by subject, searchable
├── assignments.html      → Assignment tracker with subject filters
├── calculator.html       → Grade/mark estimator
├── lounge.html           → Study lounge chat (localStorage demo, Firebase-ready)
├── css/style.css         → All design tokens + styles
├── js/data.js            → Subjects, timetable, exam date, quotes — EDIT THIS FIRST
├── js/app.js             → Theme toggle, nav drawer, mascot, countdowns
├── js/resources-data.js  → File links for the Resource Hub
├── js/firebase-config.example.js → Copy → firebase-config.js once you set up Firebase
└── README.md
```

## Step-by-step: get it live on GitHub Pages

1. **Create a GitHub account** (if you don't have one) at github.com.
2. **Create a new repository** — name it `g11` (or anything), keep it Public.
3. **Upload these files**: on the repo page, click "Add file → Upload files", drag in the whole `g11` folder contents (keep the folder structure — `css/`, `js/`, and the `.html` files at the root).
4. Commit the upload.
5. Go to **Settings → Pages** in your repo.
6. Under "Build and deployment", set **Source: Deploy from a branch**, Branch: `main`, folder `/ (root)`. Save.
7. Wait ~1 minute, then your site is live at `https://<your-username>.github.io/g11/`.
8. Share that link with your class and teacher — works on any phone or laptop browser, no install needed.

## Step-by-step: make it truly shared (Firebase)

Right now the Assignment Tracker and Study Lounge save data **only on each person's own device** (via `localStorage`) — it works immediately with zero setup, but classmates won't see each other's tasks or messages yet.

1. Go to [console.firebase.google.com](https://console.firebase.google.com) → **Create a project** (free plan).
2. Click the **Web** icon (`</>`) to register a web app → copy the config object it shows you.
3. In the left sidebar, open **Realtime Database → Create database → Start in test mode** (fine for a class project; tighten the rules later if you want).
4. Copy `js/firebase-config.example.js` → rename to `js/firebase-config.js`, paste in your real keys.
5. Add `<script type="module" src="js/firebase-config.js"></script>` to `lounge.html` (and `assignments.html` if you want shared tasks too).
6. Swap the `localStorage` read/write calls in those pages for the Firebase `push()`/`onValue()` pattern shown in the comments of `firebase-config.example.js`.
7. Commit and push — GitHub Pages updates automatically within a minute of any push to `main`.

## Editing your real content

- **Timetable & subjects**: edit `js/data.js` — `SUBJECTS` and `TIMETABLE` arrays.
- **Next exam countdown**: edit `NEXT_EXAM` in `js/data.js`.
- **Study tips**: edit the `QUOTES` array in `js/data.js` — shown when the mascot is tapped.
- **Resource files**: edit `js/resources-data.js`, and commit actual PDFs into a `resources/<subject>/` folder so the links work.
- **Announcements**: currently hardcoded in `index.html` — for a fully editable board, wire it to Firebase the same way as the Lounge.

## Notes on the design

Theme is a "digital planner" look — warm paper / ink-dark toggle, highlighter-yellow accent for what matters now, coral for urgent deadlines, mint for done. Space Grotesk for headings, Inter for body text, JetBrains Mono for anything ticking (countdowns, marks) so numbers read like a clock, not decoration. The mascot doubles as your quote widget — drag it anywhere, tap it for a random study tip instead of it just sitting there as decoration.
