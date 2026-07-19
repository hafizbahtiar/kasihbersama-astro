# KasihBersama Astro — TODO

Public-facing site: landing page + `/verify-email` + `/reset-password`
web fallback pages for mobile Universal Links / App Links. See
`kasihbersama-backend/docs/superpowers/specs/2026-07-19-web-frontend-stack-split-design.md`
for the full rationale (why this is split from the Next.js dashboard) and
`kasihbersama-backend/docs/superpowers/plans/2026-07-19-web-frontend-stack-split.md`
for the readiness plan this repo is part of.

## Done
- [x] Domain purchased: `kasihbersama.com`.
- [x] Project scaffolded (`npm create astro@latest`, default starter).
- [x] Simple landing page — hero + 4 feature cards (Bulatan Penjagaan,
  Log Penjagaan, Ubat & Peringatan, Temujanji & Dokumen), matching the
  mobile app's design tokens (`#00695C` primary, Poppins, same
  border-radius/spacing language as `care_circles_ui_design.md` in the
  Flutter repo). No download links yet — app isn't published to either
  store, so the hero shows an honest "Akan datang" badge instead of a
  fake CTA. Default Astro starter boilerplate (`Welcome.astro`,
  `astro.svg`, `background.svg`) removed. `npm run build` verified clean.
- [x] **Stg/prod env foundation** — `.env` (local, `localhost:8080`),
  `.env.staging` (real Railway staging URL), `.env.production` (intended
  `https://api.kasihbersama.com`, not yet DNS-resolvable — see its inline
  comment). All three **committed** (non-secret, base-URL-only, same
  convention as `kasihbersama_flutter`'s `.env`/`.env.dev`/`.env.stg`);
  `.gitignore` updated to exclude only genuinely-local `*.local` overrides
  instead of the default `.env`/`.env.production`. `src/lib/config.ts`
  exports `apiBaseUrl`/`mode` from `import.meta.env`, surfaced via a
  `<meta name="kb-build">` tag in `Layout.astro` (inspectable via
  view-source — useful for confirming which env a deployed build is
  running). New npm scripts: `dev:staging`, `build:staging` (plain
  `dev`/`build` stay on Vite's default `development`/`production` modes,
  i.e. `.env` / `.env` + `.env.production`). Verified by actually building
  both modes and checking the baked-in meta tag value, not just assumed —
  production build correctly shows `api.kasihbersama.com`, staging build
  correctly shows the Railway staging URL.

## Next
- [ ] **Deploy** — pick a host (Vercel/Netlify/Cloudflare Pages, not
  decided) and point `kasihbersama.com` DNS at it. Also wire the `api`
  subdomain's DNS + Railway custom domain so `.env.production`'s
  `PUBLIC_API_BASE_URL` actually resolves (currently just the intended
  value, unconfirmed).
- [ ] **`/verify-email` page** — reads `?token=` from the query string,
  calls the Go backend's `POST /auth/verify-email`
  (`docs/06-api-contract.md` in `kasihbersama-backend`) using
  `apiBaseUrl` from `src/lib/config.ts`, shows success/failure. Build
  against **staging** first (`npm run dev:staging` — real and testable
  today) since prod API domain isn't wired yet.
- [ ] **`/reset-password` page** — same pattern, plus a new-password
  form, calls `POST /auth/reset-password`.
- [ ] **`.well-known/apple-app-site-association`** and
  **`.well-known/assetlinks.json`** in `public/` — placeholders until
  the Android release keystore (needs generating, see the readiness
  plan) and Apple Team ID are real; do not fabricate fake values in the
  meantime.
- [ ] **CORS** — once deployed, add this site's real origin to the Go
  backend's `CORS_ALLOWED_ORIGINS` (Railway env var) so the
  verify-email/reset-password pages can actually call the API from the
  browser.
- [ ] **Point the backend's email links at this domain** (optional,
  undecided) — `signup.go`/`password_reset.go` in `kasihbersama-backend`
  currently build links off `PublicAPIBaseURL` (the raw API host, no
  HTML page). If verify/reset should open here instead, that's a
  separate small backend change — tracked as open in the stack-split
  spec's Non-goals, not decided yet.
