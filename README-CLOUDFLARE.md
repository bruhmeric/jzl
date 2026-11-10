# JAZZEL Furniture — Cloudflare Pages deployment

This is the Cloudflare Pages build of the site. The whole site is static
(fully client-side: the cart lives in the visitor's browser and checkout
sends the order through WhatsApp), so it runs on Cloudflare Pages' free
plan with no server, no database and no environment variables.

The repo already contains a **prebuilt `out/` folder** — you can deploy
without installing anything (Option A), build from source (Option B), or
deploy straight from your terminal with Wrangler (Option C).

---

## Option A — Direct upload (fastest, ~2 minutes)

1. Unzip this package.
2. Go to **https://dash.cloudflare.com** → **Workers & Pages** →
   **Create** → **Pages** → **Upload assets**.
3. Project name: `jazelle` (or anything you like).
4. Drag the contents of the **`out/`** folder (the folder's *contents* —
   `index.html`, `_next/`, `assets/`, `brand/`, `_headers` …) into the
   upload area.
5. Click **Deploy**. Your site is live at
   `https://<project-name>.pages.dev`.

No build step, no Node, no config needed.

## Option B — Deploy from Git (auto-deploys on every push)

1. Push this folder to a GitHub/GitLab repo.
2. In Cloudflare: **Workers & Pages** → **Create** → **Pages** →
   **Connect to Git** → pick the repo.
3. Build settings:
   - **Framework preset:** Next.js (Static HTML export) or None
   - **Build command:** `npm run build`
   - **Build output directory:** `out`
   - **Environment variable** (Settings → Environment variables):
     `NODE_VERSION` = `22`
4. **Save and Deploy.**

> Bun also works if you prefer: build command `bun run build`
> (Cloudflare's build image ships with Bun).

## Option C — Deploy with Wrangler CLI (terminal, no dashboard)

The project ships with a `wrangler.toml` (Pages config, output dir `out`)
and wrangler is already in `devDependencies`.

```bash
npm install          # or: bun install
npm run deploy       # or: bunx wrangler pages deploy
```

**First time — log in:** run `npx wrangler login` once; it opens a browser
to connect your Cloudflare account (no credentials stored in the repo).
Prefer a CI token? set `CLOUDFLARE_API_TOKEN` (Pages:Edit permission) and
`CLOUDFLARE_ACCOUNT_ID` instead — no browser needed.

- The first `npm run deploy` **creates** the `jazelle-furniture` Pages
  project automatically and deploys `out/`.
- Every later run publishes a new production deployment (instant rollbacks
  from the dashboard → Deployments).
- Changed the source? `npm run build` first, then `npm run deploy`.
- Preview locally on Cloudflare's own runtime: `npm run preview`
  (= `wrangler pages dev`, serves `out/` at http://localhost:8788).

---

## After deploy: use your own domain

- In the Pages project → **Custom domains** → **Set up a custom domain**
  → enter `jazzel.lk` (or your domain) and follow the DNS instructions.
- If you deploy under a domain **other than jazzel.lk**, update
  `metadataBase` in `src/app/layout.tsx` (it controls the social-share
  image URL) and rebuild.

## Good to know

| Thing | Where / how |
| --- | --- |
| Products, prices, categories | `src/lib/products.ts` — edit and rebuild |
| Brand info (phone, WhatsApp, socials) | `BRAND` object in `src/lib/products.ts` |
| Checkout | Opens WhatsApp with the order summary — no server involved |
| Caching rules | `public/_headers` (long cache for images & build assets) |
| Hero / brand images | `public/brand/`, product photos in `public/assets/` |
| Styling & theme | `src/app/globals.css` + Tailwind config |

## Local development

```bash
npm install     # or: bun install
npm run dev     # http://localhost:3000  (Next.js dev server)
npm run build   # regenerates out/
npm run preview # http://localhost:8788 (wrangler, serves out/ like Pages does)
npm run deploy  # deploys out/ to Cloudflare Pages (wrangler login first)
```
