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
- [x] **Landing page refresh + legal/standard pages** (2026-07-19, 7-task
  plan — see `docs/superpowers/specs/2026-07-19-landing-refresh-and-legal-pages-design.md`
  and `docs/superpowers/plans/2026-07-19-landing-refresh-and-legal-pages.md`)
  — new `src/layouts/SiteLayout.astro` shared chrome (sticky nav: wordmark
  + Ciri-ciri/Privasi/Terma/Hubungi; multi-column footer: brand + tagline,
  Produk, Legal, Hubungi, copyright bar), wrapping the existing
  `Layout.astro` the same way `AuthLayout.astro` already does.
  `index.astro` restructured onto it: radial-gradient hero (badge copy
  unchanged — still the honest "Akan datang" line), a new 3-pill trust
  strip ("🔒 Data disulitkan" / "🇲🇾 Patuh PDPA Malaysia" / "🙅 Tiada
  jualan data" — all checkable claims today, not aspirational), same 4
  feature cards now with icon badges + hover elevation. Five new pages
  added, all Malay, all on `SiteLayout`: `/privacy` (Dasar Privasi,
  grounded entirely in `kasihbersama-backend/docs/08-security-privacy.md`
  — data collected, why, named third-party processors [Resend, Cloudflare
  R2/Workers, Railway, FCM], user rights via `/me/delete`, retention,
  security posture, breach process, minors/vulnerable care subjects,
  contact, last-updated date), `/terms` (Terma & Syarat, including doc
  08's product disclaimer translated to Malay with meaning preserved
  exactly — not a diagnosis/treatment/emergency service), `/contact`
  (Hubungi Kami — mailto only, no form, since no backend endpoint exists
  to receive one), `/about` (Tentang Kami — honest pre-launch mission
  page, no fabricated team bios/company history), and `404.astro`
  (custom not-found page, `SiteLayout` chrome, link back to `/`). Two
  corrections made during review: (1) `/privacy`'s "Keselamatan" section
  originally claimed sensitive free-text health columns get separate
  application-level (envelope) encryption — removed, since the backend's
  own TODO.md/roadmap lists that as not-yet-shipped (Track 8d, open); the
  page now only states what's actually true (TLS in transit, argon2id
  passwords, general encryption at rest, per-request access re-check).
  (2) `/privacy`'s "Hak anda" section originally implied account
  export/delete works today "melalui aplikasi" — `POST /me/delete` is
  documented in the backend's API contract but has no handler
  implemented (no match in `kasihbersama-backend/internal/transport/
  http/v1/*.go` or `internal/app/auth/*.go`); reworded to state the
  right exists (true under PDPA regardless of tooling) while routing
  requests through email (`hafiz@hafizbahtiar.com`) until the in-app flow
  ships.
  Verified with a full clean build in both modes (`rm -rf dist && npm run
  build` then `npm run build:staging`) — both `8 page(s) built`, no
  errors, confirming the 5 new pages don't touch `apiBaseUrl` at all
  (unlike `verify-email`/`reset-password`); link check confirmed every
  nav/footer route (`/privacy`, `/terms`, `/contact`, `/about`, `/404`)
  resolves to a real file in `dist/` — though at the time `/about` itself
  was actually an orphan page (built, resolvable by direct URL, but not
  reachable from any nav or footer link); this was caught during the
  final whole-branch review, not by this task's own review, and fixed by
  adding an `/about` link to the footer's Produk column
  (`SiteLayout.astro`), so the "every nav/footer route resolves" claim
  now actually holds end-to-end. Two non-blocking follow-ups tracked
  below under **Next**: no registered legal entity name yet, and
  `AuthLayout.astro` pages don't link `/privacy`/`/terms`.

## Next
- [ ] **Deploy** — pick a host (Vercel/Netlify/Cloudflare Pages, not
  decided) and point `kasihbersama.com` DNS at it. Also wire the `api`
  subdomain's DNS + Railway custom domain so `.env.production`'s
  `PUBLIC_API_BASE_URL` actually resolves (currently just the intended
  value, unconfirmed).
- [ ] **CORS on staging** — `OPTIONS /api/v1/auth/verify-email` against
  `kasihbersama-backend-staging.up.railway.app` with
  `Origin: http://localhost:4321` returns no
  `Access-Control-Allow-Origin` header today (checked 2026-07-19), so the
  two pages below can't actually complete a browser `fetch` against
  staging yet — confirmed at the curl/API level only. Needs the Astro
  dev origin (and later the real deployed origin) added to the backend's
  `CORS_ALLOWED_ORIGINS` Railway env var (`kasihbersama-backend`
  repo/infra, not a file here) before an end-to-end browser test is
  possible.
- [x] **`/verify-email` page** (`src/pages/verify-email.astro`, shares
  chrome with `src/layouts/AuthLayout.astro`) — reads `?token=` from the
  query string client-side (static output, no SSR — must run in-browser),
  `POST`s to `${apiBaseUrl}/api/v1/auth/verify-email` via `define:vars`,
  shows loading/success/error states. Error message surfaces the
  backend's `error.message` directly. Verified: `npm run build` and
  `npm run build:staging` both succeed (3 pages, meta tag correctly bakes
  `api=https://kasihbersama-backend-staging.up.railway.app` in staging
  mode); a live curl against staging with a bogus token confirms the
  exact response shape the page's error path expects
  (`401 {"error":{"code":"unauthenticated","message":"invalid
  credentials or token"}}`). Not yet verified in an actual browser — see
  the CORS item above.
- [x] **`/reset-password` page** (`src/pages/reset-password.astro`) —
  same pattern, plus a new-password + confirm form (client-side
  min-length-10 + match check before submit), `POST`s
  `{ token, new_password }` to `/auth/reset-password`. 401 (invalid/
  expired token) replaces the whole card with the terminal error state;
  other errors (e.g. weak password) show inline under the form so the
  user can retry. Same build verification as verify-email; same
  not-yet-browser-tested caveat.
- [x] **`.well-known/apple-app-site-association`** and
  **`.well-known/assetlinks.json`** in `public/` — placeholders, no
  fabricated Team ID/package/fingerprint. `assetlinks.json` is bare `{}`
  per the readiness plan's Task 3 Step 2 (note: real Digital Asset Links
  files are a top-level *array* — `{}` is deliberately non-conforming so
  it reads as "not configured" rather than an empty-but-valid statement
  list). `apple-app-site-association` is valid JSON with `applinks.
  details: []` plus a `_comment` field pointing at the readiness plan's
  Task 5 Step 2 for what to fill in once the Apple Team ID is known (JSON
  has no native comment syntax, hence the field). Verified both copy
  through `npm run build` unmodified to `dist/.well-known/`.
- [ ] **Point the backend's email links at this domain** — now that
  `/verify-email` and `/reset-password` actually exist, this stops being
  optional: `signup.go:79` / `password_reset.go:39` in
  `kasihbersama-backend` still build links off `PublicAPIBaseURL` (the
  raw API host, JSON only), so a user clicking the email link today never
  reaches these pages at all. Needs a small backend change (new
  `PublicWebBaseURL`-style config pointing here, or repointing the
  existing var) — tracked as open in the stack-split spec's Non-goals;
  not done in this session, backend repo's call.
- [ ] **Name a registered legal entity in Privacy/Terms** — `/privacy` and
  `/terms` currently name no company, just the contact email
  `hafiz@hafizbahtiar.com` (deliberate per the 2026-07-19 spec — no entity
  is registered yet). Update both pages once a company actually exists.
- [ ] **Link `/privacy`/`/terms` from the auth pages** —
  `AuthLayout.astro`-based pages (`verify-email.astro`,
  `reset-password.astro`) still don't surface Privacy/Terms links the way
  `SiteLayout.astro`'s footer does for every other page. Deliberately out
  of scope for the 2026-07-19 landing refresh (see that spec's Non-goals —
  kept the change reviewable as "landing + new pages" without touching
  already-shipped, already-tested auth pages); worth doing as a fast
  follow.

## Later (deferred 2026-07-19 — not started)
- [ ] **Invite/claim accept without the app or an account.** Considered
  extending `/verify-email`+`/reset-password` scope to also cover
  `POST /invites/accept` and `POST /claims/accept` for someone who
  clicked an invite/claim email but has neither the app nor an account.
  Explicitly deferred — parking the findings so they don't need
  re-deriving:
  - `AcceptInvite`/`AcceptClaim` (`kasihbersama-backend/internal/app/
    membership/invite.go:67`, `claim.go:69`) require an already
    -authenticated `userID` whose **verified** email matches the invite/
    claim target. There is no combined signup+accept endpoint and no
    auto-provisioned "stub user" despite doc 02's mention of one — that
    concept isn't actually implemented.
  - So a not-yet-registered invitee needs to sign up (or log in) *before*
    accept can succeed. Recommended shape when this gets picked up: keep
    it in Astro, not Next.js — no persistent session is actually needed,
    since the page can call `/auth/signup` or `/auth/login` to get a
    token and immediately call accept with it in the same page load
    (same island-only, no-SPA-router pattern as verify-email/
    reset-password). Pulling the Next.js dashboard forward just for this
    would drag in its whole undecided auth mechanism for no reason.
  - Separately noticed while reading the code: `CreateInvite`/
    `CreateClaim` (`invite.go:60`, `claim.go:62`) put the token in a
    **query string** (`?token=`), not the URL fragment doc 04 itself
    prescribes for invite/claim links specifically (`/claim#token=...`,
    doc 04 "Token rules") — worth fixing alongside this work, since
    query-string tokens land in server logs/proxies/Referer headers.
    (`/verify-email`/`/reset-password` don't have this issue — doc 02
    doesn't carry the same fragment requirement for those, lower-stakes
    tokens.)
