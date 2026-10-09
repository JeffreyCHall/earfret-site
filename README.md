# earfret.com

The marketing site for EarFret: plain HTML and one stylesheet, no build step, no external
scripts, fonts or trackers. Every link and asset path is relative, so the same files work at a
subpath (`https://jeffreychall.github.io/earfret-site/`) and at a domain root
(`https://earfret.com/`). Open `index.html` in a browser to preview it, or run
`python3 -m http.server` in this folder.

| File | What it is |
|---|---|
| `index.html` | Home: hero, how it works, features, Apple Watch, Mac and iPad, No instrument mode, progress, pricing (no prices), FAQ, beta/waitlist |
| `privacy.html` | Privacy policy, derived from the app's code (see the comment at the top) |
| `terms.html` | Terms of use. **Draft: needs your review, not legal advice** |
| `press.html` | Press kit: description, facts, features, asset placeholders |
| `style.css` | Shared styles; light and dark via `prefers-color-scheme`; accent = the app's AccentColor |
| `img/` | Optimized screenshots (WebP) and the 1200×630 social card `og-image.jpg` |
| `favicon-64.png`, `apple-touch-icon.png` | From the app icon (the favicon is also the header logo) |
| `press/earfret-icon-1024.png` | App icon download for the press kit |

### Images

All page images are real app screenshots from `~/Library/Caches/earfret-screens/`, exported by a
small PIL script (not kept in the repo):

- iPhone: `store-iphone/iphone-<name>.png` → `img/iphone-<name>.webp`, and
  `after-dark/iphone-dark-<name>.png` → `img/iphone-<name>-dark.webp` (600 px wide, WebP q78).
  The page shows the dark version through `<picture><source media="(prefers-color-scheme: dark)">`,
  so each visitor downloads only one of the two.
- Apple Watch: `watch/*.png` → `img/watch-*.webp` (416×496, q82).
- Mac: `mac-real-set/mac-*.png` → `img/mac-*.webp` (1600 px, keeps the transparent window corners).
- iPad: `store-ipad/ipad-scales-caged.png` → `img/ipad-scales-caged.webp` (900 px).
- `img/og-image.jpg`: orange gradient + app icon + "Answer with your guitar." + an iPhone
  screenshot, 1200×630 JPEG.

Device frames (iPhone, iPad, Watch) are drawn in CSS around the raw screenshots. To swap a
screenshot, export it at the same size and keep the `width`/`height` attributes in the HTML.
Every image has alt text describing what's on screen; update it if the screenshot changes.
Images below the hero use `loading="lazy"`. The whole site is about 1.4 MB.

## Publish on Cloudflare Pages (recommended)

Either way, the output directory is this `site/` folder and there is no build command.

**Option A: connect the Git repo** (redeploys on every push)

1. Push the EarFret repo to GitHub (it can stay private).
2. Cloudflare dashboard › Workers & Pages › Create › Pages › Connect to Git, pick the repo.
3. Build settings: Framework preset **None**, build command **empty**, build output directory
   **`site`**, production branch `main`.
4. Deploy. The site appears at `<project>.pages.dev`.

**Option B: direct upload** (no Git connection)

1. Workers & Pages › Create › Pages › Upload assets, name the project `earfret`.
2. Drag in the contents of `site/` (or run `npx wrangler pages deploy site --project-name earfret`
   from the repo root after `npx wrangler login`).
3. Repeat the upload whenever the site changes.

**Custom domain**

1. Register `earfret.com` (Cloudflare Registrar keeps DNS in the same place).
2. Pages project › Custom domains › Set up a custom domain › `earfret.com`, and again for
   `www.earfret.com`. Cloudflare adds the DNS records and the TLS certificate.
3. Optional: a redirect rule from `www.earfret.com/*` to `https://earfret.com/$1`.

**Email for support@ / press@**: Cloudflare Email Routing (domain › Email › Email Routing) can
forward `support@earfret.com` and `press@earfret.com` to a personal inbox without exposing it.
Replying *as* those addresses needs a mail provider or a "send as" setup.

## Alternative: GitHub Pages

GitHub Pages serves from the repo root or `/docs`, not `/site`, so use a workflow:

1. Repo › Settings › Pages › Source: **GitHub Actions**.
2. Add `.github/workflows/pages.yml` that checks out the repo, runs
   `actions/upload-pages-artifact` with `path: site`, then `actions/deploy-pages`.
   (Or push `site/` to a separate public repo and serve that repo's root.)
3. Settings › Pages › Custom domain: `earfret.com`. At the DNS host, add A records for the apex
   (185.199.108.153, .109.153, .110.153, .111.153) and a CNAME `www` → `<user>.github.io`.
   Tick **Enforce HTTPS** once the certificate is issued.

Note: GitHub Pages on a free account requires the repo to be public. Cloudflare Pages doesn't.

## TODO before launch

Search the HTML for `TODO` to find each one in place.

**Links and accounts**
- [ ] `index.html` › "Join the beta" (header, hero) and "Join the TestFlight beta" (bottom CTA):
      set each `href` to the TestFlight public link (`https://testflight.apple.com/join/…`).
      Until then they scroll to / focus the waitlist form.
- [ ] `index.html` › App Store badge: replace the dashed placeholder with Apple's official
      "Download on the App Store" badge artwork (download it from Apple's marketing resources at
      developer.apple.com/app-store/marketing/guidelines; don't modify it) and set `href` to the
      App Store URL once the app is live.
- [ ] `index.html` › waitlist: sign up for a newsletter service (e.g. Buttondown), then set the
      form's `data-endpoint` to its subscribe URL (Buttondown:
      `https://buttondown.com/api/emails/embed-subscribe/<username>`, input name `email`).
      While it says `TODO`, the form never submits; it shows "the waitlist isn't open yet".
- [ ] Create the `support@earfret.com` mailbox or forward (footer of every page, privacy, terms).
- [ ] Create `press@earfret.com` (press page), or change it to support@.
- [ ] **When earfret.com is live:** uncomment the `<link rel="canonical">` on every page, and in
      `index.html` change `og:url`, `og:image` and `twitter:image` from
      `https://jeffreychall.github.io/earfret-site/…` to `https://earfret.com/…` (social tags must
      be absolute URLs, so they're the one place the site isn't relative).

**Content**
- [ ] `index.html` › 20-second demo video: replace the placeholder with a `<video>` (snippet in
      the comment; keep it small, add captions if there's speech).
- [x] `og:image` (1200×630) for link previews: `img/og-image.jpg`.
- [x] Real screenshots on the home page (iPhone light/dark, iPad, Mac, Apple Watch).
- [ ] `press.html` › add the logo (SVG/PNG) and full-resolution screenshot downloads (the icon
      is already there).
- [x] Favicon and touch icon from the real app icon.
- [ ] `terms.html` › **review the whole draft** (ideally with a lawyer) and fill in governing law.
- [ ] Re-check `privacy.html` against the shipping build (it reflects the code on 2026-10-08).
      If the app ever adds analytics, crash reporting or any server, update it and the App Store
      privacy label first.
- [ ] Confirm Game Center leaderboards/achievements are actually live in App Store Connect; if
      Game Center isn't enabled at launch, remove it from the "60-second challenges" card on the
      home page, the press facts and the privacy policy's Game Center section.
- [ ] Confirm the minimum OS versions (iOS/iPadOS 17, macOS 14, watchOS 10 from `project.yml`,
      quoted in the FAQ and press kit) and the language list are still right at launch.
- [ ] Confirm the home page's Apple Watch claims match the shipping Watch app (quiz types,
      5/10/20 questions, flame, notification quizzes, ear quizzes with sound).
- [ ] The name "EarFret" is still marked as a working name in `docs/PLAN.md` (trademark/App
      Store search). Settle it before registering the domain.

**In the app** (not the site)
- [ ] Point the paywall's privacy policy and terms links at `https://earfret.com/privacy.html`
      and `https://earfret.com/terms.html` (`docs/PLAN.md` notes they're StoreKit defaults), and
      enter the same URLs in App Store Connect (Privacy Policy URL, and the Terms of Use / EULA
      link in the description or a custom EULA). Support URL: `https://earfret.com/`.
