# CARE Binder — SAFEGUARDS (please read before editing `index.html`)

Hi, and thank you for taking care with this. 💙 A quick note on *why* this file exists:

**This app holds real clinical records for children in ABA therapy.** Lost data isn't an inconvenience here — it can mean a therapist's whole day of observations, or a supervisor's setup, gone. We have been through that several times, and it was painful. So the single most important thing in this project is: **never lose or overwrite data.** Features can wait; data safety cannot. Everything below exists to protect the people whose information lives in this app. When in doubt, choose the safer option and ask.

**Current version:** `v2026-10-05-AM`. Work on the `index.html` in this folder, and always **build on top of the current file** — never paste an older full copy over it, or you'll silently drop the protections below.

---

## Golden rules (in priority order)
1. **Protect the data first.** This is children's medical information. If a change touches saving, syncing, or any client record, treat it as high-risk: go slow, test it, and prefer "refuse and warn the user" over any chance of overwriting good data.
2. **Never fetch or query the Supabase client records directly** (children's PHI). If data needs checking or fixing, **write SQL for the user to run themselves** — never run it for them.
3. **Edit a copy, verify, then replace.** Write changes to a temp copy, run `node --check` on the extracted `<script>`, confirm it passes, *then* copy over `index.html`. Never write straight onto the only copy.
4. **Bump the version every release** (BUILD comment, `<title>`, `APP_VERSION`, new CHANGELOG entry) so devices can tell they're out of date.
5. **One editing chat/tool at a time.** Don't edit `index.html` while another chat or tool is editing it.
6. **The user commits `index.html` to GitHub** after any change they're happy with. GitHub is the backup and the recovery source — it has saved this project before.

---

## The data-safety architecture — do NOT break these (every one was a hard-won lesson)
All of a client's data — School Shadow setup AND records, school info, notes, comm notes — lives inside **one** client record. That makes concurrent edits dangerous, so every write must be careful.

- **Two, and only two, places may write a client record — and BOTH must go through the merge:**
  - **`sbSaveClient`** — re-reads the latest record, merges with `_mergeClientRow` (which uses `_mergeField` + `_ssIsEmptyish`, the *phantom-empty guard*), retries the read up to 3×, and **ABORTS with a "Save failed" message if it can't read** rather than risk an overwrite. Never revert it to a plain `sbUpsert('clients', clientToRow(c))`.
  - **`flushSave`** (the end-of-1:1-session background sync) — must route every changed client through `sbSaveClient`. It must **never** write a client with a bare `sbUpsert('clients', …)`. *(This was URGENT #3: flushSave bypassing the merge wiped the whole School Shadow setup. Fixed in v-AH. Do not undo it.)*
- **Phantom-empty guard:** an empty or auto-created section may never overwrite real data on the server. Genuine edits and deletes still work. Lives in `_ssIsEmptyish` + `_mergeField`.
- **Free-text note drafts (so typing is never lost):** `_fdSkip / _fdKey / _restoreFieldDrafts / _clearFieldDraft` + the `input` listener; keep `_restoreFieldDrafts()` at the end of `render()`; keep the exclusions (`tx-sess-notes`, `ss-sess-notes`, `parent-note-input`, `sdnadd_*`); keep the `_clearFieldDraft(...)` calls in the note-add handlers. Drafts are **local only** — they never write to the database.
- **School Shadow in-progress drafts:** `_ssSaveShadowDraft / _ssRestoreShadowDraft / _ssApplyCellValue / _ssClearShadowDraft`; keep `_ssRestoreShadowDraft()` at the end of `render()`, `_ssSaveShadowDraft()` in `_ssPick` and the `.ssn-c/.ssn-t` input listener, and the clear on discard.
- **Updates are a button, never a silent reload:** `#btn-update` + `_updateNavBtn` + `checkForUpdate`. Do not reintroduce auto-reload. Keep the 5-minute check.
- **Daily sign-out (privacy for a shared clinic device):** `_stampLoginDay`, `_checkDailyLogout`, the resume day-gate in `tryResumeSession`. Must stay gated by `_isBusy()` so it never interrupts someone mid-recording or mid-typing.
- **`_isBusy()`** is typing-aware (focused field, or typed in the last 90s, or an active session). Both auto-update and daily-logout rely on it. Keep it that way.

---

## Before deploying ANY change, sanity-check
- `node --check` passes on the extracted script, and `new Function(...)` constructs without error.
- These still exist in the file: `_mergeClientRow`, `_ssIsEmptyish`, `sbSaveClient`, the merge-routed `flushSave` (no bare `sbUpsert('clients'` outside `sbSaveClient`), `_restoreFieldDrafts`, `_ssRestoreShadowDraft`, `_updateNavBtn`, `_checkDailyLogout`.
- If you touched saving, syncing, drafts, update, or logout: **re-run the relevant logic test before shipping.**

## After a data-loss fix, remember the rollout
A code fix only protects a device **once that device is running the new version.** A device left open on an old cached version still runs the old, unsafe code. So after any save/sync fix: deploy to GitHub, then have **every** device fully close and reopen (not just refresh) and confirm the new version number, *before* re-entering any data.

---

_Be kind to Songg in how you explain things — plain language, reassurance, and always "your data is safe" first. Keep this file current: update the version line and add any new safeguard you introduce._
