# Landing Page Refresh + Legal/Standard Pages — Design Spec

**Date:** 2026-07-19
**Repo:** `kasihbersama-astro` (scoped entirely to this repo — unlike the
same-day stack-split decision, nothing here touches the backend or mobile
repos).

## Goal

The landing page reads as unfinished/untrustworthy in its current form (flat
single-column layout, no legal pages, no way to contact the operator). This
spec covers a visual refresh of the landing page plus five new standard
pages: Privacy Policy, Terms & Conditions, Contact, About, and a custom 404.

## Context

Brainstormed with the visual companion (three structural directions —
two-column SaaS hero, warm narrative, minimal-plus-trust-band). **Minimal +
trust band ("Option C")** was selected, then refined once for polish (soft
gradient hero, icon badges, trust-pill strip, card hover elevation,
multi-column footer). That refined mockup is the visual target below.

Explicit constraint carried through the whole brainstorm: **no fabricated
trust signals.** No fake testimonials, user counts, star ratings, or app
screenshots — the app isn't published to either store yet (the existing
"Akan datang" badge already reflects this honestly, per `TODO.md`'s "Done"
section). Trust must come from real, checkable claims (encryption, PDPA
posture, a real contact address) and from looking structurally complete
(legal pages existing, working nav/footer), not from invented social proof.

## Decisions

### 1. Shared layout: new `SiteLayout.astro`

Landing (`index.astro`) and the four new content pages (`privacy`, `terms`,
`contact`, `about`) all need the same chrome: sticky nav (wordmark +
Ciri-ciri/Privasi/Terma/Hubungi links) and a multi-column footer (brand +
tagline, Produk, Legal, Hubungi, bottom bar with copyright). That's five
pages sharing identical structure — enough real duplication to justify a
shared layout component, same pattern as `AuthLayout.astro` already does for
`verify-email`/`reset-password`.

`AuthLayout.astro` is **not** reused here and is not being touched — it's
deliberately minimal-chrome (centered card, no footer nav) because it serves
a transactional, drop-in-from-an-email-link flow, not a browsing flow.
`SiteLayout.astro` gets its own footer with the Privacy/Terms/Contact links
those auth pages currently lack — bringing them in line is a fast follow
listed under Non-goals, not part of this pass.

`SiteLayout.astro` wraps the existing `Layout.astro` (keeps the
`<meta name="kb-build">` inspection tag, fonts, global CSS variables) the
same way `AuthLayout.astro` does.

### 2. Landing page (`index.astro`) — restructure

Sections, top to bottom:
1. **Nav** — sticky, semi-transparent backdrop, wordmark + 4 links.
2. **Hero** — radial gradient background (`--color-primary-light` fading to
   `--color-surface`), badge (existing "Akan datang..." copy, now styled as
   a pill with a border/shadow instead of a flat tag), heading, subtitle.
   Copy is unchanged from today — this is a structural/visual pass, not a
   copy rewrite.
3. **Trust strip** — three pills: "🔒 Data disulitkan", "🇲🇾 Patuh PDPA
   Malaysia", "🙅 Tiada jualan data". All three are true today (TLS +
   at-rest encryption per doc 08, PDPA posture documented, no analytics/ad
   SDKs anywhere in this repo or the backend) — not aspirational claims.
4. **Feature grid** — same 4 cards/copy as today, each gets an icon badge
   (emoji in a rounded-square tinted background) and a hover elevation
   (`box-shadow` + slight `translateY`).
5. **Footer** — brand + one-line tagline, three link columns (Produk →
   Ciri-ciri anchor; Legal → Privasi, Terma; Hubungi → mailto link), bottom
   bar with copyright + "Dibuat di Malaysia 🇲🇾".

### 3. New pages

All in Malay (matches `Layout.astro`'s `lang="ms"` and every existing page
— no reason to break that convention for these). All routed at the root
(`/privacy`, `/terms`, `/contact`, `/about`), wrapped in `SiteLayout`, using
a simple prose column (max-width ~680px, headings + paragraphs + lists —
no new components needed beyond what `SiteLayout` provides).

**`/privacy` — Dasar Privasi.** Grounded entirely in
`kasihbersama-backend/docs/08-security-privacy.md` — no invented data
practices. Sections: apa data dikumpul (account info; health-adjacent care
data — allergies/conditions/vitals/prescriptions; uploaded documents; device
push tokens); kenapa dikumpul (provide the service, reminders, emergency
card); perkongsian pihak ketiga, named — Resend (email delivery), Cloudflare
R2 + Workers (storage/hosting), Railway (database hosting), Firebase Cloud
Messaging (push) — stated as processors, not data sales; hak pengguna
(export/delete via the app's `/me/delete` flow); pengekalan data (soft
delete + anonymization on account deletion, never hard-delete per doc 08);
keselamatan (TLS, argon2id, encryption at rest, envelope encryption for the
most sensitive free-text columns); proses pelanggaran data (breach
notification); kanak-kanak/subjek terdedah (a care profile's *subject* may
be a minor or an elderly person who isn't the account holder — data is
entered by a guardian on their behalf, not collected directly from them);
hubungi (`hafiz@hafizbahtiar.com`); tarikh kemas kini.

**`/terms` — Terma & Syarat.** Sections: penerimaan terma; penerangan
perkhidmatan (family care coordination/logging tool) **including doc 08's
verbatim product disclaimer** ("KasihBersama is a family care coordination
and logging tool. It does not provide medical diagnosis, treatment, or
emergency advice. Always consult a qualified healthcare professional." —
translated to Malay, meaning preserved exactly, not paraphrased loosely);
kelayakan (account holder 18+; the care subject has no age restriction);
tanggungjawab pengguna (accurate info, account security); penggunaan yang
dibenarkan (no relying on the app for medical decisions, no unlawful use);
pemilikan kandungan (family's care data belongs to the family); penamatan;
had liabiliti; perubahan terma; undang-undang yang terpakai (Malaysia);
hubungi.

**`/contact` — Hubungi Kami.** Short: one paragraph, the contact email as a
prominent `mailto:` link, no web form (no backend endpoint exists to receive
one — a form with nowhere to submit would be worse than no form).

**`/about` — Tentang Kami.** Short mission page, expands the "kenapa kami
bina KasihBersama" line from the narrative-direction mockup into 2-3
paragraphs. Explicitly honest about being pre-launch/early — no fabricated
team bios, no fake company history, no invented founding date beyond what's
true (domain purchased 2026, per this repo's own `TODO.md`).

**`404.astro`** — Astro's file-based custom-404 convention. `SiteLayout`
chrome, centered message ("Halaman tidak dijumpai"), link back to `/`. No
legal/compliance content here — pure UX.

## Contact/entity details used

Per explicit instruction this session: contact email is
`hafiz@hafizbahtiar.com`. No registered company entity exists yet — pages
refer to "KasihBersama" as the product/operator, not a named legal entity.
**Flagged for follow-up, not blocking:** if/when a company is registered,
the Privacy Policy and Terms should be updated with the real entity name —
tracked in this repo's `TODO.md`, not a launch blocker for a pre-launch
landing page.

## Non-goals

- **Not touching `AuthLayout.astro`, `verify-email.astro`, or
  `reset-password.astro`.** Giving those pages the same footer/nav links is
  a reasonable fast follow but is out of scope for this pass — keeps this
  change reviewable as "landing + new pages" rather than mixing in changes
  to already-shipped, already-tested pages.
- **Not a legal review.** This is real content grounded in doc 08, not
  boilerplate — but it is not a substitute for actual legal counsel before
  the app has real users signing up. Flagged in `TODO.md` after
  implementation, not blocking this pass.
- **Not adding analytics, cookies, or any tracking.** The trust-strip claim
  "no data selling" and the absence of a cookie banner both depend on this
  staying true. If analytics gets added later, the Privacy Policy needs a
  cookie/tracking section added at that time.
- **Not building a working contact form.** `/contact` is a mailto link
  only, per the reasoning above.

## Testing

- `npm run build` after each new page — must stay at N pages built with no
  errors (currently 3: `/`, `/verify-email`, `/reset-password`; will become
  8 after this work: those three + `/privacy`, `/terms`, `/contact`,
  `/about`, `/404`).
- Visual check of the built HTML for the landing page against the approved
  mockup (structure/sections match, not pixel-perfect).
- No new client-side scripts are introduced by this work (all five new
  pages are static prose or a mailto link) — no fetch/CORS surface to test,
  unlike `verify-email`/`reset-password`.
