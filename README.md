# Quai Active Wallet Scanner

Static, single-page dashboard that counts unique active wallets on Quai Network
(Cyprus-1) over 24h / 7d / 30d, by scanning Quaiscan's public transaction API — live,
entirely in the visitor's browser. No backend, no build step, no dependencies.

## Deploy

**Option A — GitHub + Vercel dashboard**
1. Create a new GitHub repo and push this folder to it:
   ```bash
   git init
   git add .
   git commit -m "Quai active wallet scanner"
   git branch -M main
   git remote add origin https://github.com/<you>/<repo>.git
   git push -u origin main
   ```
2. Go to [vercel.com/new](https://vercel.com/new), import the repo.
3. Framework preset: **Other** (it's a static site — no build command, no output
   directory override needed, `index.html` at the root is served as-is).
4. Deploy.

**Option B — Vercel CLI, no GitHub needed**
```bash
npm i -g vercel
vercel
```

## How it works

- Pages backward through `GET https://quaiscan.io/api/v2/transactions?filter=validated`
  using Quaiscan's keyset pagination (`next_page_params`)
- For every transaction, adds the sender address (and, if the checkbox is on, the
  recipient too) into per-window `Set`s for 24h / 7d / 30d
- Stops once it reaches a transaction older than 30 days, or you hit **Stop**

## Will it actually be able to reach Quaiscan?

Two different failure modes matter here, and this deploy only fixes one of them:

- **claude.ai's artifact sandbox** blocks outbound requests to arbitrary domains by
  policy — that's why this didn't work as a chat artifact. A real Vercel deployment has
  no such restriction, so that specific problem is gone.
- **Whether quaiscan.io sends CORS headers permitting browser JS on a different domain
  to call its API** is a separate, genuine unknown I haven't been able to verify from
  here. If it does (common for Blockscout instances, since dapp frontends rely on this),
  the site will just work once deployed. If it doesn't, every visitor's browser will
  throw a CORS error in the console and the on-page banner will explain it.

Open the deployed site and click **Start scan**. If you see numbers moving, you're set.
If you get the red banner, check the browser console for a CORS error — if that's what
it is, the fix is a serverless proxy (a Vercel API route or Edge Function that fetches
Quaiscan server-side and streams results back), which is a quick follow-up build, just
say so.

## Files

- `index.html` — the entire app (styles + logic inline, no dependencies)
