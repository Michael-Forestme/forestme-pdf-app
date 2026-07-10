[README.md](https://github.com/user-attachments/files/29896759/README.md)
# Forestme — Daily Site Report

The daily site report web app used by Forestme (Liquid Forest Pty Ltd) team leaders.
Served at **reports.forestme.io** via GitHub Pages.

A team leader fills in one report per day on their phone, adds photos, gets the
client to sign on screen, and taps **Send to client**. The finished PDF is emailed
straight to the client (with a copy to the office and an archive copy in Google
Drive) — no manual filing, no oversized attachments.

---

## What's in this repo

| File | What it is |
|------|-----------|
| `index.html` | **The entire app.** One self-contained file — HTML, styling, logo, and all the code are inside it. This is the only file that serves the site. |
| `README.md` | This document. |
| `.nojekyll` | Tells GitHub Pages to serve `index.html` as-is (add it if the site ever fails to build). |

There is no build step and no dependencies to install. The file runs as-is in any
modern phone or desktop browser.

---

## How it works (the whole system)

1. **The form** (`index.html`) — captures job details, a 15-point Take 5 safety
   check, quantities, extra hours (stand-down / labour / inductions), works
   completed, issues, notes, up to 10 photos, and an on-screen client signature.
2. **Photo compression** — every photo is automatically shrunk before it goes into
   the PDF, so a full report emails cleanly (around 1–3 MB instead of 30 MB+).
3. **The PDF** — built in the browser, branded, and named
   `Forestme Daily Report - {Project} - {Date}.pdf`.
4. **Send** — the PDF is posted to a Google Apps Script endpoint, which emails it
   to the client, CCs the office, and saves a copy to a Drive archive folder.

The email routing and archiving live in the Apps Script, **not** in this repo.

---

## Editing the app

The only thing most edits touch is the **CONFIG block** near the top of the
`<script>` section in `index.html`:

```js
const ENDPOINT = 'https://script.google.com/macros/s/.../exec';  // Apps Script URL
const SECRET   = 'CHANGEME';   // must match the SECRET in the Apps Script
```

- **`ENDPOINT`** — the deployed Apps Script web-app URL. Only changes if the script
  is redeployed to a brand-new deployment.
- **`SECRET`** — a shared password that must match the `SECRET` in the Apps Script.
  This is what stops anyone else's request from using the endpoint.

Other easy tweaks further down the CONFIG block: the Take 5 checklist wording, the
weather options, and the number of quantity rows shown on load.

### Editing on GitHub (recommended)

1. Open `index.html` in this repo.
2. Click the **pencil (Edit)** icon.
3. Make the change (e.g. find `CHANGEME`, type the real secret between the quotes).
4. Click **Commit changes**.

GitHub's editor always treats the file as code, which avoids the smart-quote and
"preview vs. code" problems you can hit editing HTML in a desktop text editor.

---

## Deploying (updating the live site)

The live site updates automatically whenever `index.html` on the `main` branch
changes. To publish a new version:

1. **Add file → Upload files**, drag in the new `index.html` (same name replaces
   the old one), and **Commit changes** — *or* edit `index.html` directly with the
   pencil icon and commit.
2. Wait 1–2 minutes for GitHub Pages to rebuild.
3. Open **reports.forestme.io** in a private/incognito tab (to skip the cached old
   version) and confirm the change.

If the page ever shows up **unstyled** (plain text on a plain background), the file
didn't land as `index.html` at the repo root — check the filename and location.

---

## Before going live: the SECRET

The app ships with `SECRET = 'CHANGEME'`. It must be changed to the real secret
phrase — the exact same one set in the Apps Script — before the Send button will
work. Set it via the pencil-edit method above. If the secret is ever exposed,
change it in **both** places (this file and the Apps Script) and it's secure again.

---

## Testing safely

The Apps Script has a `TEST_MODE` flag:

- **`TEST_MODE = true`** — every report is emailed to the office only, no matter
  what client email is entered. Use this to trial the whole flow without anything
  reaching a real client.
- **`TEST_MODE = false`** — live. Reports go to the client email captured at
  sign-off, with the office CC'd.

After changing anything in the Apps Script (including `TEST_MODE`), republish it:
**Deploy → Manage deployments → pencil → Version: New version → Deploy.**

Recommended first run: with `TEST_MODE = true`, open the live site, fill in a full
report, sign, and Send. Confirm the PDF lands in the office inbox and the Drive
archive. Then flip `TEST_MODE` to `false` and republish.

---

## Notes

- No analytics, no cookies, no browser storage — nothing is saved on the device.
  A report exists only until it's sent; closing the tab loses an unsent draft.
- The app needs an internet connection to send (it posts to the Apps Script).
- Contact for this repo: Forestme / Liquid Forest Pty Ltd.
