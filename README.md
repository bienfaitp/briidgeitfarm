# BriidgeITFarm — standalone build

An animal-farm tracker (pigs, cattle, goats, sheep, poultry, rabbits): animal registry, family tree, births, feed/health/money ledger with
receipts, spending requests (approve / decline), worker agreements, reports, and a farm
photo library. It is a website **and** an installable phone app (PWA), and two phones can
watch the same farm live.

## Any animal, not just pigs

BriidgeITFarm now works for any animal farm. In **Settings → Animals on this farm**, tap the animals you keep:
pigs, cattle, goats, sheep, chickens, ducks, turkeys, rabbits, or "other".

- **One kind selected** (pigs is the default, so an existing pig farm looks exactly as before): the wording
  matches it everywhere: "Total cattle", "Calves", "Add a cow", and so on.
- **Several kinds selected:** the wording becomes "animals" and "young animals", the Add screen asks which kind
  it is, the Animals list gets a filter for each kind, and the Home screen shows a count per kind.
- Each animal remembers its kind. Young animals are recognised by the right age for their kind
  (for example a piglet until about 3 months, a calf until about 18 months). Births name the newborns correctly
  ("Bessie's calf 1") and give them the mother's kind.
- Everything else (photos, family tree, money, receipts, spending requests, agreements, reports, sync and login)
  works the same for every kind. Nothing already saved is changed.

## What's new: in-app camera + live sync between phones

**In-app camera.** Everywhere the app asks for a photo — new pig, birth, receipt, purchase
receipt, spending-request photo, extra pig photos, farm photo library — tapping the box opens
a camera *inside the app*. Take the picture, check it, tap **Use photo**. It never hands you
over to the phone's camera app or gallery. A small **Choose a photo** button is still there as a
fallback (for example if camera permission is blocked).
The camera needs the app to be opened over **https** (GitHub Pages is) or `localhost`, and the
first time the phone asks "Allow camera?" — tap Allow.

**Live sync.** Connect both phones to the same cloud project (Supabase: you sign in as owner and send the worker an invite link; Firebase: both phones use the same sync code). When one
phone adds a pig, takes a receipt photo or approves a request, the other phone shows it within a
second or two, with no upload/export step. You can use **Supabase** or **Firebase (Firestore)**.
Pick one in Settings → Live sync between phones.

> **Which one?** Supabase is the easier free option: its free plan includes photo storage.
> Firebase's Firestore (the records) is free, but Google now asks for a billing plan before it
> will turn on Firebase **Storage** (the photos), so photos may not work on Firebase without a
> card on file. If you just want it to work, choose Supabase.

### Set up Supabase with login (about 8 minutes, once)

With Supabase you get **real logins**: you are the **owner**, each worker gets their own email and
password, and nobody can open the farm without signing in.

| | Owner (you) | Worker |
|---|---|---|
| See everything | yes | yes |
| Add pigs, births, photos, receipts, spending requests | yes | yes |
| **Approve or decline spending** | **yes** | no |
| Delete pigs / suppliers / agreements, change farm settings | yes | no |
| Invite workers, see and remove the team | yes | no |

These limits are enforced by Supabase itself, not just hidden in the app.

1. Go to [supabase.com](https://supabase.com) → free account → **New project** (any name and region,
   set a database password and keep it safe). Wait until it is ready.
2. **Turn off email confirmation** (so workers can join instantly): Supabase → **Authentication →
   Sign In / Providers → Email** → switch **Confirm email** OFF → Save. Leave "Allow new users to
   sign up" ON (workers need it to join through your link; strangers can't do anything without an invite).
3. Open **SQL Editor → New query**. In the app go to **Settings → Live sync → Supabase → "Copy the setup
   SQL"**, paste it into the SQL Editor and press **Run**. It should say *Success*. (Safe to run again,
   and it also removes the open-access version from the earlier build if you ran that.)
4. In Supabase open **Project Settings → API** and copy the **Project URL** and the
   **anon / publishable key**.
5. In the app (Settings): paste both, type **your email**, choose a **password** (8+ characters) and a
   **farm code** (anything, e.g. `kwangwali-farm`), then tap **Create owner account & go live**.
   Your existing farm and photos are copied up. You are now the owner.
6. **Invite a worker:** Settings → **Invite a worker** → *Copy link* (or *Share*) → send it by text or
   WhatsApp. The worker opens it, types an email and a password, and is in. Each link works for **one
   person** and expires after 7 days. **See your team** lets you remove someone.
7. Next time you open the app on your phone you stay signed in. If it asks, just sign in again.

> The first person to create an account in a new Supabase project becomes its owner, so do step 5
> yourself before sharing the app link with anyone. A project has one owner and one farm.

### Set up Firebase / Firestore instead

1. [console.firebase.google.com](https://console.firebase.google.com) → create a project → add a
   **Web app (`</>`)** → copy the `firebaseConfig` block it shows.
2. **Build → Firestore Database → Create database.** Then **Storage → Get started** (this is the part
   that may ask for a billing plan).
3. In the app choose Firebase, tap **Copy the Firestore & Storage rules**, and paste them into
   Firestore → Rules and Storage → Rules in the Firebase console. Press **Publish** on each.
4. Paste the config and a sync code in the app and tap **Connect & go live**. Do the same on the
   second phone with the same config and code.

### Test it with two phones

1. Phone A and Phone B both connected with the same code. Both show a green **Live** badge under the
   farm name.
2. On Phone A (owner) invite Phone B's worker using the invite link, so both are signed in.
   Then on Phone A: open a pig → **Add photo** (or Money → add a receipt) and take a photo.
3. Look at Phone B: the new photo/record appears on its own. No refresh, no upload button.
   (If Phone B is in the middle of typing in a form, the data updates in the background and the
   screen is redrawn when they leave the form, so nothing they typed is lost.)

### Good to know

- **Supabase = logins. Firebase = no logins.** With Firebase, anyone who has your project config *and*
  sync code can read and change the farm, so treat the code like a password. If you want an owner who
  alone approves spending, use Supabase.
- **Photos are not login-protected.** Pictures are saved with long random web addresses. Only farm members
  can add them, but anyone who has an exact photo address can open it. Don't share the addresses.
- **Needs internet while connected.** If a phone loses signal the badge says *Offline* and it
  catches up when the internet returns. Edits made while offline are not queued — wait until the
  badge is green again before saving something important.
- **Last save wins.** If two people edit the *same* record in the same second, the later save is kept.
- **Joining an existing farm.** A second phone that already has its own different pigs saved
  locally will show the shared farm instead; its own local pigs are not merged in. Start the
  second phone empty (or export a backup first).
- **Sign out** (Supabase) returns that phone to its own local copy; the shared farm stays safe in Supabase.
  **Disconnect** (Firebase) copies the shared farm onto that phone so nothing disappears.
- If you forget your password, reset it in Supabase → Authentication → Users.
- Photos are shrunk to a sensible size (about 1600 px) before they are stored, so they load quickly.
- If the app can't reach the shared farm when it opens, it says so rather than quietly showing an
  empty copy.

## Backups

Settings → Backup & transfer → **Export all data** saves a `.json` file; **Tap to import** loads one.
(The import box did nothing in the previous build; it works now.)

## Deploying to GitHub Pages (the website)

1. Create a new GitHub repository (public or private — Pages needs a public repo on
   the free plan, or any repo if you're on GitHub Pro/Team/Enterprise).
2. Upload every file in this zip to the root of that repository, keeping the folder
   structure:
   ```
   index.html
   manifest.json
   service-worker.js
   icons/
     icon-192.png
     icon-512.png
     apple-touch-icon.png
     favicon-32.png
   ```
3. In the repo, go to **Settings → Pages**.
4. Under "Build and deployment", set **Source** to "Deploy from a branch", branch
   `main`, folder `/ (root)`. Save.
5. GitHub gives you a URL like `https://yourusername.github.io/your-repo-name/`.
   It can take a minute or two to go live.

That URL is now your website. Anyone who opens it gets their own private, local
copy of the app — nothing is shared between visitors.

## Installing it as a mobile app

Once it's live on GitHub Pages:

- **iPhone (Safari):** open the URL → Share icon → "Add to Home Screen."
- **Android (Chrome):** open the URL → ⋮ menu → "Install app" (or "Add to Home
  screen").
- **Desktop (Chrome/Edge):** open the URL → install icon in the address bar.

It'll then open full-screen with its own icon, like a normal app, and keeps working
without a connection after the first load.

This gives you a real installable app without going through an app store. If you
later want it listed in the Apple App Store or Google Play, the usual next step is
wrapping this same site with a tool like **Capacitor** or **PWABuilder** — that's a
separate project on top of this one, not something this zip does for you.

## Custom domain (optional)

In the same repo **Settings → Pages**, you can add a custom domain you own (e.g.
`farm.yourdomain.com`) instead of the default `github.io` address.

## Logo

The Briidgeit logo (with "FARM" under it) is in `/icons`: `logo.svg` (full logo, used on the sign-in and invite screens), `mark.svg` (the two-line symbol, used in the header) and the app icons. To change them, replace
`icon-192.png`, `icon-512.png`, `apple-touch-icon.png`, and `favicon-32.png` with
your own images at the same filenames and sizes (192×192, 512×512, 180×180, 32×32).

## Local testing before you deploy

You can't just double-click `index.html` — browsers block some of what it needs
(IndexedDB and the service worker) when opened as a bare `file://` page. Serve it
over a local address instead, e.g. from a terminal in this folder:

```
python3 -m http.server 8000
```

then open `http://localhost:8000` in your browser.
