# Important Contacts — self-hosted version

A contact directory for the Minister (MA&UD, Government of Andhra Pradesh) covering
MLAs, Lok Sabha & Rajya Sabha MPs, Ministers, Collectors, Joint Collectors,
Mayors/Chairpersons, UDA Chairpersons and ULB Commissioners.

- **Anyone with the link can view and call contacts — no sign-in required.**
- **Only signed-in staff accounts can add, edit, delete, or bulk-upload.**
- Data lives in your own Firebase project (Firestore only), not on Claude's servers.
- **No billing / Blaze plan needed.** Photos are compressed in the browser into a
  small JPEG and stored directly on the contact's record in Firestore — this
  intentionally avoids Firebase Storage, which Google now requires a billing
  account to switch on (even though using it would still be free at this scale).

## One-time setup (about 10–15 minutes)

1. **Create a Firebase project** — go to https://console.firebase.google.com, click
   "Add project", give it a name (e.g. `important-contacts`), and finish the wizard.
2. **Enable Firestore** — in the left sidebar, Build → Firestore Database → Create database
   → start in **production mode** → pick a region close to India (e.g. `asia-south1`).
3. **Enable Authentication** — Build → Authentication → Get started → enable the
   **Email/Password** sign-in provider.
4. **Add your staff accounts** — Authentication → Users → Add user → enter each staff
   member's email and a password. These are the only people who can edit data.
5. **Register a Web app** — Project Settings (gear icon) → General → "Your apps" →
   click the `</>` (Web) icon → give it a nickname → Firebase shows you a `firebaseConfig`
   object with `apiKey`, `authDomain`, `projectId`, etc.
6. **Paste your config into `index.html`** — open `index.html`, find the block near the
   top of the `<script>` that says:
   ```js
   const YOUR_FIREBASE_CONFIG = {
     apiKey: "REPLACE_ME",
     ...
   };
   ```
   Replace every `"REPLACE_ME"` with the matching value Firebase gave you.
7. **Paste the security rules** — Firestore Database → Rules tab → replace the
   contents with `firestore.rules` from this folder → click **Publish** (editing the
   text alone doesn't apply it — you must click Publish and see it confirm).
8. **Put it on GitHub Pages** —
   - Create a new GitHub repository (public or private).
   - Upload **all six files** to it: `index.html`, `manifest.json`, `sw.js`,
     `icon-192.png`, `icon-512.png`, `icon-512-maskable.png` — they must all sit
     next to each other at the root of the repo (or all together in the same
     subfolder) for the app icon and "Install app" prompt to work.
   - Repo Settings → Pages → Source: deploy from branch → pick `main` and `/ (root)` →
     Save. GitHub gives you a URL like `https://yourname.github.io/important-contacts/`.
9. **Share the link** — that URL is the one, single link for everyone. The Minister
   (or anyone) opens it and can view/call. Staff open the same link and click
   **"🔒 Staff Sign In"** in the top-right to unlock editing.

## Installing it as an app on a phone

With `manifest.json`, the icons, and `sw.js` in place, the site is a proper
installable web app:
- **Android (Chrome)** — open the link, then either tap the "Install app" banner
  if it appears, or open the ⋮ menu → **"Add to Home screen" / "Install app"**.
  It installs with its own icon and opens full-screen, without a browser
  address bar.
- **iPhone/iPad (Safari)** — open the link, tap the Share icon, then
  **"Add to Home Screen"**. iOS doesn't support the automatic install banner,
  but this gives the same full-screen, own-icon result.
- It can take a minute after your first deploy for GitHub Pages to serve the
  new files — if "Install app" doesn't appear immediately, wait a minute and
  reload.

## About photos (no Storage/billing required)

When staff upload a photo, the browser resizes and compresses it down to a small
JPEG (roughly 20–80KB depending on the source image) and saves it as part of the
contact's own Firestore document. This is intentionally a *thumbnail*, sized for
recognising a face on a small card — not a high-resolution photo. Two practical
limits worth knowing:
- Firestore caps each document at 1MB total; the compression keeps photos far
  below that with room to spare for the rest of the contact's fields.
- The free ("Spark") Firestore plan includes 1GB of stored data and 10GB of
  monthly network transfer — at these thumbnail sizes, several hundred contacts
  with photos comfortably fits within that, even with daily use across an office.

If you later want full-resolution photos, you can upgrade to Firebase's Blaze
(pay-as-you-go) plan and enable Storage — the upload logic is isolated in one
function (`assetsCap.upload` / `compressImageToDataUrl`), so swapping it for
real Storage upload later is a small, contained change.

## Moving your existing data across

Your current data lives in the Claude-hosted version. To bring it into this one:

1. In the Claude-hosted app, select each category tab and use **Export CSV**
   (or download each category's template after filling it) to get your data out.
2. In this new app, use the matching category's **Bulk upload** button to import
   each CSV.
3. Photos will need to be re-uploaded via **Bulk upload photos** in the new app,
   using the same file-naming convention as before — photo storage doesn't carry
   over automatically between the two systems.

## Notes

- `index.html` is the entire app — one file, no build step.
- `firestore.rules` is pasted into the Firebase console, not deployed to GitHub.
- If you ever want a *link that pre-opens the sign-in box* for staff convenience,
  share the URL with `#edit` on the end (e.g. `.../index.html#edit`) — it still
  requires a real password, it just saves a click.
