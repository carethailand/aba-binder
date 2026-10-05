# CARE Client Binder — Project Log

Welcome 👋 — whether you're Songg picking this back up, or a new chat/tool (including Claude Code) continuing the work: thank you for taking care of it.

**What this is:** `index.html` — a single-file web app for ABA therapy data at CARE Learning Center. It's backed by Supabase and hosted on GitHub Pages at https://carethailand.github.io/aba-binder/. It holds **real clinical records for children**, so the golden rule of this project is simple: **keep the data safe, above everything else.** Features can wait; data cannot be lost.

> **Starting a new chat or tool? Please read `SAFEGUARDS.md` and `index.html` first.** `SAFEGUARDS.md` explains the data-protection rules that must never be broken. Then tell me the ONE thing you want to work on.

---

## Current version
**v2026-10-05-AM** — latest. Full history lives in the CHANGELOG inside `index.html` (search `const CHANGELOG`).

> ⚠️ **Rollout reminder:** after any data-safety fix, every device must fully close and reopen the app (not just refresh) and show the new version number *before* re-entering data. A device left on an old cached version still runs the old code. Please make sure the whole team is on **v-AM** and re-enter any School Shadow setup that was lost, once everyone is updated.

---

## ⚠️ Note about this workspace (tasks being deprecated)
From **October 6, 2026**, the "tasks on this computer" mode is being retired — you won't be able to start new ones here. **This does not affect the project**, because everything lives as files in this folder and on GitHub, not inside the app. To keep working after that date, use **Claude Code** (or any tool that can open this folder): point it at this folder and have it read `SAFEGUARDS.md` first. Before Oct 6, please **commit everything to GitHub** (the `index.html` *and* these `.md` docs and the SQL) so the whole project is safely backed up and portable.

---

## Safety rules (never break these)
- **This is children's medical data — protect it first.** Any change to saving/syncing is high-risk: go slow, test, and prefer "warn and refuse" over any chance of overwriting good data.
- **Never fetch or query the Supabase records.** If data needs checking/fixing, Claude writes SQL and *you* run it yourself.
- **Edit a copy, verify (`node --check`), then replace.** Never write straight onto the only copy.
- **Commit to GitHub after every version you're happy with.** Each push is a checkpoint and the recovery source.
- **Version bump every release** (BUILD comment, `<title>`, `APP_VERSION`, new CHANGELOG entry).
- **One editing chat/tool at a time.**

---

## How we work
1. One chat per feature — short and focused.
2. Build → Claude verifies (syntax + logic) → you deploy → **commit to GitHub** → update this log → fresh chat for the next thing.
3. **Capture, don't chase:** anything that comes up mid-way goes in the Inbox below — don't fix it in the current chat.
4. Triage: broken-in-real-use jumps the queue (own chat); idea/improvement waits in Future.
5. "Shipped" = deployed and works once. "Stable" = survived a few days of real team use with no complaints.

### When you START a new chat/tool
1. "Read `SAFEGUARDS.md` and `index.html` before we start."
2. Tell me the ONE feature or fix.

### When we FINISH a feature (Claude will remind you)
1. Claude verifies + deploys the new version to your folder.
2. **You commit `index.html` to GitHub** (the checkpoint — don't skip).
3. Update this log (move the item, update the version line); commit the `.md` too.
4. Close this chat, start a fresh one for the next thing.

> Note to future chats: Songg is new to this workflow and has asked to be reminded, warmly and in plain language, at each finish step. Always lead with "your data is safe."

---

## INBOX (anything new lands here first)
- 
- **Print overlap/clinic notes for a chosen day** — let the user pick a date and print/export just that day's overlap (1:1) / clinic supervision notes.

---

## 🚑 DATA-LOSS SAGA (the hard-won history — keep it visible)
School Shadow data was wiped several times. Each time we found a different door and closed it:
- **URGENT #1 (v-O):** `sbSaveClient` blindly overwrote the whole client record → added a section-level **merge** (only write what changed).
- **URGENT #2 (v-Z):** a stale device auto-created an *empty* School Shadow and the merge treated it as a real change → added the **phantom-empty guard** (an empty/auto-made section can never overwrite real data).
- **Hardening (v-AE):** if the app can't re-read the record (flaky connection), it now **retries then aborts with a message** instead of overwriting.
- **Crash-safety (v-AF):** free-text notes AND School Shadow grid/NET now auto-save a **local draft**, so a reload/logout/closed-tab doesn't lose in-progress work.
- **URGENT #3 (v-AH):** the end-of-1:1-session sync `flushSave` was writing clients directly, **bypassing the merge** → now routes every client through `sbSaveClient`. This was the "whole setup disappears after a 1:1" bug.
➡️ There are now only two places that write a client record (`sbSaveClient` and `flushSave`) and both go through the merge. Keep it that way.

---

## IN TRIAL (shipped, watching in real use)
- **Data-safety stack (v-O → v-AH)** — merge + phantom-empty guard + abort-on-failed-read + note/grid drafts + flushSave routed through the merge. Watch School Shadow especially: setup + records should survive a therapist's 1:1 session on another device.
- **Auto-update button + daily privacy sign-out (v-AA/-AB/-AF)** — devices show an "Update ready" button (no surprise reloads); logins last the day; both wait while you're typing.
- **School Shadow readability + view-past-days + "Recorded by" (v-M/-N).**
- **Mobile Layout Stage 1 (v-Q)** and **BIP collapsibles (v-AG, Stage 4)** — see the mobile project below.
- **5 Oct batch (v-AI → v-AM):**
  - **v-AI** — Case Info: sticky "Save Case Info" bar + "Unsaved changes" cue (fixes case info being lost because the Save button was scrolled out of sight; it was never a data bug).
  - **v-AJ** — Reassign a case's supervisor: a "Sup:" dropdown in the client header (director/admin) moves the case and re-groups the Clients list. (Previously there was no way to change a case's supervisor after creation.)
  - **v-AK** — Demo Client is a shared sandbox: any supervisor can fully use it regardless of assignment (real clients unchanged).
  - **v-AL** — Maintenance tab cards stay open after recording a mark (no more reopening per target).
  - **v-AM** — Program Checklist split into In-progress / Maintenance / Hold sections; lessons move between sections automatically by phase. Hold section has a Resume button (reuses existing logic). Program Edit tab untouched.

---

## STABLE (shipped and proven) — highlights
Through ~v-L (full details in the in-app CHANGELOG): edit session time (BIP rate recalc); edit/delete session icons in the checklist; delete session (blank = one confirm, data = type DELETE); unified note styling; School Shadow time-first grid + grid/NET save together + QCD recording; tally no longer saves a misleading 0; Team Notes de-duplicated; School Shadow summary in the progress report.

---

## 📱 MOBILE LAYOUT PROJECT (iPad / iPhone) — multi-stage, IN PROGRESS
**Goal:** every tab reads cleanly on iPhone + iPad so therapists can record *in-session* and stop the paper → retype double-entry.
**Playbook:** follow `CaRe_mobile_layout_checklist.md`.
**Presentation-only** — do NOT change save/sync logic or input IDs / data-attrs / handler names (version-bump metadata excepted). Test every stage on the **demo client** (save → reload → confirm) and commit after each stage as a rollback point.
- [x] Stage 1 — Shared shell (v-Q): scrolling pill tabs, one-line top bar, compact session bar, roomier cards, bigger tap targets.
- [ ] Stage 2 — Session/supervision card polish (remaining: true segmented 1:1 / School Shadow toggle).
- [ ] Stage 3 — Lesson / session recording cards per data type. ★
- [ ] Stage 4 — BIP tab + collapsible therapist sections (landed as v-AG — verify/commit). ★
- [ ] Stage 5 — Remaining tabs (School Shadow polish, Case Info, ABC, notes, Report).

---

## FUTURE / IDEAS (discussed, not built)
- **Observation narrative box** — one free-form note on School Shadow (`Label:` / `- bullet` / `Rec:`).
- **Finish-supervision report** — reorganize what prints by supervision type.
- **Client-wide lesson-graph phase breaks.**
- **Promote-to-next-academic-year** for clients/lessons.
- **Independent coverage warnings.**
- **Rich-text notes.**
- **Stimulus note on rating targets.**

---

_Last updated: 5 Oct 2026 — current app v2026-10-05-AM. Shipped today: AI (Case Info sticky save), AJ (supervisor reassignment), AK (demo access), AL (maintenance cards stay open), AM (checklist sections). Next in mobile project: Stage 5 (School Shadow polish, Case Info, ABC, notes, Report). Still open in Inbox: print overlap/clinic notes by day. Remember to commit index.html + docs to GitHub (tasks-on-this-computer retires Oct 6 — after that continue in Claude Code)._
