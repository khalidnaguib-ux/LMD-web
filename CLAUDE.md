# LMD web: instructions for Claude

This repository is the **LMD web** app: a private Arabic/English guard-shift roster, one self-contained `index.html` on GitHub Pages. Firebase project `lmd-roster` (Google sign-in + Cloud Firestore). It is the TEST version: new features are tried here first. It now has the WhatsApp shift-report reader, the supervisor form link and the Shift start card (v58). LMD web2 (a different repository) is the STABLE version the user depends on; it only changes on request.

## Rules for every change
- Work on a branch and open a pull request. Never edit other apps' code or configs here.
- Keep the firebaseConfig exactly as it is in the file.
- Every update raises the version number in THREE places: the page `<title>`, the header line under the month name, and the login-screen title (format `LMD web vNN`). Read the current number from the file and add 1.
- Keep the file a single `index.html`. Do not add build tools.
- The file starts with a stray short HTML document (`<!doctype html><html><head>...</head><body>`) before the real one. Put icon / meta tags in that FIRST `<head>`.
- Icon files `apple-touch-icon.png` and `icon-512.png` sit next to `index.html`.

## Facts
- Business day runs 9:00 am to 9:00 am next day: use `bizNow()` (now minus 9 hours) for "today" and period logic.
- Pay period = 26th to 25th (`days()`, `pid()`). Status codes: D, N, DS, V, Z, C, A, S, SM, VDS (shown "V/DS"), R (OUT).
- Roster data is stored sharded in Firestore (manifest doc `roster` plus `r-<name>-<i>` chunks) because of the 1 MB document limit and no nested arrays. Never store the roster as one document.
- Home screen is the Today dashboard (cards) with a bottom tab bar (Today, Roster, Pay, Forms). D and N counts there show guards (فرد) only on the ground in that shift; a DS (day + night on the same date) counts in both D and N; DS shows how many did a double shift. A guard set as acting supervisor (قائم بالعمل, RO.f.act[date_d|n]) is not counted as a guard in that shift.

## Payroll rules (keep unless the user changes them)
- unit = net / 30. While the period is open, earned net = days from joining day (or period start) up to the last marked day; after the period ends, all days from joining count.
- Leave: 5 days per full month; late joiner earns floor(days attended / 6). Unused leave pay and extra Double Shifts (DS minus V/DS) = unit x 1.5. Unused leave counts only after the period ends; DS counts as it happens.
- Absence: single A = 2 units; 2-5 consecutive = 2 per day + 1; more than 5 = OUT.
- S and SM are paid 100% and do not use the leave balance.
- Signed penalty forms deduct: خصم يوم = 1 unit, نصف يوم = 0.5, N أيام = N units.
- Compensation (500 default, supervisors and guards): written only after the period ends, with no A, no signed penalty, and not a late joiner.
- Joining: the first D/N of an added employee sets the joining date on the contact card; deleting it resets the rules.

## Testing before you finish
- `node --check` every inline script block.
- Run the logic in a Node `vm` sandbox with a stub DOM and a fake `Date` to test payroll and dashboard numbers.
- Take headless Chrome screenshots at 390x844 (light and dark) for visual changes.
- Summarize what changed in a few plain sentences.

## Icon
The test icon is inverted (black background). Files: `apple-touch-icon-test.png`, `icon-512-test.png`. Home-screen name: LMD TEST.
