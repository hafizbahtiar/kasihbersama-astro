# Landing Page Refresh + Legal/Standard Pages Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Refresh the landing page to look trustworthy/professional (not "scammer-y") and add five standard pages (Privacy, Terms, Contact, About, 404) — all in Malay, matching the existing design tokens.

**Architecture:** A new `SiteLayout.astro` (sticky nav + slot + multi-column footer) becomes the shared chrome for the landing page and four new content pages. `AuthLayout.astro` (used by `verify-email`/`reset-password`) is untouched — different job, different chrome. Content pages are static prose wrapped in `SiteLayout`; no new client-side JS anywhere in this plan.

**Tech Stack:** Astro (static output, no SSR), no test framework in this repo — verification is `npm run build` succeeding plus grepping the built HTML for expected content.

## Global Constraints

- All new page copy is in **Malay** — matches `Layout.astro`'s `lang="ms"` and every existing page. Do not write any new user-facing copy in English.
- Brand tokens from `src/layouts/Layout.astro`'s `:root`: `--color-primary: #00695c`, `--color-primary-light: #e0f2f1`, `--color-text-primary: #1a1a2e`, `--color-text-secondary: #64748b`, `--color-surface: #f8fafb`, `--color-border: #e2e8f0`. Font is Poppins (already loaded in `Layout.astro`) — do not add another font.
- No fabricated trust signals anywhere: no testimonials, no user counts, no star ratings, no fake app screenshots. The existing "Akan datang di App Store & Google Play" honesty must be preserved verbatim.
- Contact email is `hafiz@hafizbahtiar.com` — no registered company entity exists yet, so legal copy refers to "KasihBersama" as the product/operator, never a named legal entity.
- **Do not run `git commit`.** The user commits their own changes — every task ends at its verification step, not a commit step. If you're executing this plan and reach a point where the template would normally say "commit," stop there instead and report the task as ready for the user to commit.
- Full design rationale: `docs/superpowers/specs/2026-07-19-landing-refresh-and-legal-pages-design.md` in this repo. Data-practice claims in the Privacy Policy must trace back to `kasihbersama-backend/docs/08-security-privacy.md` — don't invent data practices beyond what that doc describes.

---

### Task 1: Shared site chrome (`SiteLayout.astro`) + landing page refresh

**Files:**
- Create: `src/layouts/SiteLayout.astro`
- Modify: `src/pages/index.astro` (full rewrite)

**Interfaces:**
- Produces: `SiteLayout` Astro component, props `{ title?: string; description?: string }` (both optional, forwarded to `Layout.astro` which supplies defaults when omitted — same pattern as `AuthLayout.astro` but with optional instead of required `title`). Renders a `<slot />` for page content between a sticky nav and a footer. Consumed by every task below via `import SiteLayout from '../layouts/SiteLayout.astro';`.

- [ ] **Step 1: Create `src/layouts/SiteLayout.astro`**

```astro
---
import Layout from './Layout.astro';

interface Props {
	title?: string;
	description?: string;
}

const { title, description } = Astro.props;

const navLinks = [
	{ href: '/#ciri-ciri', label: 'Ciri-ciri' },
	{ href: '/privacy', label: 'Privasi' },
	{ href: '/terms', label: 'Terma' },
	{ href: '/contact', label: 'Hubungi' },
];
---

<Layout title={title} description={description}>
	<header class="site-nav">
		<div class="site-nav-inner">
			<a class="wordmark" href="/">KasihBersama</a>
			<nav class="nav-links">
				{navLinks.map((link) => <a href={link.href}>{link.label}</a>)}
			</nav>
		</div>
	</header>

	<slot />

	<footer class="site-footer">
		<div class="footer-inner">
			<div class="footer-brand">
				<span class="brand">KasihBersama</span>
				<p class="tagline">Menyelaraskan penjagaan keluarga, bersama.</p>
			</div>
			<div class="footer-col">
				<span class="col-title">Produk</span>
				<a href="/#ciri-ciri">Ciri-ciri</a>
				<a href="/about">Tentang</a>
			</div>
			<div class="footer-col">
				<span class="col-title">Legal</span>
				<a href="/privacy">Privasi</a>
				<a href="/terms">Terma &amp; Syarat</a>
			</div>
			<div class="footer-col">
				<span class="col-title">Hubungi</span>
				<a href="mailto:hafiz@hafizbahtiar.com">hafiz@hafizbahtiar.com</a>
			</div>
		</div>
		<div class="footer-bottom">
			<span>&copy; {new Date().getFullYear()} KasihBersama</span>
			<span>Dibuat di Malaysia 🇲🇾</span>
		</div>
	</footer>
</Layout>

<style>
	.site-nav {
		position: sticky;
		top: 0;
		z-index: 10;
		border-bottom: 1px solid var(--color-border);
		background: rgba(248, 250, 251, 0.9);
		backdrop-filter: blur(6px);
	}

	.site-nav-inner {
		max-width: 1080px;
		margin: 0 auto;
		padding: 18px 24px;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	.wordmark {
		font-size: 18px;
		font-weight: 700;
		color: var(--color-primary);
		text-decoration: none;
		letter-spacing: -0.2px;
	}

	.nav-links {
		display: flex;
		gap: 24px;
	}

	.nav-links a {
		font-size: 14px;
		font-weight: 500;
		color: var(--color-text-secondary);
		text-decoration: none;
	}

	.nav-links a:hover {
		color: var(--color-primary);
	}

	.site-footer {
		background: #0f1e24;
		color: #cbd5d1;
	}

	.footer-inner {
		max-width: 1080px;
		margin: 0 auto;
		padding: 48px 24px 28px;
		display: flex;
		flex-wrap: wrap;
		justify-content: space-between;
		gap: 32px;
	}

	.footer-brand .brand {
		font-weight: 700;
		color: white;
		font-size: 16px;
	}

	.footer-brand .tagline {
		font-size: 13px;
		color: #8ea39d;
		max-width: 240px;
		margin: 8px 0 0;
		line-height: 1.6;
	}

	.footer-col {
		display: flex;
		flex-direction: column;
	}

	.footer-col .col-title {
		font-size: 11px;
		font-weight: 600;
		text-transform: uppercase;
		letter-spacing: 0.5px;
		color: #8ea39d;
		margin-bottom: 12px;
	}

	.footer-col a {
		font-size: 13px;
		color: #d8e4e1;
		text-decoration: none;
		margin-bottom: 8px;
	}

	.footer-col a:hover {
		color: white;
	}

	.footer-bottom {
		max-width: 1080px;
		margin: 0 auto;
		border-top: 1px solid #1f3038;
		padding: 16px 24px 24px;
		display: flex;
		justify-content: space-between;
		font-size: 12px;
		color: #8ea39d;
	}
</style>
```

- [ ] **Step 2: Replace `src/pages/index.astro` with the refreshed landing page**

```astro
---
import SiteLayout from '../layouts/SiteLayout.astro';

const features = [
	{
		icon: '👪',
		title: 'Bulatan Penjagaan',
		description:
			'Jemput ahli keluarga untuk berkongsi tanggungjawab menjaga orang tersayang, dalam satu bulatan.',
	},
	{
		icon: '📝',
		title: 'Log Penjagaan',
		description:
			'Rekod aktiviti harian — makan, tidur, mood, dan lain-lain — supaya semua ahli keluarga sentiasa terkini.',
	},
	{
		icon: '💊',
		title: 'Ubat & Peringatan',
		description:
			'Jejak jadual ubat dan tandakan bila sudah diambil, tanpa tertinggal satu dos pun.',
	},
	{
		icon: '📄',
		title: 'Temujanji & Dokumen',
		description:
			'Simpan temujanji perubatan dan dokumen penting di satu tempat yang selamat dan mudah dicapai.',
	},
];

const trustPoints = ['🔒 Data disulitkan', '🇲🇾 Patuh PDPA Malaysia', '🙅 Tiada jualan data'];
---

<SiteLayout>
	<main>
		<section class="hero">
			<span class="badge">Akan datang di App Store & Google Play</span>
			<h1>Kongsikan penjagaan dengan keluarga</h1>
			<p class="subtitle">
				KasihBersama membantu keluarga menyelaraskan penjagaan orang
				tersayang — log harian, ubat, temujanji, dan dokumen, semua dalam
				satu tempat.
			</p>
		</section>

		<section class="trust-strip">
			{trustPoints.map((point) => <span class="pill">{point}</span>)}
		</section>

		<section class="features" id="ciri-ciri">
			{
				features.map((f) => (
					<div class="feature-card">
						<div class="icon-badge">{f.icon}</div>
						<h2>{f.title}</h2>
						<p>{f.description}</p>
					</div>
				))
			}
		</section>
	</main>
</SiteLayout>

<style>
	.hero {
		max-width: 720px;
		margin: 0 auto;
		padding: 96px 24px 48px;
		text-align: center;
		background: radial-gradient(
			ellipse 80% 60% at 50% 0%,
			var(--color-primary-light) 0%,
			transparent 70%
		);
	}

	.badge {
		display: inline-block;
		padding: 10px 20px;
		border-radius: 999px;
		background: white;
		border: 1px solid #cfe8e6;
		color: var(--color-primary);
		font-size: 14px;
		font-weight: 600;
		box-shadow: 0 1px 2px rgba(0, 0, 0, 0.04);
	}

	.hero h1 {
		font-size: 40px;
		font-weight: 700;
		line-height: 1.2;
		letter-spacing: -0.4px;
		margin: 20px 0 16px;
		color: var(--color-text-primary);
	}

	.subtitle {
		font-size: 17px;
		line-height: 1.6;
		color: var(--color-text-secondary);
		margin: 0;
	}

	.trust-strip {
		display: flex;
		justify-content: center;
		flex-wrap: wrap;
		gap: 10px;
		padding: 8px 24px 40px;
	}

	.pill {
		font-size: 12px;
		font-weight: 500;
		color: var(--color-text-primary);
		background: var(--color-primary-light);
		border-radius: 999px;
		padding: 8px 16px;
	}

	.features {
		max-width: 1080px;
		margin: 0 auto;
		padding: 0 24px 96px;
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
		gap: 20px;
		scroll-margin-top: 80px;
	}

	.feature-card {
		background: white;
		border: 1px solid var(--color-border);
		border-radius: 20px;
		padding: 24px;
		transition:
			box-shadow 0.15s ease,
			transform 0.15s ease;
	}

	.feature-card:hover {
		box-shadow: 0 8px 20px rgba(0, 105, 92, 0.1);
		transform: translateY(-2px);
	}

	.icon-badge {
		width: 40px;
		height: 40px;
		border-radius: 10px;
		background: var(--color-primary-light);
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 18px;
		margin-bottom: 14px;
	}

	.feature-card h2 {
		font-size: 16px;
		font-weight: 600;
		margin: 0 0 8px;
		color: var(--color-text-primary);
	}

	.feature-card p {
		font-size: 14px;
		line-height: 1.6;
		color: var(--color-text-secondary);
		margin: 0;
	}
</style>
```

- [ ] **Step 3: Build and verify**

Run: `npm run build`
Expected: `3 page(s) built` (unchanged — still just `/`, `/verify-email`, `/reset-password`), no errors.

Then verify the nav/footer/trust-strip actually landed in the output:

```bash
grep -o 'id="ciri-ciri"' dist/index.html
grep -o 'Data disulitkan' dist/index.html
grep -o 'href="/privacy"' dist/index.html
grep -o 'href="/terms"' dist/index.html
```

Expected: all four greps print a match (non-empty output).

---

### Task 2: `/privacy` page

**Files:**
- Create: `src/pages/privacy.astro`

**Interfaces:**
- Consumes: `SiteLayout` from Task 1 (`import SiteLayout from '../layouts/SiteLayout.astro';`, props `{ title?: string; description?: string }`).

- [ ] **Step 1: Create `src/pages/privacy.astro`**

```astro
---
import SiteLayout from '../layouts/SiteLayout.astro';
---

<SiteLayout
	title="Dasar Privasi — KasihBersama"
	description="Bagaimana KasihBersama mengumpul, menggunakan, dan melindungi data anda."
>
	<main class="prose-page">
		<article class="prose">
			<h1>Dasar Privasi</h1>
			<p class="updated">Kemas kini terakhir: 19 Julai 2026</p>

			<p>
				KasihBersama ("kami") menghormati privasi anda. Dasar ini
				menerangkan data yang kami kumpul, sebab kami mengumpulnya, dan
				bagaimana anda boleh mengawalnya.
			</p>

			<h2>Apa data yang kami kumpul</h2>
			<ul>
				<li>
					<strong>Maklumat akaun:</strong> e-mel, nama paparan, kata laluan
					(disulitkan, bukan disimpan sebagai teks biasa).
				</li>
				<li>
					<strong>Data penjagaan:</strong> log harian, ubat, jadual ubat,
					temujanji, tanda vital, dan nota berkaitan kesihatan yang anda
					atau ahli keluarga masukkan untuk sesuatu profil penjagaan.
				</li>
				<li>
					<strong>Dokumen dimuat naik:</strong> gambar dan dokumen (seperti
					preskripsi) yang anda lampirkan pada profil penjagaan.
				</li>
				<li>
					<strong>Token peranti:</strong> untuk menghantar notifikasi push
					(peringatan ubat, temujanji), jika anda membenarkannya.
				</li>
			</ul>

			<h2>Kenapa kami mengumpul data ini</h2>
			<p>
				Kami menggunakan data ini semata-mata untuk menyediakan
				perkhidmatan KasihBersama — menyelaraskan penjagaan antara ahli
				keluarga, menghantar peringatan, dan memaparkan kad kecemasan
				apabila diberi kebenaran. Kami tidak menggunakan data penjagaan
				anda untuk pengiklanan.
			</p>

			<h2>Perkongsian dengan pihak ketiga</h2>
			<p>
				Kami menggunakan pembekal berikut untuk menjalankan perkhidmatan
				(sebagai pemproses data, bukan untuk menjual data anda):
			</p>
			<ul>
				<li><strong>Resend</strong> — penghantaran e-mel (pengesahan akaun, tetapan semula kata laluan, jemputan).</li>
				<li><strong>Cloudflare (R2 &amp; Workers)</strong> — penyimpanan dokumen/gambar dan penghosan laman web ini.</li>
				<li><strong>Railway</strong> — penghosan pangkalan data dan pelayan bahagian belakang.</li>
				<li><strong>Firebase Cloud Messaging (Google)</strong> — penghantaran notifikasi push.</li>
			</ul>
			<p>Kami tidak menjual data peribadi anda kepada mana-mana pihak ketiga.</p>

			<h2>Hak anda</h2>
			<p>
				Anda berhak memohon eksport data penjagaan anda atau memadam
				akaun anda pada bila-bila masa. Keupayaan ini sedang dibina terus
				dalam aplikasi; buat masa ini, hantar permohonan ke
				<a href="mailto:hafiz@hafizbahtiar.com">hafiz@hafizbahtiar.com</a>
				dan kami akan uruskan secara manual. Apabila akaun dipadam,
				maklumat pengenalan (e-mel, nama, kata laluan) dinyahnamakan; rekod
				penjagaan yang dikongsi dengan ahli keluarga lain dikekalkan
				mengikut sejarah yang telah disimpan (dipadam lembut, bukan
				dipadam terus), supaya audit dan integriti bulatan penjagaan kekal
				terjaga.
			</p>

			<h2>Pengekalan data</h2>
			<p>
				Kami tidak memadam terus akaun atau rekod penjagaan — sebaliknya
				kami menyahnamakan akaun (e-mel/nama/kata laluan) dan memadam
				lembut rekod penjagaan, supaya sejarah dan log audit kekal sah
				tanpa mendedahkan identiti anda.
			</p>

			<h2>Keselamatan</h2>
			<p>
				Semua data dihantar melalui HTTPS/TLS. Kata laluan disulitkan
				menggunakan argon2id. Data disimpan disulitkan semasa rehat.
				Akses kepada setiap profil penjagaan disemak semula pada setiap
				permintaan — pembatalan akses berkuat kuasa serta-merta.
			</p>

			<h2>Proses pelanggaran data</h2>
			<p>
				Jika berlaku pelanggaran data yang menjejaskan maklumat peribadi
				anda, kami akan memberitahu pengguna yang terjejas mengikut
				tempoh yang munasabah dan mengikut keperluan Akta Perlindungan
				Data Peribadi 2010 (PDPA) Malaysia.
			</p>

			<h2>Kanak-kanak dan subjek penjagaan</h2>
			<p>
				Subjek sesuatu profil penjagaan (contohnya kanak-kanak atau warga
				emas) mungkin bukan pemegang akaun itu sendiri — data mereka
				dimasukkan oleh penjaga/ahli keluarga bagi pihak mereka, bukan
				dikumpul terus daripada mereka.
			</p>

			<h2>Perubahan kepada dasar ini</h2>
			<p>
				Kami mungkin mengemas kini dasar ini dari semasa ke semasa.
				Tarikh "kemas kini terakhir" di atas menunjukkan versi terkini.
			</p>

			<h2>Hubungi kami</h2>
			<p>
				Sebarang pertanyaan berkaitan privasi, hubungi kami di
				<a href="mailto:hafiz@hafizbahtiar.com">hafiz@hafizbahtiar.com</a>.
			</p>
		</article>
	</main>
</SiteLayout>

<style>
	.prose-page {
		display: flex;
		justify-content: center;
		padding: 64px 24px 96px;
	}

	.prose {
		width: 100%;
		max-width: 680px;
	}

	.prose h1 {
		font-size: 30px;
		font-weight: 700;
		margin: 0 0 8px;
		color: var(--color-text-primary);
	}

	.prose .updated {
		font-size: 13px;
		color: var(--color-text-secondary);
		margin: 0 0 32px;
	}

	.prose h2 {
		font-size: 19px;
		font-weight: 600;
		margin: 36px 0 12px;
		color: var(--color-text-primary);
	}

	.prose p {
		font-size: 15px;
		line-height: 1.7;
		color: var(--color-text-secondary);
		margin: 0 0 14px;
	}

	.prose ul {
		margin: 0 0 14px;
		padding-left: 20px;
	}

	.prose li {
		font-size: 15px;
		line-height: 1.7;
		color: var(--color-text-secondary);
		margin-bottom: 6px;
	}

	.prose a {
		color: var(--color-primary);
	}

	.prose strong {
		color: var(--color-text-primary);
	}
</style>
```

- [ ] **Step 2: Build and verify**

Run: `npm run build`
Expected: `4 page(s) built` (`/`, `/verify-email`, `/reset-password`, `/privacy`), no errors.

```bash
grep -o 'Dasar Privasi' dist/privacy/index.html
grep -o 'Resend' dist/privacy/index.html
grep -o 'Railway' dist/privacy/index.html
grep -o 'Firebase Cloud Messaging' dist/privacy/index.html
grep -o 'hafiz@hafizbahtiar.com' dist/privacy/index.html
```

Expected: all five greps print a match.

---

### Task 3: `/terms` page

**Files:**
- Create: `src/pages/terms.astro`

**Interfaces:**
- Consumes: `SiteLayout` from Task 1 (same signature as Task 2).

- [ ] **Step 1: Create `src/pages/terms.astro`**

```astro
---
import SiteLayout from '../layouts/SiteLayout.astro';
---

<SiteLayout
	title="Terma & Syarat — KasihBersama"
	description="Terma dan syarat penggunaan perkhidmatan KasihBersama."
>
	<main class="prose-page">
		<article class="prose">
			<h1>Terma &amp; Syarat</h1>
			<p class="updated">Kemas kini terakhir: 19 Julai 2026</p>

			<p>Dengan menggunakan KasihBersama, anda bersetuju dengan terma dan syarat berikut.</p>

			<h2>Penerimaan terma</h2>
			<p>
				Dengan mendaftar atau menggunakan perkhidmatan KasihBersama,
				anda bersetuju untuk terikat dengan terma ini. Jika anda tidak
				bersetuju, sila jangan gunakan perkhidmatan ini.
			</p>

			<h2>Penerangan perkhidmatan</h2>
			<p>
				KasihBersama ialah alat penyelarasan dan pencatatan penjagaan
				keluarga. Ia membantu ahli keluarga berkongsi log harian, jadual
				ubat, temujanji, dan dokumen bagi orang yang mereka jaga.
			</p>
			<p>
				<strong>KasihBersama bukan alat perubatan.</strong> Ia tidak
				menyediakan diagnosis, rawatan, atau nasihat kecemasan
				perubatan. Sila rujuk profesional penjagaan kesihatan yang
				bertauliah untuk sebarang keputusan perubatan.
			</p>

			<h2>Kelayakan</h2>
			<p>
				Anda mesti berusia 18 tahun ke atas untuk mendaftar akaun. Tiada
				had umur bagi subjek profil penjagaan (contohnya kanak-kanak
				atau warga emas yang dijaga) — profil bagi pihak mereka
				diuruskan oleh penjaga yang mendaftar akaun.
			</p>

			<h2>Tanggungjawab anda</h2>
			<p>
				Anda bertanggungjawab untuk memastikan maklumat yang dimasukkan
				tepat, menjaga kerahsiaan kata laluan akaun anda, dan
				memberitahu kami dengan segera jika berlaku akses tanpa
				kebenaran ke akaun anda.
			</p>

			<h2>Penggunaan yang dibenarkan</h2>
			<p>Anda bersetuju untuk tidak:</p>
			<ul>
				<li>Menggunakan KasihBersama sebagai pengganti nasihat perubatan profesional.</li>
				<li>Memuat naik kandungan yang menyalahi undang-undang atau melanggar hak pihak lain.</li>
				<li>Cuba mengakses profil penjagaan atau akaun yang bukan milik anda tanpa kebenaran.</li>
			</ul>

			<h2>Pemilikan kandungan</h2>
			<p>
				Data penjagaan yang anda masukkan kekal milik anda dan keluarga
				anda. Kami menyimpan dan memproses data tersebut semata-mata
				untuk menyediakan perkhidmatan (lihat
				<a href="/privacy">Dasar Privasi</a>).
			</p>

			<h2>Penamatan</h2>
			<p>
				Anda boleh memadam akaun anda pada bila-bila masa. Kami berhak
				menggantung atau menamatkan akaun yang melanggar terma ini.
			</p>

			<h2>Had liabiliti</h2>
			<p>
				KasihBersama disediakan "seadanya" tanpa jaminan tersirat.
				Setakat yang dibenarkan undang-undang, kami tidak bertanggungjawab
				ke atas kerugian tidak langsung yang timbul daripada penggunaan
				perkhidmatan ini. Ini tidak menghadkan liabiliti bagi kecuaian
				yang tidak boleh dihadkan di bawah undang-undang Malaysia.
			</p>

			<h2>Perubahan terma</h2>
			<p>
				Kami mungkin mengemas kini terma ini dari semasa ke semasa.
				Penggunaan berterusan selepas kemas kini bermakna anda
				bersetuju dengan terma yang dikemas kini.
			</p>

			<h2>Undang-undang yang terpakai</h2>
			<p>Terma ini ditadbir oleh undang-undang Malaysia.</p>

			<h2>Hubungi kami</h2>
			<p>
				Sebarang pertanyaan, hubungi kami di
				<a href="mailto:hafiz@hafizbahtiar.com">hafiz@hafizbahtiar.com</a>.
			</p>
		</article>
	</main>
</SiteLayout>

<style>
	.prose-page {
		display: flex;
		justify-content: center;
		padding: 64px 24px 96px;
	}

	.prose {
		width: 100%;
		max-width: 680px;
	}

	.prose h1 {
		font-size: 30px;
		font-weight: 700;
		margin: 0 0 8px;
		color: var(--color-text-primary);
	}

	.prose .updated {
		font-size: 13px;
		color: var(--color-text-secondary);
		margin: 0 0 32px;
	}

	.prose h2 {
		font-size: 19px;
		font-weight: 600;
		margin: 36px 0 12px;
		color: var(--color-text-primary);
	}

	.prose p {
		font-size: 15px;
		line-height: 1.7;
		color: var(--color-text-secondary);
		margin: 0 0 14px;
	}

	.prose ul {
		margin: 0 0 14px;
		padding-left: 20px;
	}

	.prose li {
		font-size: 15px;
		line-height: 1.7;
		color: var(--color-text-secondary);
		margin-bottom: 6px;
	}

	.prose a {
		color: var(--color-primary);
	}

	.prose strong {
		color: var(--color-text-primary);
	}
</style>
```

- [ ] **Step 2: Build and verify**

Run: `npm run build`
Expected: `5 page(s) built` (adds `/terms`), no errors.

```bash
grep -o 'Terma &amp; Syarat' dist/terms/index.html
grep -o 'bukan alat perubatan' dist/terms/index.html
grep -o '18 tahun' dist/terms/index.html
```

Expected: all three greps print a match.

---

### Task 4: `/contact` page

**Files:**
- Create: `src/pages/contact.astro`

**Interfaces:**
- Consumes: `SiteLayout` from Task 1 (same signature as Task 2).

- [ ] **Step 1: Create `src/pages/contact.astro`**

```astro
---
import SiteLayout from '../layouts/SiteLayout.astro';
---

<SiteLayout title="Hubungi Kami — KasihBersama" description="Hubungi pasukan KasihBersama.">
	<main class="prose-page">
		<article class="prose">
			<h1>Hubungi Kami</h1>
			<p>
				Ada soalan, maklum balas, atau nak laporkan isu privasi? Kami
				sedia membantu.
			</p>
			<a class="contact-email" href="mailto:hafiz@hafizbahtiar.com">hafiz@hafizbahtiar.com</a>
			<p class="note">Kami akan cuba membalas secepat mungkin.</p>
		</article>
	</main>
</SiteLayout>

<style>
	.prose-page {
		display: flex;
		justify-content: center;
		padding: 64px 24px 96px;
	}

	.prose {
		width: 100%;
		max-width: 680px;
		text-align: center;
	}

	.prose h1 {
		font-size: 30px;
		font-weight: 700;
		margin: 0 0 16px;
		color: var(--color-text-primary);
	}

	.prose p {
		font-size: 15px;
		line-height: 1.7;
		color: var(--color-text-secondary);
		margin: 0 0 24px;
	}

	.contact-email {
		display: inline-block;
		font-size: 20px;
		font-weight: 600;
		color: var(--color-primary);
		background: var(--color-primary-light);
		border-radius: 999px;
		padding: 14px 28px;
		text-decoration: none;
	}

	.note {
		margin-top: 20px;
		font-size: 13px;
	}
</style>
```

- [ ] **Step 2: Build and verify**

Run: `npm run build`
Expected: `6 page(s) built` (adds `/contact`), no errors.

```bash
grep -o 'Hubungi Kami' dist/contact/index.html
grep -o 'mailto:hafiz@hafizbahtiar.com' dist/contact/index.html
```

Expected: both greps print a match.

---

### Task 5: `/about` page

**Files:**
- Create: `src/pages/about.astro`

**Interfaces:**
- Consumes: `SiteLayout` from Task 1 (same signature as Task 2).

- [ ] **Step 1: Create `src/pages/about.astro`**

```astro
---
import SiteLayout from '../layouts/SiteLayout.astro';
---

<SiteLayout title="Tentang Kami — KasihBersama" description="Kenapa kami membina KasihBersama.">
	<main class="prose-page">
		<article class="prose">
			<h1>Tentang Kami</h1>

			<p>
				KasihBersama bermula daripada satu pemerhatian mudah: menjaga
				orang tersayang — sama ada anak kecil, ibu bapa yang menua, atau
				ahli keluarga yang sakit — jarang menjadi tanggungjawab seorang
				sahaja, tetapi selalunya bertaburan dalam mesej WhatsApp, nota
				kertas, dan ingatan yang cuba dikongsi semula setiap kali
				seseorang bertanya "macam mana keadaan dia hari ini?"
			</p>

			<p>
				Kami sedang membina KasihBersama supaya setiap ahli keluarga
				dalam bulatan penjagaan — walau di mana mereka berada — boleh
				melihat log harian, jadual ubat, dan dokumen penting yang sama,
				tanpa perlu bertanya berulang kali.
			</p>

			<p>
				Kami masih di peringkat awal. Aplikasi belum lagi tersedia di
				App Store atau Google Play, dan kami sedang membina ciri demi
				ciri dengan berhati-hati — terutamanya bahagian keselamatan dan
				privasi data, memandangkan data penjagaan adalah maklumat
				sensitif.
			</p>

			<p>
				Ada soalan atau cadangan? Kami nak dengar — hubungi kami di
				<a href="mailto:hafiz@hafizbahtiar.com">hafiz@hafizbahtiar.com</a>.
			</p>
		</article>
	</main>
</SiteLayout>

<style>
	.prose-page {
		display: flex;
		justify-content: center;
		padding: 64px 24px 96px;
	}

	.prose {
		width: 100%;
		max-width: 680px;
	}

	.prose h1 {
		font-size: 30px;
		font-weight: 700;
		margin: 0 0 24px;
		color: var(--color-text-primary);
	}

	.prose p {
		font-size: 15px;
		line-height: 1.7;
		color: var(--color-text-secondary);
		margin: 0 0 16px;
	}

	.prose a {
		color: var(--color-primary);
	}
</style>
```

- [ ] **Step 2: Build and verify**

Run: `npm run build`
Expected: `7 page(s) built` (adds `/about`), no errors.

```bash
grep -o 'Tentang Kami' dist/about/index.html
grep -o 'App Store atau Google Play' dist/about/index.html
```

Expected: both greps print a match.

---

### Task 6: Custom 404 page

**Files:**
- Create: `src/pages/404.astro`

**Interfaces:**
- Consumes: `SiteLayout` from Task 1 (same signature as Task 2).

Astro treats `src/pages/404.astro` specially: it builds to `dist/404.html` (not `dist/404/index.html` like every other route), which is what static hosts auto-detect as the custom not-found page. This repo's `wrangler.jsonc` already has `assets.not_found_handling: "404-page"`, which is Cloudflare Workers' equivalent auto-detection — this task is the missing half of that existing config.

- [ ] **Step 1: Create `src/pages/404.astro`**

```astro
---
import SiteLayout from '../layouts/SiteLayout.astro';
---

<SiteLayout
	title="Halaman Tidak Dijumpai — KasihBersama"
	description="Halaman yang anda cari tidak wujud."
>
	<main class="not-found">
		<h1>Halaman tidak dijumpai</h1>
		<p>Pautan ini tidak wujud atau telah dialihkan.</p>
		<a class="home-link" href="/">Kembali ke laman utama</a>
	</main>
</SiteLayout>

<style>
	.not-found {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		min-height: 50vh;
		padding: 64px 24px;
		text-align: center;
	}

	.not-found h1 {
		font-size: 28px;
		font-weight: 700;
		margin: 0 0 12px;
		color: var(--color-text-primary);
	}

	.not-found p {
		font-size: 15px;
		color: var(--color-text-secondary);
		margin: 0 0 24px;
	}

	.home-link {
		display: inline-block;
		font-size: 14px;
		font-weight: 600;
		color: white;
		background: var(--color-primary);
		border-radius: 999px;
		padding: 12px 24px;
		text-decoration: none;
	}
</style>
```

- [ ] **Step 2: Build and verify**

Run: `npm run build`
Expected: `8 page(s) built`, no errors.

```bash
ls dist/404.html
grep -o 'Halaman tidak dijumpai' dist/404.html
```

Expected: `ls` finds the file at that exact path (not `dist/404/index.html`); the grep prints a match.

---

### Task 7: Full-site verification + `TODO.md` update

**Files:**
- Modify: `TODO.md` (repo root)

- [ ] **Step 1: Full clean build, both modes**

```bash
rm -rf dist
npm run build
```

Expected: `8 page(s) built`, no errors. Then:

```bash
npm run build:staging
```

Expected: `8 page(s) built`, no errors (confirms the new pages don't accidentally depend on anything env-specific — they don't call `apiBaseUrl` at all, unlike `verify-email`/`reset-password`).

- [ ] **Step 2: Confirm every nav/footer link resolves to a real built page**

```bash
for route in privacy terms contact about; do
  test -f "dist/$route/index.html" && echo "OK: $route" || echo "MISSING: $route"
done
test -f dist/404.html && echo "OK: 404" || echo "MISSING: 404"
```

Expected: five `OK:` lines, no `MISSING:` lines.

- [ ] **Step 3: Update `TODO.md`**

Read the current `## Next` section first (it was last edited when `.well-known` placeholders were added), then add a new `## Done` entry (or extend the existing one) describing what shipped: `SiteLayout.astro` shared chrome; landing page restructured (gradient hero, trust-pill strip, icon-badge feature cards, multi-column footer); five new pages (`/privacy`, `/terms`, `/contact`, `/about`, `/404`), all grounded in `kasihbersama-backend/docs/08-security-privacy.md` for the privacy content. Note explicitly, as a follow-up (not a blocker): the Privacy Policy and Terms currently list no registered legal entity, just the contact email `hafiz@hafizbahtiar.com` — update both pages if/when a company is registered. Also note: `AuthLayout.astro`-based pages (`verify-email`, `reset-password`) still don't link to `/privacy`/`/terms` — deliberately out of scope per the design spec's Non-goals, tracked here as a possible fast follow.

---

## Self-Review Notes

- **Spec coverage:** every section of `docs/superpowers/specs/2026-07-19-landing-refresh-and-legal-pages-design.md` maps to a task — `SiteLayout` + landing (Task 1), `/privacy` (Task 2), `/terms` (Task 3), `/contact` (Task 4), `/about` (Task 5), `404.astro` (Task 6). The spec's Non-goals (not touching `AuthLayout`, no legal review, no analytics, no working contact form) are respected — no task does any of those.
- **Placeholder scan:** no TBD/TODO markers; every task has complete file contents, not descriptions of content.
- **Type consistency:** `SiteLayout` props (`{ title?: string; description?: string }`) declared once in Task 1 and used identically (same import path, same prop names) in Tasks 2–6.
