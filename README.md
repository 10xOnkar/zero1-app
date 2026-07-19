# zerO 1 — Daily Growth App (v2: check-in + dashboard in one)

The full system in one installable app — no Notion, no separate dashboard:

- **Today tab:** habit check-in (auto-adapts to your habit columns), ₹ Earned Today, Tomorrow #1 → one-tap **Google Tasks** + **6–9 AM Deep Work Calendar block**, Reflection (Wins / Learnings / Problems / Energy), live streak 🔥
- **Dashboard tab:** month earned vs target with ahead/behind pace chip, cumulative-₹-vs-pace line chart, discipline bars + 30-day heatmap, earnings-by-month chart
- Data lives in one Google Sheet (`Log` tab = daily rows, `Targets` tab = monthly ₹ targets). Older v1 sheets are upgraded automatically (missing columns/tab added on first load).

**Architecture:** GitHub Pages (static hosting) → your Google Sheet (data) → Google Calendar/Tasks (native notifications on Pixel + Mac). Claude reads the same Sheet to mentor you. No server, ₹0 cost.

**Files:** `index.html`, `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png`

## Security (personal-use model)

- **Only you can sign in:** the OAuth consent screen stays in *Testing* mode, so sign-in works exclusively for the test-user emails you list — everyone else is rejected by Google.
- **No server, no third parties:** the app is static files; your data moves only between your browser and Google's APIs over HTTPS. Nothing is stored on GitHub.
- **The Client ID is not a secret** (it's public by design); the locks are the JavaScript-origin allowlist + the test-user allowlist. The public repo therefore exposes nothing sensitive — never commit anything else (no API keys, no tokens).
- **Sheet stays private** in your Drive; share it only with your own second account.
- Tokens live in the browser session only and expire after ~1 hour.

---

## Part A — Google Cloud setup (~15 min, do on your PROJECT Gmail)

1. Go to https://console.cloud.google.com → sign in with the **project Gmail** → top bar → **New Project** → name: `zero1` → Create.
2. **Enable 3 APIs:** Menu → *APIs & Services → Library* → search and Enable each:
   - Google Sheets API
   - Google Tasks API
   - Google Calendar API
3. **OAuth consent screen:** *APIs & Services → OAuth consent screen*
   - User type: **External** → Create
   - App name: `zerO 1`, support email: project Gmail → Save through the steps
   - **Audience / Test users → Add users:** add BOTH emails — the project Gmail **and** `onkarpatil.iitm@gmail.com`. Keep publishing status = **Testing**.
4. **Create the Client ID:** *APIs & Services → Credentials → + Create Credentials → OAuth client ID*
   - Application type: **Web application**, name: `zero1-web`
   - **Authorized JavaScript origins** → Add: `https://YOUR-GITHUB-USERNAME.github.io` (no path, no trailing slash)
   - Create → **copy the Client ID** (`xxxx.apps.googleusercontent.com`)

## Part B — Deploy on GitHub Pages (~10 min, project account)

1. https://github.com → sign in (project account) → **New repository** → name: `zero1-app` → Public → Create.
2. **Add file → Upload files** → drag all 5 app files → Commit.
3. Repo → **Settings → Pages** → Source: *Deploy from a branch* → Branch: `main`, folder `/ (root)` → Save.
4. After ~1 minute your app is live at: `https://YOUR-GITHUB-USERNAME.github.io/zero1-app/`

## Part C — First run (~3 min)

1. Open the app URL → paste the **Client ID** → Save & Continue.
2. **Sign in with your MAIN Gmail** (`onkarpatil.iitm@gmail.com`) — recommended, because:
   - Tasks & Calendar entries land where your Pixel and Mac already notify you.
   - The Sheet is created in your main Drive, so Claude can read it and mentor you.
   - (The project account is only the *developer/host* — the app user can be any test user.)
3. It will warn "Google hasn't verified this app" (normal in Testing mode) → **Continue**.
4. Tap **Create my Growth Log sheet** → done. Check some habits — watch "Saved ✓".

> If you sign in with the project Gmail instead: open the Sheet → Share → add your main Gmail as Editor, so Claude and your main account can see it.

## Install as an app

- **Pixel:** open the URL in Chrome → ⋮ menu → **Add to Home screen → Install**.
- **Mac:** open in Chrome → install icon (⊕) at the right of the address bar → **Install**.

## Second device

Open the app → ⚙︎ Settings → paste the same **Client ID** and **Spreadsheet ID** (from the sheet URL) → Save & reload → Sign in.

## Daily use

- Tap habits as you finish them; enter ₹ earned; log Wins / Learnings / Problems / Energy at night; streak updates live.
- Type **Tomorrow #1** → `＋ Google Tasks` (task due tomorrow) and/or `🎯 Block 6–9 AM` (Calendar event with notification).
- **Dashboard tab:** set the month's ₹ target once — then watch earned vs pace, discipline heatmap, and monthly bars. Targets are stored in the `Targets` tab (`2026-07 | 10000` format).
- Rename/add/remove habits: edit the **header row** of the Log sheet — the app adapts automatically. Never delete the `Date`, `Day`, `Earned Today`, `Tomorrow #1`, `Wins`, `Learnings`, `Problems`, `Energy` columns.

## Troubleshooting

- **"origin_mismatch" / redirect error:** the JavaScript origin in Google Cloud must be exactly `https://YOUR-USERNAME.github.io`.
- **Error 403 "API not enabled":** enable the missing API from Part A step 2.
- **Asked to sign in again after ~1 hr / after a week:** normal (Testing mode tokens). One tap re-signs you in.
- **Popup blocked:** allow popups for your app URL in Chrome.
- **Nothing saves:** check you are signed in (⚙︎ → Sign out → Sign in again).
