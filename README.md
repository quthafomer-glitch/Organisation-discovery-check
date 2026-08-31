# Organisation Discovery Check — single-deploy version

This is the same app, restructured so it deploys as **one project to one
URL** — frontend and API together — instead of running a separate
frontend and backend.

```
public/index.html    Static frontend (served as-is)
api/research.js       Serverless function → becomes POST /api/research
package.json           Just the one dependency (openai)
```

I can't deploy this for you or hand you a live URL directly — I don't have
the ability to reach the internet or provision hosting from here. But this
is set up so *you* can get a working URL in about two minutes, no server
management involved, using Vercel's free tier (this structure — a
`public/` folder plus an `api/` folder — is what Vercel auto-detects; the
same two files also work on Netlify with a one-line config change if you'd
rather use that).

## Deploy to Vercel (get your URL)

**Option A — from the Vercel dashboard, no command line:**
1. Put this folder in a GitHub repo (create a new repo, drag these files
   in, commit).
2. Go to vercel.com → **Add New Project** → import that repo.
3. Before the first deploy, open **Environment Variables** and add:
   - `OPENAI_API_KEY` = your real key
   - `OPENAI_MODEL` = (optional) a model that supports the Responses API
     `web_search` tool — check OpenAI's current docs for what you have
     access to
4. Click **Deploy**. Vercel gives you a URL like
   `https://organisation-discovery-check.vercel.app` — that's your single
   URL. Open it in a browser and it works, no local setup needed.

**Option B — from the command line:**
```bash
npm install -g vercel
cd organisation-discovery-check
vercel                       # first deploy, follow the prompts
vercel env add OPENAI_API_KEY production
vercel --prod                # redeploy with the env var applied
```

Either way, the API key stays in Vercel's server-side environment — it's
never in the repo, never sent to the browser.

## Trying it locally first (optional)

```bash
npm install -g vercel
npm install
vercel dev
```
`vercel dev` runs the static frontend and the `/api/research` function
together on `http://localhost:3000`, matching production behavior — so
you can test before deploying.

## Everything else

Same behavior, same validation rules, same XLSX export as described in the
original two-piece version. See the code comments in `api/research.js` and
`public/index.html` for the full logic. Before sharing the URL widely,
still worth doing:
- Add rate limiting (each search is a paid AI + web-search call).
- Confirm your exact `OPENAI_MODEL` against current OpenAI docs.
- Swap the lightweight domain-matching heuristic for the `psl` package if
  you need stricter public-suffix handling.
