# RESTART 27 — Vite + React rebuild

Migration of the single-file `restart27_v3_17_1.html` into a proper
multi-file Vite + React project. Same Supabase backend, same design
(Obsidian black, glassmorphism, gold/neon accents, Ultra/AERO/Clean
themes) — the change is structural, not visual.

## Run it

```bash
npm install
npm run dev
```

Opens at `http://localhost:5173`. `.env` already has the same Supabase
project the live app uses, so login/timer work immediately — no setup
needed.

```bash
npm run build     # production build → dist/ (minified, chunked)
npm run preview   # preview that build locally
```

Deploy `dist/` to Netlify exactly like before (same drag-and-drop or
git-connected flow works — it's still a static site).

## What's fully migrated and working

- **Auth** — new account + recover account (ID or phone), identical
  password hashing (`hashStr`/legacy fallback) so existing accounts in
  Supabase log in without any migration needed.
- **Focus / Timer** — command bar, quick-fire presets, the ring timer,
  pause/resume/reset/skip, the 30-second heartbeat + stale-session
  cleanup, localStorage session persistence (survives a refresh),
  session log, today's stats. This is a full port of the original
  timer logic, not a rebuild-from-scratch — same absolute-timestamp
  architecture, same bug fixes (rolling heartbeat anchor, instant
  local patch before DB round-trip, etc.)
- **Design system** — every CSS custom property, every component
  class, the font stack, and the Ultra/AERO/Clean theme + accent-color
  + font overrides, copied over as-is in `src/styles/global.css`.

## What's a placeholder ("SOON" in the sidebar)

Analytics, Activity, Clan/Leaderboard (+ Realtime), Planner, Mission
Control, Achievements, Notice Board, Community, Settings, Admin.

These all exist in the old HTML file and will get migrated the same
way Focus was — one feature at a time, so each one is verified working
before moving to the next, rather than shipping a giant untested batch.

## A couple of things worth knowing

- **Particle background** is a faithful recreation of the effect
  (same particle counts, mobile optimization, connecting lines) — not
  a byte-for-byte copy of the original canvas code, since decorative
  animation isn't worth the risk of guessing at logic I couldn't fully
  verify from the source.
- **Session-complete celebration** is a simpler card for now (no
  confetti canvas / level-up overlay yet) — the underlying stats are
  accurate, the fireworks are a follow-up.
- **Command bar layout** (the 3-field subject/task/minutes row) was
  reconstructed from the original's element IDs and CSS, since the
  exact markup wasn't something I could pull verbatim — functionally
  identical, but flag it if the spacing looks off vs. what you remember.
- **`.env` has the real Supabase key already** — that's fine, it's the
  same anon key already sitting in the shipped HTML file today (anon
  keys are meant to be public; protection comes from RLS policies, not
  secrecy). `.env` is gitignored either way.
- **PWA icons** — `manifest.json` points at `/public/icon-192.png` and
  `/public/icon-512.png`, which don't exist yet. Drop your icon files
  in `public/` with those names, or tell me and I'll generate them from
  the existing favicon.

## Folder structure

```
src/
  main.jsx, App.jsx
  lib/            supabaseClient.js, sessions.js
  contexts/       AuthContext, ThemeContext, ToastContext
  components/     ParticleBackground, layout/ (Sidebar, Topbar, AppShell)
  features/
    auth/         LoginScreen
    focus/        FocusPage, useTimer
  styles/         global.css
```

New features go in their own `features/<name>/` folder, same pattern
as `focus/`.
