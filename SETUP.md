# LayeredLanz site — auto-updating from Etsy

This folder is a small, complete website project. Once it's set up, a script
runs once a day, pulls your **current** Etsy listings (titles, prices,
photos, links), and republishes the site automatically — no manual editing.

You'll do five things, all one-time setup: get an Etsy API key, create a
GitHub repo, upload these files, add the API key as a secret, and turn on
GitHub Pages. About 15 minutes total.

## 1. Get a free Etsy API key

1. Go to https://www.etsy.com/developers/register and sign in with your
   normal Etsy (shop owner) account.
2. Click **Create a New App**. Fill in:
   - App name: something like "LayeredLanz Website"
   - App description: "Personal site that displays my shop's listings"
   - You do not need a callback URL for this — this project only reads
     public listing data, so no Etsy login/OAuth is needed.
3. Once the app is created, Etsy shows you a **Keystring** — that's your
   API key. Copy it somewhere safe. (There's also a "Shared secret" — you
   don't need that one for this project.)

## 2. Create a GitHub repository

1. On github.com, click **New repository**.
2. Name it whatever you like, e.g. `layeredlanz-site`.
3. Either "Public" or "Private" works. Public is simplest if you don't mind
   the code itself being visible (your API key never goes in the code, so
   this is safe either way).
4. Don't initialize it with a README — you're uploading files directly.

## 3. Upload these files

Easiest way if you're not using git from the command line: on your new
repo's GitHub page, click **Add file → Upload files**, then drag in
everything from this folder (keeping the folder structure — `scripts/` and
`.github/workflows/` need to stay as subfolders). Commit directly to `main`.

If you're comfortable with git instead:
```
cd layeredlanz-site
git init
git add .
git commit -m "Initial site"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main
```

## 4. Add your Etsy API key as a secret

Never put the API key directly in the code. Instead:

1. In your repo, go to **Settings → Secrets and variables → Actions**.
2. Click **New repository secret**.
3. Name: `ETSY_API_KEY`
4. Value: paste the Keystring from step 1.
5. Save.

## 5. Run it once, then turn on Pages

1. Go to the **Actions** tab in your repo. You should see a workflow called
   **"Update site from Etsy"**. Click it, then click **Run workflow** (this
   triggers it manually instead of waiting for tomorrow's schedule).
2. Wait ~30 seconds and refresh — it should show a green checkmark. This
   means it fetched your shop and committed a fresh `index.html`.
   - If it fails (red X), click into the run to read the error — the
     most common cause is a typo'd API key or the secret not being saved.
3. Now turn on the actual hosting: **Settings → Pages**. Under
   **Build and deployment → Source**, choose **Deploy from a branch**, then
   pick branch `main` and folder `/ (root)`. Save.
4. GitHub will give you a URL like
   `https://<your-username>.github.io/<your-repo>/` — that's your live,
   self-updating site. It may take a minute or two to go live the first
   time.

From now on, the site rebuilds itself every day at 13:00 UTC automatically
— no further action needed. If you add, remove, or reprice a listing on
Etsy, or upload new item photos there, the site picks it up on the next
daily run (or run it manually from the Actions tab any time you don't want
to wait).

## What updates automatically vs. what you edit by hand

Etsy's public API exposes listings (titles, prices, photos, links) and shop
sections, so **items, prices, photos, and buy links stay in sync
automatically.**

The shop story and the policy text in the About/Policies sections come from
`config.json` in this project, because Etsy's public API doesn't expose
that free-text content. If you change your shop's story or policies on
Etsy, update the matching text in `config.json` and push the change (or
edit it directly on GitHub's website — click the file, then the pencil
icon).

## Testing changes locally (optional)

If you have Node.js installed on your own computer:
```
ETSY_API_KEY=your-key-here node scripts/generate.mjs
```
This writes a fresh `index.html` you can open directly in a browser before
pushing anything.
