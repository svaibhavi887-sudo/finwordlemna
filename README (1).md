# FinWordle — Multiplayer Finance Word Challenge

A simultaneous multiplayer Wordle game for 11 teams, with 5 fixed finance words, real-time sync via Firebase, and a password-protected admin panel.

---

## How It Works

- All 11 teams play **simultaneously** on their own devices (phones, laptops, etc.)
- The **admin controls** the game flow: start rounds, show results, advance leaderboard
- Scores sync in real-time via **Firebase Realtime Database** (free)
- 5 fixed rounds: DERIVE → DILUTE → OFFSET → HURDLE → ESCROW

---

## Files

| File | Purpose |
|------|---------|
| `index.html` | The game — shared with all players |
| `admin.html` | Admin panel — password-protected host control |

---

## Step 1: Set Up Firebase (Free)

1. Go to **https://console.firebase.google.com**
2. Click **"Add project"** → name it (e.g. `finwordle`) → Create
3. In the left sidebar: **Build → Realtime Database**
4. Click **"Create Database"** → choose your region → Start in **test mode** → Done
5. In the left sidebar: **Project Settings** (⚙ icon)
6. Scroll to **"Your apps"** → click the **`</>`** (Web) icon → Register app → name it → Continue
7. Copy the `firebaseConfig` object shown

---

## Step 2: Add Firebase Config

Open **both** `index.html` and `admin.html` and replace the placeholder config block:

```javascript
const firebaseConfig = {
  apiKey: "REPLACE_WITH_YOUR_API_KEY",
  authDomain: "REPLACE_WITH_YOUR_PROJECT.firebaseapp.com",
  databaseURL: "https://REPLACE_WITH_YOUR_PROJECT-default-rtdb.firebaseio.com",
  projectId: "REPLACE_WITH_YOUR_PROJECT",
  storageBucket: "REPLACE_WITH_YOUR_PROJECT.appspot.com",
  messagingSenderId: "REPLACE_WITH_YOUR_SENDER_ID",
  appId: "REPLACE_WITH_YOUR_APP_ID"
};
```

Paste **your actual values** from Firebase in both files.

---

## Step 3: Set Your Admin Password

In `admin.html`, find this line near the top of the `<script>` block:

```javascript
const ADMIN_PASSWORD = "finwordle2025"; // Change this!
```

Change `"finwordle2025"` to a strong password of your choice.

---

## Step 4: Deploy

### Option A — GitHub Pages (Free, Recommended)

1. Create a GitHub account at **github.com** if you don't have one
2. Click **"New repository"** → name it `finwordle` → Public → Create
3. Upload both `index.html` and `admin.html`
4. Go to **Settings → Pages → Source: Deploy from branch → main → / (root)** → Save
5. Your URLs will be:
   - Players: `https://YOUR_USERNAME.github.io/finwordle/`
   - Admin: `https://YOUR_USERNAME.github.io/finwordle/admin.html`

### Option B — Vercel (Free)

1. Go to **vercel.com** → Sign up with GitHub
2. Click **"Add New Project"** → import your `finwordle` GitHub repo
3. Deploy — Vercel auto-detects static files
4. Your URLs will be:
   - Players: `https://finwordle.vercel.app/`
   - Admin: `https://finwordle.vercel.app/admin.html`

### Option C — Render (Free)

1. Go to **render.com** → New → Static Site
2. Connect your GitHub repo
3. Build command: _(leave blank)_
4. Publish directory: `.`
5. Deploy

---

## Step 5: Firebase Security Rules

Once you're ready to go live, tighten your Firebase rules. In the Firebase Console:
**Realtime Database → Rules** → paste:

```json
{
  "rules": {
    "sessions": {
      "$session": {
        ".read": true,
        ".write": true
      }
    }
  }
}
```

This keeps it open for the event but limits access to the `sessions` path only.

---

## Running the Game (Host Instructions)

### Before the event:
1. Deploy the files and configure Firebase
2. Share the player URL with all 11 teams
3. Open `admin.html` on your device and log in

### During the event:

**Setup Phase:**
- One person (or the host) opens the player page and enters all 11 team names, then clicks **"Start Competition"**
- All other team devices open the same player URL and wait for team selection

**Each Round:**
1. Each team selects their name on the player-select screen
2. Teams arrive at the **lobby** and wait
3. Admin clicks **▶ Start Round** → all teams begin playing simultaneously
4. Teams solve the word (or time out) and are moved to the waiting screen
5. Admin watches the progress grid fill up
6. When most teams are done, admin clicks **📊 Show Results**
7. Teams see round scores
8. Admin clicks **🏆 Leaderboard**
9. Teams see updated leaderboard
10. Admin clicks **▶ Start Round** for the next round (auto-advances)
11. Repeat for all 5 rounds
12. After Round 5 leaderboard, admin clicks **🏁 End Game**
13. All devices show the final winner screen

---

## Customizing Words

To change the words, edit this section in `index.html`:

```javascript
const WORD_BANK = [
  {word:"DERIVE", clue:"Financial instruments whose value comes from an underlying asset."},
  {word:"DILUTE", clue:"Reduction in ownership percentage due to new shares."},
  {word:"OFFSET", clue:"To counterbalance or cancel out a financial position."},
  {word:"HURDLE", clue:"Minimum rate of return required before profits are shared."},
  {word:"ESCROW", clue:"Money held by a third party until conditions are met."}
];
```

**Important:** Also update `WORD_BANK` in `admin.html` to match (same constant near the top of the script).

Words can be 5 or 6 letters. If using 6-letter words, make sure they are added to the `VALID_GUESSES` set in `index.html` so they can be entered as valid guesses.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Teams don't see round start | Check Firebase config is correct in both files |
| "No session" in admin | Have a player device go through setup first |
| Scores not syncing | Check browser console for Firebase errors |
| Admin page accessible to all | Deploy admin.html under a different obscure path, or use Vercel password protection |

---

## Scoring

| Guesses | Base Points |
|---------|-------------|
| 1 | 100 pts |
| 2 | 85 pts |
| 3 | 70 pts |
| 4 | 55 pts |
| 5 | 40 pts |
| 6 | 25 pts |
| Not solved | 0 pts |

**Time bonus:** Up to +50 pts, scales down over 120 seconds.
