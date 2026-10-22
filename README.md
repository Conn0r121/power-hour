# Power Hour

A one-page web app that plays a Spotify list one song per minute, each from a chosen start time, with a fade between songs. Runs entirely in the browser on GitHub Pages; no server.

## What players need

- Spotify **Premium**
- Chrome, Edge, or Firefox **on a computer** (Spotify's web player doesn't run in mobile browsers)
- Their own Spotify app's Client ID (free, ~3 minutes; the setup screen walks through it). The Client ID is saved only in that browser's localStorage and is only sent to Spotify. No Client Secret is needed or asked for.

## Setup (one time, ~10 minutes)

### 1. Create the GitHub repo and push

GitHub Pages on a free account needs a **public** repo. That's fine: no Spotify credentials live in the repo, and the password is a soft gate to keep randoms and crawlers out, not real security.

```bash
cd power-hour
git init
git add .
git commit -m "Power Hour"
git branch -M main
gh repo create power-hour --public --source=. --push
```

No GitHub CLI? Create an empty public repo named `power-hour` on github.com, then:

```bash
git remote add origin https://github.com/YOUR-USERNAME/power-hour.git
git push -u origin main
```

### 2. Turn on GitHub Pages

Repo → **Settings → Pages** → Source: **Deploy from a branch** → Branch: `main`, folder `/ (root)` → Save.

Your URL will be `https://YOUR-USERNAME.github.io/power-hour/`.

### 3. Each player connects their own Spotify app (in the browser)

The first time someone opens the site (after the password), it asks for a Spotify Client ID and shows the exact Redirect URI to use:

1. Go to https://developer.spotify.com/dashboard (log in with a Premium account) → **Create app**.
2. Redirect URI: the one the setup screen shows, e.g. `https://YOUR-USERNAME.github.io/power-hour/` (trailing slash matters). For local testing also add `http://127.0.0.1:8000/` (Spotify doesn't allow `localhost`).
3. APIs used: check **Web API** and **Web Playback SDK**.
4. Save, copy the **Client ID** from the app's Settings, and paste it into the setup screen.

The ID is stored in that browser's localStorage only. **Change Spotify app** in the header clears it. If someone signs in to Spotify with a different account than the one that owns the app, add that account's email under the app's **Settings → User Management**.

### 4. Optional: change the password

In `index.html`, near the top of the `<script>`:

```js
const CONFIG = {
  passwordHash: "…",
};
```

The default password is `powerhour`. To change it:

```bash
echo -n "your new password" | shasum -a 256
```

Paste the hash (just the hex, not the trailing `-`), then:

```bash
git commit -am "Configure" && git push
```

Pages redeploys in about a minute.

## Local testing

```bash
python3 -m http.server 8000
```

Open `http://127.0.0.1:8000/` (not `localhost`).

## Using it

- Paste Spotify song or playlist links into **Add songs**. A time after a link sets its start: `https://open.spotify.com/track/… 1:23`.
- Drag the handle to reorder (or focus it and use Alt + arrow keys).
- **Play** previews from the start time. While it plays, **Mark start here** sets the start to the current spot; −5s / +5s scrub.
- **Test fade** plays the end of a song fading into the next.
- Rows warn if a song is unavailable in your region, if Spotify substituted a different version, or if the start is too late for a full minute.
- The list saves in the browser automatically. **Export** / **Import** in Settings moves it between computers.
- During the hour: Space pauses, arrow keys skip. Keep the tab open; the screen is kept awake where the browser allows it.

## Known limits

- Fades are fade-out then fade-in, not an overlapping crossfade. Spotify plays one stream per account.
- Spotify-made editorial playlists can't be imported; paste the songs individually or copy them into your own playlist.
- Development Mode caps each Spotify app at 5 users.
