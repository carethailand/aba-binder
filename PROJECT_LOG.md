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

## TO DO — next work session (added 9 Nov 2026)
_Wish-list from Songg, 2–9 Nov 2026. Nothing built yet. Answer the ❓ first, then build one at a time._
- [x] **Bug: Print "Include lesson data & graphs" shows nothing for non-% targets** — 14 Nov: Songg confirmed lesson data now shows (after BX/BY update). Maintenance + DT added in CA. (reported 9 Nov). 14 Nov: tested Day report with fake rating/tally/TA/First-Trial data, and all printed with graphs. Found and fixed (BX): rating showed as "75%" instead of "rating 4/5". Not covered at all by the Day report: DT-phase lesson data (`ld.dtData`), maintenance +/P/− probes, and School Shadow daily data. Need Songg's example. Rating / tally / TA / First-Trial-only targets print no data or graph, % targets do. Day report code (`_dayReportHtml` → `_drTdVal` + `_drTargetChart`) does have branches for each type, so the cause is still unknown. Test with fake data on port 8767 first. ❓ Which print: Report → Day report, or Finish & Preview? Which target type did you see it on?
- [x] **School targets summary + graphs since last observation** (14 Nov). Done in v2026-11-14-CC: "School targets" block (current targets + mastered / new / on hold / resumed since last observation, with dates); school graphs + monthly overview cover since the last school observation (latest date of anything made in a school visit, cap 3 months; else 4 weeks). New school/NET targets store addedDate/addedKind (older targets have none, so they never show as "new"). School notes get a heading.
- [x] **School report fixes — Step A** (14 Nov). Done in v2026-11-14-CB: BIPs follow visit type (School = School Shadow BIPs, Overlap = 1:1, Clinic/All = both); School Shadow tick box adds school BIPs + ALL that day's school notes whatever visit they were written in; empty Lessons hidden on School reports. School data window: changed to since last observation in CC.
- [x] **School observation notes — Step B** (14 Nov). Done in v2026-11-14-CD: School Shadow tab has a Recommendations box and "Observation notes by subject" (subjects from the timetable + "+ Add subject"; each card opens/closes, all start closed). Notes and recommendations are written only by supervisors during a School observation visit; edit/delete = admin/supervisor/director. Newest first in a scrolling box. Stored note by note (ss.snotes / ss.recs / ss.obsSubj maps, merged piece by piece like tnotes). Day report (type School or All, or School Shadow box ticked) + Finish & Preview: School notes → Recommendations → School targets → target updates → data → Observation notes by subject (that day only). Supervisor school notes also became one scrolling box; deleting one now asks first. A custom subject can be removed only while it has no notes. Finish & Preview change count now includes School Shadow changes (snapshot `school` part via `_ssSnap` / `_ssSupChanges`; visits started on an older version skip it), so the therapist notification counts them too.
- [ ] **School Shadow archive (end of academic year)** (added 14 Nov). Like the 1:1 archive, which clears everything after archiving. Need to work out how for school: timetable, school targets/NET, checklist data, school notes, subject notes, recommendations, school BIP? What is kept/viewable/printable afterwards, and how the new year starts. ❓ Think through with Songg before building.
- [x] **School notes: one scrolling box instead of a closed card per visit** (idea 14 Nov; done in v2026-11-14-CD). Today each supervisor school note is its own closed card. Idea: one box, newest note at the top and open, older notes below with a scroll bar (like the "What's new" update panel). Same idea could apply to the subject cards in Step B.
- [x] **Subject cards: one subject per card** (14 Nov, v2026-11-14-CE). Timetable boxes split at "/" into separate subjects; when a box names more than one class code, only the child's class from Case Info → Class / Room is kept; class codes and "both" removed; no duplicates. If Class / Room has no class code, all parts show. Timetable data unchanged (`_ssCellSubjects`, `_ssClassCode`). Rule: CHANGELOG / log / commit text never quotes a real child's timetable, class or subjects (16 Nov).
- [x] **Lesson graphs missing on clinic reports** (19 Nov, v2026-11-19-CG). Cause: a target's graph printed only when there was session data on the report's exact day; also sessions saved on another device after the client was opened weren't on this device. Fix: with "Include lesson data", every target with data in the period prints ("no data on this day · last <date>" + graph); Clinic/All reports go back at least to the last clinic visit (`_drLastObs`, max 3 months); Day report and Finish & Preview fetch new sessions first (`_drFetchNewSessions`, adds only, never replaces).
- [x] **Day report: DT, teaching steps, maintenance, stimuli** (14 Nov). Done in v2026-11-14-CA: lesson data prints DT-phase lessons (step, FT, c/t, 4-week graph, last 4 FT) and the teaching step on each target line; new tick box "Include maintenance probes & stimuli checklist" = last 3 probes per mastered target + that day's stimuli marks (moved out of lesson data).
- [x] **School Shadow data in the Day report** (14 Nov). Done in v2026-11-14-BZ: tick box "Include School Shadow data & graphs" (Day report + Finish & Preview; auto-ticked for School observation). Per school target: today by subject (C/P/−), % independent, 4-week graph + dashboard (by subject, by time of day, hardest). Per NET target: today c/t %, 4-week graph.
- [x] **BIP summary in every Day report** (9 Nov). Done in v2026-11-14-BY. Every BIP and setting listed daily, with the last recorded value, 4-week days/average/highest/goal, and the trend. Trend = best-fit line, ±20% of the average; needs 4+ days; only data after the latest break or plan change; "Variable" if very scattered. Also First Trial "last 4" in lesson data. Today's BIP data for that day (count / rate / duration per behavior, 1:1 and School), not only in Print BIP.
- [x] **BIP text: always bold the "Name:" part** — done in v2026-11-11-BW, even when nothing follows the colon. App and print (shared `_bipBlock`).
- [x] **BIP edit boxes too small; can't scroll on iPad.** — done in v2026-11-14-BX (plan boxes grow with text; Add/Edit BIP window scrolls as one page). Idea: boxes grow as you type, and the form scrolls (maybe full-screen on iPad). ❓ Is it the supervisor's Edit BIP form?
- [ ] **Phase-change line for sick/absent, "click all, draw all".** ❓ Guess: one button draws a labelled line on every graph for that date. Gap too?
- [ ] **Trial count = hard minimum.** Today the Total trials box has a fixed `min="5"` that isn't enforced (3 still saves) and clashes with a supervisor setting of 3. Plan: the supervisor's number is the minimum, block saving below it with a clear message, remove the fixed 5. ❓ Can a TX type MORE than the set number?
- [ ] **Maintenance criteria?** Today maintenance targets have none: +/P/− per date, they stay until Reintroduce/Archive, nothing auto-removes them (the team thought otherwise). ❓ Want e.g. "3 + in a row → ask the supervisor to archive"?
- [ ] **Checklist: where do notes go + show SD/R on the target card.** ❓ Which checklist: First-Trial (C/P/I/N/A) target, Program Checklist tab, or School Shadow checklist?

### Idea under evaluation — Parent view / parent accounts (3 Nov 2026)
Risk first, nothing to build yet. The biggest risk is a parent seeing another child's data, because every table would need a per-child rule. Also: the app loads all clients at start, staff-only notes, custody/who approves access, PDPA consent. Suggested stages:
1. Staff send a parent summary (no login).
2. A read-only parent page fed by a narrow server function (one child, approved fields only).
3. Full parent accounts, only after the "therapists see only assigned clients" database rule.

❓ What should parents see, and who approves and removes access?

---

## FUTURE / IDEAS (discussed, not built)
- **Discharged case management** — *requested 17 Oct 2026, design proposed.* Supervisor/director marks a client "Discharged" (date + optional reason) → client moves to a collapsed "Discharged" section, read-only, hidden from therapists' lists; "Export full record" (Archive printout + data file); "Reactivate" if they return. Permanent removal is never an app button — SQL for Songg to run only after the export is checked (check clinical-record retention rules first). Open question: can therapists still view discharged clients read-only?
- **Storage & loading (measured 17 Oct 2026):** database ≈ 2.9 MB of 500 MB (327 sessions since 12 Jun 2026; 141 in the last 30 days) with only ~10% of clients on the app. Space is fine even at full rollout (~50–100 MB/yr). **URGENT before adding more clients:** the app loads only the latest 500 sessions across ALL clients → passes the limit ~late Nov 2026 at today's pace, and at full rollout (~1,400 sessions/month) would show only ~10 days of history; it also downloads everyone's data at every login/reload, which at full rollout could exceed the free plan's monthly download allowance (~5 GB — check Supabase Usage). Fix = per-client loading — **built in v2026-10-17-BD**. Next: Discharged case management; then database rule so therapists can only fetch their assigned clients.
- ~~Stimulus list (simple version)~~ — **built in v2026-10-16-AY** as the Stimuli Checklist tab (see CHANGELOG).
- **Download report as Word (editable)** — *on hold: Songg checking with the supervisor team (16 Oct 2026).* Add a "Download Word" button next to PDF in Day report + Finish & Preview. Options assessed: (A) HTML→.doc, small effort, editable text but graphs don't show; (B) A + graphs converted to pictures first — **recommended**, medium effort, test in Word for iPad; (C) true .docx built piece by piece — larger, every report change made twice. Read-only export (no data risk). Open questions: where they edit (Word desktop / Word iPad / Google Docs), whether graphs are needed in the editable copy, keep PDF button too (suggested yes).
- **Observation narrative box** — one free-form note on School Shadow (`Label:` / `- bullet` / `Rec:`).
- **Finish-supervision report** — reorganize what prints by supervision type.
- **Client-wide lesson-graph phase breaks.**
- **Promote-to-next-academic-year** for clients/lessons.
- **Independent coverage warnings.**
- **Rich-text notes.**
- **Stimulus note on rating targets.**

---

_Last updated: 10 Oct 2026 — current app v2026-10-10-AO. Security: AN (app sends each user's login with every request) + database locked to active staff only (verified: a stranger sees 0 rows on every table). Code now lives in the GitHub clone `Desktop/CARE/aba-binder`; Songg pushes with `git push` (token in Keychain, expires ~6 Jan 2027). Mobile Stage 5 started: AO = phone login screen._

## Health check plan (started 20 Nov 2026)
Ranked findings from the full health check, worked one at a time (each its own version, tested, then pushed by Songg):
- [x] Plain-language save/delete error messages; console no longer logs database replies (v2026-11-20-CH).
- [x] Deletes: "deleted" shows only after the database confirms (sessions, BIPs, BIP log entries, ABC entries, lessons, archive) (v2026-11-20-CI).
- [x] Removed the admin "Reset ALL data" button and its code (v2026-11-20-CJ).
- [x] Removed the admin "Import" button and its code (v2026-11-21-CM). Full Export + Archive/Restore Archive stay; review Restore Archive in the full audit.
- [x] Add-Client full-name warning: not wanted (Songg 21 Nov). Note: supervisors sometimes enter full names as client names, so print titles / archive file names can contain them — revisit during the access/privacy design.
- [ ] Full function audit: review every button/feature left from the early builds — is it still needed, is the logic safe, is there a better way? (Songg agreed 20 Nov.)
- [x] Client names in file names / print titles: checked 20 Nov — the app's client names are already short codes, so nothing to change. Optional idea: a gentle warning in Add Client if the name looks like a full name.
- [x] Clearer "new version available" banner under the top bar; never reloads by itself, asks first if typing/in a session (v2026-11-20-CK).
- [x] Old local note drafts: erased after 3 days and on a manual logout (asks first); automatic end-of-day sign-out keeps them; session/BIP/School Shadow/stimuli drafts are never auto-cleared (v2026-11-21-CL).
- [ ] Backups: 4-monthly archive + export of all cases; plan an extra routine export. Check Supabase plan/backups page.
- [ ] Access: therapists see only assigned clients (cover sessions / team rotation need a temporary-access design); supervisors see all but edit only their own cases (may cover as therapist); admin sees admin pages only. Needs design before building.
- [ ] Split index.html into separate files (styles, reports, lessons/BIP/School Shadow, saving last). Planned for the long holiday break, after archive. Model layout: the therapist portal (css/, js/ one file per area). Tag files with the version so old caches cannot mix.
- [ ] Admin page (Staff & Access) redesign — noted 20 Nov: role drop-down is too narrow (shows only its arrow); one chip per client on every staff row makes a very long page; old unused/legacy and test accounts clutter the list; no way to give temporary cover access (team rotation). Ideas: wider role box, search/filter by staff or client, assignment grid, cover access with an end date, a tidy-up for legacy/test accounts. Design with Songg first.
  - Admin page note (20 Nov): inactive "Legacy" accounts include former therapists who may have data on cases, so they are never deleted — only grouped under a collapsed "Former staff" section (no Upgrade/Reactivate clutter). Login is Supabase-only now (legacy accounts cannot log in). Early generic test accounts: check with read-only SQL that no records use them before hiding.

## Access rules design (decided with Songg, 21 Nov 2026 — not built yet)
Decisions:
- **Enforce in the database** (not only on screen): a therapist's login can read only assigned cases + cases they currently cover.
- **Who may grant cover:** the case's supervisor, a director, or an admin.
- **Cover length:** usually a single day; sometimes planned ahead on certain days, so a cover grant is a LIST of chosen dates (not necessarily consecutive). Access switches on only on those dates (Thailand date) and off again by itself. Records made during cover stay on the case.
- **Supervisor covering as a therapist on another supervisor's case:** may run sessions/record notes only; cannot change the program, targets or plan.
- Existing rules to keep: therapists see only assigned cases; supervisors see all cases but edit only their own; directors edit all; admin sees the admin page only.
Plan (stages, each tested and with undo SQL; Songg runs all SQL):
1. Cover access feature: `case_cover` table (case, therapist, one row per cover date, who granted), a "Cover" control on the case screen and admin page, app-side checks. No lock-down yet.
2. Database read lock: staff-only policies tightened per table (clients, lessons, sessions, behaviors, ABC, BIPs, summary view) using a helper function "can this login access this case today?". Needs an undo script saved as `(keep) UNDO`. Do AFTER the year-end archive/holiday, with a rehearsal on fake data.
3. Database write lock (supervisor edits only own cases; cover supervisors can only add sessions/notes).
Open questions: can admin see the client list (codes) to assign staff, without records (needs a names-only view)? Cover therapist visibility of earlier history on that case (assumed yes, for the cover dates).
- Cover access, more decisions (21 Nov 2026): (a) covering staff can READ the case (read-only) from a "Can read from" date, default 3 days before the first cover day, changeable per cover; recording only on cover days. (b) Juniors: no limit on which cases they can cover for now (the scheduler decides); seniors, QCD and supervisors can cover any case. A "Team" field and "previous cases" memory are possible later, not needed now. (c) The picker does not show who is free. (d) Database: new table `case_cover` (client_id, user_id, cover_date, read_from, granted_by), staff-only policy, no foreign keys.
- [x] Access Stage 1 built (v2026-11-21-CN): Cover button + calendar on the case screen, 'Covering today' / 'Read only until' tags, view-only rules for read-ahead, supervisors cover = session only. Needs the `case_cover` table (created by Songg 21 Nov). App-side only; Stage 2 (database read lock) and Stage 3 (write lock) still to do after the year-end archive.
- [x] Admin page Cover tab (v2026-11-21-CO): all planned cover from today on, by date; + Add cover (choose case first), Remove, Refresh. Directors and admins only (it lives on the admin page). Past days are not shown (timekeeping is a separate system).
- [x] Admin Staff & Access redesign (v2026-11-21-CP): search + role filter, By staff / By case, Edit cases (tick-list + Save, merged with the latest server list), collapsed Former staff, role/active changes save one column and are confirmed before the screen changes.
- [ ] Look & feel (Songg 21 Nov): she likes the binder blue and wants it consistent with the CARE logo and the Therapist Portal. Portal style = cream/sage/clay with logo blue-yellow-pink touches, serif headings, real logo image. Ideas: use the real logo image in the header, "CARE" in capitals in titles, shared fonts; calm colour themes (Calm ocean = keep blue, Sage, Lavender, Peach) then dark mode after the file split.
- [x] Look & feel step 1 (v2026-11-21-CQ): real CARE logo (assets/care-logo.png) on login, top bar (white pill) and browser tab; "CARE Thailand" wording; staff page grouped by role in separate cards; By case grouped by supervisor. Next ideas: shared fonts/rounded corners with the Portal, calm colour themes, dark mode after the file split.

## Supervisor runs School Shadow as therapist (decided with Songg, 22 Nov 2026 — not built yet)
Today a supervisor's "Start Session (as therapist)" always starts a 1:1 session, and the School Shadow daily grid / NET entry / End & Save are blocked for supervisors (`_ssCanRecord`, `_ssSaveEntry`, `_ssSaveNetDay`, `_ssSaveAll` allow therapists and QCD only).
Decisions: (1) a supervisor may record School Shadow like a therapist (grid, NET trials, notes, BIP logs, End & Save), under their own name; (2) own cases any day, another supervisor's case only on cover days (same cover rule as 1:1, no new setup; program/setup stays read-only there); (3) the "Start Session (as therapist)" bar gets the same 1:1 | Shadow switch therapists use; "Start Supervision" incl. School Observation is unchanged. School observation notes / subject notes / recommendations stay tied to a School Observation visit.
Build plan: allow `_ssCanRecord` for supervisor/director when a session on this case is active and the case is not view-only; add the switch to the supervisor start bar; test own-case, cover-day, other-case-blocked, and that observation visits are unaffected.
- [x] Supervisor runs School Shadow as therapist built (v2026-11-22-CR): 1:1 | Shadow switch on the supervisor start bar, `_ssCanRecord` gate, own case any day / other case on cover days, setup read-only while covering.
- [x] Session banner redesign, Option B (v2026-11-22-CS): equal-block grid for the 4 banner states, one badge, phone layout (compact in-progress bar), supervisor bar = Supervise | Cover as therapist panels; School Shadow setup + observation-note box hidden while a supervisor acts as therapist.
- [x] Smaller session banner + phone top bar (v2026-11-22-CT): compact sizes, supervisor banner one row on wide screens, logo no longer covers the Clients button on phone (Admin/Password/Update shown as icons).
- [ ] Open (Songg 22 Nov): (1) client list on phone/PC feels busy - redesign proposal pending (compact cards, fewer details); (2) 'hide target edit for School Shadow when logged in as supervisor' - clarify where it still shows (in a therapist-style session all target edit controls are already hidden).
- [x] Calmer client list + client page (v2026-11-22-CU): one-line rows with search, grouped by supervisor (side by side on wide screens); client page header = name + diagnosis + a ⋯ menu (Change supervisor / Give cover / Archive), quiet info line, compact graph-month control, repeated yellow notice removed. DOB no longer shown on the list.

## Notes: edit / delete audit (23 Nov 2026 - requested by Songg, not built yet)
Where notes live and what can be done today (all edit/delete need an active session or supervision visit via `requireActiveSession`):
- Sup Notes tab (`c.notes`): add yes, edit yes (supervisor/director), DELETE MISSING.
- Lesson general notes (`l.supNotes`, saved by position, no ids): add yes, edit yes (supervisor/director), DELETE MISSING.
- Target-set notes (`sd.notes`): add yes (one per person per day, re-saving overwrites), edit no, delete yes (supervisor/director).
- Behavior notes (`c.behaviorNotes`): add (sup), edit MISSING, delete yes (sup).
- Team notes (`c.commNotes`): add (therapists), edit MISSING, delete yes (author, supervisor/director/admin).
- School Shadow observation notes (`ss.notes`): add (sup/director/admin), edit MISSING, delete yes (asks first).
- School Shadow target notes, subject notes, recommendations: edit + delete exist.
- Session notes / parent note: editable via Edit last session.
Plan: add the missing delete/edit, confirm before every delete (shows the note), saves confirmed with plain failure messages, allow tidy-up without starting a visit for people allowed to edit that case, give lesson/target-set notes ids (delete by id, never by position). Awaiting Songg's answers on who may edit/delete.
- [x] Notes edit/delete built (v2026-11-23-CV) per Songg's answers (supervisors/directors any note on cases they can edit, therapists own; no visit needed for edit/delete; confirm before delete, permanent). Added: delete for Sup Notes + lesson General Notes; edit for behavior notes, team notes, target-set notes, School Shadow observation notes; behavior/team delete buttons now work without a running session.
- [x] Logout 'unsaved note' warning fixed (v2026-11-23-CW): false alarms came from pop-up/edit text boxes and Case Info notes keeping a typed-text copy that was never cleared on save; now only the real note boxes keep one (whitelist `_FD_INFO`), cleared on save, old leftovers swept; the pop-up lists each unsaved note (client, which box, start of text) with Go to this note.
