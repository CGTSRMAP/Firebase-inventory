# Firebase setup — login & data entry

This connects `manage.html` (the admin console) to your Firebase project so admins
can sign in and add, edit, and delete equipment and contacts from the browser.
Your existing `index.html` / `admin.html` search apps are untouched by this step.

You only do this once per Firebase project. Plan on ~15 minutes.

---

## 1. Create the Firebase project
1. Go to <https://console.firebase.google.com> and **Add project** (or open an existing one).
2. Name it after the college, e.g. `srm-ap-inventory`. Analytics is optional.

## 2. Turn on Email/Password sign-in
1. Left menu → **Build → Authentication → Get started**.
2. **Sign-in method** tab → enable **Email/Password** → Save.
   (Leave "Email link" off — plain password is fine.)

## 3. Create your admin login(s)
1. Authentication → **Users** tab → **Add user**.
2. Enter the admin's email and a password → **Add user**.
3. Repeat for each person who should be allowed to edit inventory.
   There is **no public sign-up** — this is the only way accounts are created,
   which keeps editing locked to people you choose.

## 4. Create the database
1. Left menu → **Build → Firestore Database → Create database**.
2. Start in **production mode** (we'll paste rules next) → pick the region
   closest to the college → Enable.

## 5. Paste the security rules
1. Firestore Database → **Rules** tab.
2. Replace everything with the contents of **`firestore.rules`** (in this folder) → **Publish**.
3. This is the part that actually protects the data. The login screen alone is
   just convenience; these rules are enforced on Google's servers.

## 6. Register a Web app and copy the config
1. Project **Settings** (gear icon) → **General** → scroll to **Your apps**.
2. Click the **Web** icon `</>`, give it a nickname, **Register app**.
3. You'll see a `firebaseConfig = { apiKey: "…", … }` block. Copy those values.
4. Open **`data/firebase.json`** in this folder and replace the placeholders with
   your real values. (These web keys are safe to ship in the browser — they
   identify the project, they don't grant access. Access is controlled by step 5.)

```json
{
  "apiKey": "AIza…",
  "authDomain": "srm-ap-inventory.firebaseapp.com",
  "projectId": "srm-ap-inventory",
  "storageBucket": "srm-ap-inventory.appspot.com",
  "messagingSenderId": "123456789",
  "appId": "1:123456789:web:abc123"
}
```

## 7. Deploy and import your existing data
1. Push these files to the same GitHub Pages repo as your other pages, or run a
   local server: `python -m http.server 8000`.
2. Open **`manage.html`** (e.g. `https://…/manage.html` or `http://localhost:8000/manage.html`).
3. Sign in with the admin account from step 3.
4. Click **Import CSV** once. It reads your existing `data/csv/equipment.csv` and
   `data/csv/contacts.csv` and loads them into Firebase. Do this only on first setup.
5. From now on, use **Add / Edit / Delete** — no more editing CSV files by hand.

---

## What's connected, and what's next
- ✅ Admins sign in; everyone else is locked out of editing.
- ✅ Equipment and contacts are now stored in Firebase, editable in the browser.
- ⏳ **Next step:** your public finder (`index.html`) and `admin.html` still read the
  old CSV files, so they won't yet show edits made here. Pointing them at Firebase
  is a small change to their `loadLive` function — say the word and I'll wire it up
  so the whole tool runs off one live source.

## Reusing for another college
Each college gets its own Firebase project (cleanest data isolation): repeat steps
1–6 with that college's project, drop in their `data/firebase.json`, `logo/logo.png`,
and `data/config.json`. The console reads the college name from `config.json` and
tags every record with it automatically.
