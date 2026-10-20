# CARE Client Binder — Watch List & Bug Log

**Purpose:** one place to monitor recently shipped features for regressions and to log any bug found, from any chat. This is the monitoring chat's home base.

**How to use:**
- Skim "Watching" after a release; if the team uses those areas without issues for ~a week, mark them ✅ stable in PROJECT_LOG.md.
- Found a bug anywhere? Add it under "Bugs found" with the date, what you did, what happened, and who/where. Then fix it in its own new chat.
- Safety rule still applies: never fetch/query the Supabase clinical records — Claude writes SQL, you run it.

---

## WATCHING (recently shipped — verify in real use)

### v2026-09-16-M (just shipped, highest priority)
- **View past days in School Shadow daily log** — date picker + "Recorded days…" list.
  - Watch for: picking a past day shows THAT day's saved data (not today's); past days are read-only (no Save button); "Today" button returns to today; can't pick a future date.
- **"Recorded by [name]"** in School Shadow daily log + NET section.
  - Watch for: shows the correct therapist who recorded that day; blank (not broken) when no one has recorded yet.

### Recent data/calculation changes (watch — these touch saved records)
- **Edit a saved session's time → BIP rate recalculates** (v-D/-C).
  - Watch for: after editing session length, the per-hour rate updates correctly and ONLY for that session's logs; other sessions untouched.
- **Delete a session** (v-E/-F/-G).
  - Watch for: deleting removes only that session's BIP + ABC data; a second session on the same date is left intact; blank = one confirm, data = type DELETE.
- **Tally (count) untouched no longer saves 0** (v-J).
  - Watch for: leaving a count blank records nothing (not a 0); pressing − or typing 0 still records a real zero; end-session "no data" reminder lists the untouched ones.
- **School Shadow grid + NET save together** (v-L).
  - Watch for: typed NET trials are kept when you Save day or End session; a NET entry with no Total warns instead of silently dropping.
- **QCD can record School Shadow on assigned cases** (v-K).
  - Watch for: QCD can record on assigned cases only; unassigned stays view-only.

---

## BUGS FOUND (log here, then fix in a new chat)
- [22 Sep 2026] **URGENT #2 — School Shadow wiped AGAIN after SQL restore.** Cause: (a) merge fix gap — a device that opened the app BEFORE shadow existed auto-creates an EMPTY shadow in memory; the merge treated that empty as a real change and overwrote the good server data; (b) devices on OLD cached versions have no merge fix at all. FIX: v2026-09-22-Z adds a phantom-empty guard (an empty/auto-created section can never overwrite real server data; genuine edits/deletes still work). Unit-tested. STILL REQUIRED: deploy Z to GitHub + every device hard-refresh; re-run the JAA timetable SQL after everyone is on Z.

_Format: [date] area — what you did → what happened → who/role, which client if relevant._





- [17 Sep 2026] **URGENT — School Shadow data wiped by concurrent save.** Director added School Shadow content; a therapist with an older copy of the client loaded then saved during a 1:1, and her stale whole-record write overwrote the School Shadow section (incl. supervisor observation notes). 1:1 data unaffected (separate tables). ROOT CAUSE: entire client stored as one record; `sbSaveClient` blindly overwrites the whole record from in-memory state (no merge, no re-read). FIX: make client save re-read latest row and merge only changed sections. STATUS: FIX BUILT + 2-device test PASSED (v2026-09-17-O, section-level merge in sbSaveClient). Wiped JAA data will NOT be restored in-app — team re-enters YTD + today into Google Sheet and starts fresh in the app tomorrow. TODO before rollout: deploy v-O to GitHub. NET targets (Listening Comprehension, Transition) SQL ready if wanted.

---

## RESOLVED
_Move fixed bugs here with the version that fixed them._

- [20 Oct 2026] **Notes from one visit split across two days → Day report misses some** (reported by a supervisor). Cause: general/supervisor notes (`saveNote`) use the visit/session date (`getActiveState().date`), but program notes, SD notes, behavior notes, comm notes, School Shadow notes, mastered/hold/step stamps use today's date (`_todayLocal()`). If the visit date ≠ today (back-dated visit, or a visit left open overnight — daily sign-out is skipped while busy), notes land on two days. (Also pre-v-AX: saves before 07:00 were dated the previous day.) Proposed fix: everything saved during an active visit/session uses that visit/session date; warn when continuing a visit dated a different day. Existing notes not moved. **FIXED in v2026-10-20-BH (`_workDate()` + 'Recording for <date>' notice). Notes already split were not moved.**

- [17 Oct 2026] **App only loads the latest 500 sessions across ALL clients** (also 1,000 behaviors, 500 ABC) in `loadFromSupabase`. With ~20 cases that is only ~5–6 weeks of history: older sessions are safe in the database but missing from long graphs, Print BIP ranges, stimulus history, etc. Grows worse with more data. Fix (own chat, before year-end): load each client's full history when the client is opened (per-client fetch), keep the global load small. Read-only change to loading; test carefully that saves/merges still use complete data. **FIXED in v2026-10-17-BD (per-client loading).**

- [15 Oct 2026] **Dates recorded in world time (UTC), 7 h behind Thailand** — most date stamps use `new Date().toISOString().slice(0,10)` (mastered date, step changes, program/SD notes, etc.), so anything done between 00:00 and 07:00 Thai time is saved with YESTERDAY's date and lands on the wrong day in the Day report / graphs. Rare during clinic hours. Fix (own chat): switch those stamps to the local-date helper `_todayLocal()` (already used for the new hold/resume dates in v-AR). Low risk, but touches many save points — test carefully. **FIXED in v2026-10-16-AX.**
- [16 Oct 2026] **Sup Notes tab → "Notes Feed" mixes in therapist (TX) session notes** — the feed in `renderNotesTab` merges supervisor general notes with every session's TX note (`// ── Notes feed — merge sup notes + session TX notes ──`). Since TX notes already have their own place (Team Notes / session notes), this feed should show **supervisor notes only**. Change: drop the session-TX-notes part of the merge (display only — no data is deleted; TX notes stay in their sessions). Check nothing else (e.g. print / Day report) relies on this feed. Own small chat. **FIXED in v2026-10-16-AX.**

- 

- **19 Sep 2026 · rating graph (session mini-graph)** — for a rating that is a *prompt hierarchy* (1 = Independent/best → 5 = Full Prompt), the graph draws the green "mastery" line at the max (5 = worst) and shades the whole area, so it reads backwards. Needs a decision: mark scale direction (lower-is-better vs higher-is-better) per rating target, then point the mastery line + fill the right way. Own chat (chart logic, not layout).

- [23 Sep 2026] **Front page (client list) cut off on iPhone** — on iPhone the front/landing page layout runs off the side and has to be scrolled horizontally; content is clipped at the screen edge. Wants it adjusted to fit the phone width and look better (no side-scroll). Mobile-layout / presentation. Own chat (part of the Mobile Layout project — Stage 5 remaining tabs / front page).

- [23 Sep 2026] **Phase-change markers land on different days: line graph vs first-trial dots** — the first-trial strip places each step-change marker (S2/S3/S4) by its DATE (correct). The line graph places it by a stored `sessionCount` and clamps with `Math.min(count, points-1)`, so a marker whose count exceeds the visible points is pinned to the LAST point (e.g. Step 4 jumps to the far right instead of its real June date). Compounding: the 
- [23 Sep 2026] **Phase-change markers land on different days: line graph vs first-trial dots** — the first-trial strip places each step-change marker (S2/S3/S4) by its DATE (correct). The line graph places it by a stored `sessionCount` and clamps with `Math.min(count, points-1)`, so a marker whose count exceeds the visible points is pinned to the LAST point (e.g. Step 4 jumps to the far right instead of its real June date). Compounding: the percent/rating graph timeline only spans days where percent/rating was recorded, while the dots include earlier first-trial-only days, so an early step change has no slot on the graph. Fix (own chat): position graph markers by DATE (match the dots), use `sessionCount` only as a same-day tie-breaker, and drop/pin-to-edge out-of-range markers instead of clamping. Note: `sessionCount` was added on purpose to disambiguate two sessions on the same date, so do not simply remove it. Two graph renderers affected (buildChartData + the Program-Edit target chart).

- [28 Sep 2026] **URGENT #3 — School Shadow data loss AGAIN, now the whole SETUP is gone (not just TX daily records).** Despite the v-Z phantom-empty guard, v-AE abort-on-failed-read, and v-AF drafts, the School Shadow *configuration* itself (subjects/timetable, NET targets, setup — not only the daily grid logs) is disappearing. Worse than before: previously only a section's records were overwritten; now the entire School Shadow setup vanishes. Cause unknown — needs its own URGENT investigation chat. Candidates to check: (a) a device still on an old cached version with no/partial merge; (b) the phantom-empty guard mis-classifying a populated setup as "emptyish" and letting a blank overwrite it; (c) a School Shadow write path that does NOT go through sbSaveClient's merge; (d) setup stored somewhere the merge doesn't cover. Do NOT fetch/query PHI — write SQL for Songg to run to inspect/restore. This is top priority over new School Shadow features. **ROOT CAUSE FOUND + FIXED (v2026-09-28-AH):** the end-of-1:1-session background sync `flushSave` wrote whole client records directly, BYPASSING sbSaveClient's merge/phantom-guard. A therapist with a stale/empty copy wiped the whole School Shadow setup on ending a 1:1. Fix routes flushSave's client writes through sbSaveClient. Unit-tested. STILL REQUIRED: deploy AH to GitHub + update EVERY device (old cached tabs still run the unsafe flushSave); then re-run the client's School Shadow setup SQL.
