# CARE Client Binder — Project Log

**The app is `index.html` in this project.** It's a single-file web app (ABA therapy data for CARE Learning Center), backed by Supabase, hosted on GitHub Pages at https://carethailand.github.io/aba-binder/.

> **New chat? Start here:** read this file and `index.html` first. Current version is in the CHANGELOG inside `index.html` (search `const CHANGELOG`). One chat = one feature. When a feature is shipped, commit it to GitHub and update this log.

---

## Current version
**v2026-09-16-N** — shipped, on trial (see below). Full history is in the CHANGELOG inside `index.html`.

---

## Safety rules (never break these)
- **Never fetch or query the Supabase clinical records** (children's PHI). If data needs checking, Claude writes the SQL and *you* run it yourself.
- **Edit a copy, verify, then replace** the real file. Never write straight onto the only copy. (This is what caused the one file loss — now avoided.)
- **Commit to GitHub after every version you're happy with.** Each push is a checkpoint you can't lose. GitHub is our recovery source.
- **Version bump every release:** the `<!-- BUILD -->` comment, `<title>`, `const APP_VERSION`, and a new CHANGELOG entry.

---

## How we work
1. One chat per feature — keep chats short and focused (cheaper + clearer).
2. Build -> Claude verifies (syntax + logic) -> you deploy -> **commit to GitHub** -> update this log -> start a fresh chat for the next thing.
3. **Capture, don't chase:** anything that comes up mid-way (bug, idea, request) goes in the Inbox below. Don't fix it in the current chat.
4. Triage the Inbox:
   - **Broken in real use** -> jumps the queue, gets its own new chat.
   - **Idea / improvement** -> stays in Future until you pick it.
5. "Shipped" = deployed and works once. "Stable" = survived a few days of real team use with no complaints.

---

## Two checklists (the whole system)

### When you START a new chat
1. First message: **"Read PROJECT_LOG.md and index.html before we start."**
2. Tell me the ONE feature or fix for this chat.
3. That's it — we work on just that one thing.

### When we FINISH a feature (I will remind you at this point)
1. I verify it (syntax + logic) and deploy the new version to your folder.
2. **You commit `index.html` to GitHub** (this is the checkpoint — do not skip).
3. Move the item in this log: Future/Inbox -> In trial, and update the version line.
4. Commit `PROJECT_LOG.md` too.
5. **Archive/close this chat and start a fresh one** for the next thing.

> Note to Claude (future chats): when a feature is shipped and deployed, proactively stop and walk Songg through the "When we FINISH a feature" checklist above. Songg is new to this workflow and has asked to be reminded every time.

---

## INBOX (unsorted — anything new lands here first)
_Jot it and move on. Sort later._
- 

---

## IN TRIAL (shipped, watching in real use)
- **View past days in School Shadow daily log** (v-M) — date picker + "Recorded days..." list; past days are read-only.
- **"Recorded by [name]"** in the School Shadow daily log and NET section (v-M).
- **School Shadow readability** (v-N) — colour-matched C/P/−/N/A legend chips + plain-language Daily % note; tidier NET cards (% pill + labelled 80%-mastery trend graph); "Recorded by" badge. _(commit v-N to GitHub if not done.)_

---

## STABLE (shipped and proven)
Everything through **v2026-09-16-L** — full details in the in-app CHANGELOG. Recent highlights:
- Edit a saved session's time; BIP rate recalculates from the corrected session length.
- Edit / delete session icons in the Program Checklist (therapists on own sessions; supervisors/directors on any).
- Delete a session (blank = one confirm; with data = type DELETE); removes only that session's data.
- Unified note styling (bold labels + bullets) across all note areas.
- School Shadow: time-first grid, header wrap fix, grid + NET save together, QCD can record on assigned cases.
- Tally (count) targets no longer save a misleading 0 when untouched.
- Team Notes no longer duplicates School Shadow observation notes.
- Progress report can include a School Shadow summary.

---

## 📱 MOBILE LAYOUT PROJECT (iPad / iPhone) — multi-stage, IN PROGRESS
**Goal:** every tab reads cleanly on iPhone + iPad so therapists can record *in-session* and stop the paper → retype double-entry.
**Playbook:** follow `CaRe_mobile_layout_checklist.md` (safeguards + save-capture tests + staged plan).
**Presentation-only** — do NOT change save/sync logic or input IDs / data-attrs / handler names. (Exception: the version-bump edits — BUILD comment, `<title>`, `APP_VERSION`, `CHANGELOG` — are metadata, not logic, and are expected each release.) Test every stage on the **demo client**.
**Priority tabs:** Session (recording) and BIP — worst on mobile. **Tab style:** scrolling pill tabs (fewest taps to record).
**Design approved in mockups:** nav + session card + pill tabs; session/lesson cards (TX + SUP); BIP tab (TX + SUP); + collapsible BIP sections for therapists.

Stages — all worked in ONE build chat, in order. **Checkpoint after each stage before the next:** version bump → Songg verifies → test on the **demo client** (save → reload → confirm) → **GitHub commit**. Each commit is a rollback point.
- [ ] Stage 0 — GitHub commit checkpoint of current version.
- [ ] **Stage 1 — Shared shell (CSS-only): scrolling pill tabs, session/supervision card, segmented 1:1/Shadow toggle, roomier cards, stacked field rows, bigger tap targets.  ← START HERE**
- [ ] Stage 2 — Session / supervision card polish.
- [ ] Stage 3 — Lesson / session recording cards, per data type. ★ top priority.
- [ ] Stage 4 — BIP tab + collapsible therapist sections. ★ priority.
- [ ] Stage 5 — Remaining tabs (School Shadow polish, Case Info, ABC, notes, Report).

---

## FUTURE / IDEAS (discussed, not built)
- **Observation narrative box** — one free-form note on the School Shadow tab using `Label:` / `- bullet` / `Rec:` conventions (design already approved).
- **Finish-supervision report** — reorganize what prints by supervision type.
- **Client-wide lesson-graph phase breaks.**
- **Promote-to-next-academic-year** for clients/lessons.
- **Independent coverage warnings** (flag gaps in coverage).
- **Rich-text notes** (formatting in note fields).
- **Stimulus note on rating targets.**

---

_Last updated: 18 Sep 2026 (v2026-09-16-N shipped; Mobile Layout project queued — Stage 1 next)._
