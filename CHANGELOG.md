# SiteLogs · Changelog

> All notable changes to the **SiteLogs Chrome extension** are documented here.
> Format follows [Keep a Changelog](https://keepachangelog.com/) and the
> project uses [Semantic Versioning](https://semver.org/).
>
> The canonical install path for end users is the
> [Chrome Web Store](https://chromewebstore.google.com/detail/cgmjcajdmdhmojploigcijmghbchmejj) —
> we don't distribute the extension binary anywhere else. This file (and the
> [GitHub Releases page](https://github.com/shyenx/sitelogs-site/releases))
> exists so you can see exactly what changed between versions before
> Chrome auto-updates you.

---

## Marketing site · 2026-05-10

> Pure marketing-site rebuild. **No extension changes.** Extension remains on v0.1.2.

### Added (siteslogs.com)

- **Conversion-first homepage** — restructured from 8 sections to 12,
  adding Problem / Solution / Personas / Comparison / FAQ / Final CTA.
  Hero headline now anchors directly against Chrome's built-in History
  to make the value proposition land in two seconds.
- **`/use-cases.html`** — three long-form persona stories (indie maker,
  knowledge worker, student/researcher). Each is ~600 words showing
  how SiteLogs changes a typical day.
- **`/compare.html`** — full capability comparison vs typical web-time
  extensions (cloud-sync subscriptions vs SiteLogs's local-first
  one-time model), with per-row explanations.
- **`/faq.html`** — 20+ questions across Privacy, Pricing, Activation,
  Browser, and Features sections.
- **Five alternating left-right feature blocks** on the homepage in
  place of the prior 6-grid card list, each with a full-size screenshot.
- **30-day money-back guarantee callout** on the Pricing section,
  visually separated for trust-building.
- **Final CTA card** — full-width ink-on-cream block at the bottom of
  every page so visitors hit "Add to Chrome" twice during a scroll.

### Changed (siteslogs.com)

- **sitemap.xml** — adds `/use-cases.html`, `/compare.html`, `/faq.html`.
- **All headers** — nav unified across all sub-pages, includes the new
  "Use cases" entry.
- **Footers** — unified across all pages with a consistent link list.

---

## [0.1.2] — 2026-05-10

### Added

- **Public roadmap** in this repo's README — mirrors the maintainer's
  internal punch-list so users see exactly what's shipped, next up, and
  later. Each bullet links to the issue tracker for "move it up" requests.
- **Standalone changelog** (this file) — every shipped change ends up
  here with rationale, not just a one-line release note.
- **Standardised release routine** (in the maintainer repo's TODO.md) —
  bump → build → zip → README sync → tag → push → `gh release` →
  CWS upload, in that order. Mistakes like accidentally publishing
  build artefacts to private channels were caught and fixed during
  this release.

### Changed

- **Compact tab-group titles** — One-click **Organize** used to label
  each group with the full domain. Group names now strip the TLD and
  any country suffix, returning the brand segment only:
  - `mail.google.com` → `Google`
  - `news.bbc.co.uk` → `Bbc`
  - `github.com` → `Github`
  - Long brand names cap at 14 chars with an ellipsis.
- **Privacy-policy paragraph in README** — clarifies the payment provider
  is Paddle (Merchant of Record) rather than referencing the previous
  payment integration.

---

## [0.1.1] — 2026-05-09 *(internal — not shipped to Chrome Web Store)*

### Added

- **Live paid-licensing flow** — flipped `LICENSE_LAUNCHED` from `false`
  to `true` after Paddle production verification cleared. Buy buttons on
  the marketing site, in the extension Settings → License panel, and on
  the dedicated `/pricing` page now redirect to the real Paddle checkout
  overlay.
- **License-key delivery email** — a hand-crafted HTML + plaintext email
  (cream + ember branded) goes out via Resend within seconds of a
  successful payment. Includes the key, activation steps, install CTA,
  and a reply-to that lands in the maintainer's inbox.
- **Confetti celebration on activation 🎉** — three-stage burst (main
  cannon → side cannons → final sparkle) tinted in the SiteLogs
  brand palette plays when the user successfully activates a license.
- **`?` keyboard-shortcut help modal** — pressing `?` (Shift+/) anywhere
  in the dashboard opens a clean overlay listing all keyboard shortcuts
  with their destinations. Replaces the cryptic `g + o/t/h/s/a/,`
  footer hint with a discoverable one-key trigger.
- **Worker admin panel** at `/admin` (token-gated) for maintainer
  support ops — view license records, reset device slots, change
  activation limits, revoke / restore licenses.

### Fixed

- **`thanks.html` no-transaction-id bug** — Paddle's `settings.successUrl`
  was firing before the `eventCallback` could append `?txn=<id>`, so the
  thanks page couldn't look up the freshly minted license. Removed
  `successUrl` and rely solely on the event callback for the redirect.

---

## [0.1.0] — 2026-05-01 *(initial public release)*

### Added

- **First public release on the Chrome Web Store** — listing live at
  [chromewebstore.google.com/.../cgmj…ejj](https://chromewebstore.google.com/detail/cgmjcajdmdhmojploigcijmghbchmejj).
- **Local-first time tracking** — every visit, time-on-site, and
  category breakdown lives in IndexedDB on the user's own machine. No
  telemetry. No external server.
- **Custom new tab dashboard** — replaces Chrome's default new tab page
  with the SiteLogs dashboard, including pinned-site tiles, top
  domains, time-of-day heatmap, and category trends.
- **One-click tab organize** — dedupes by URL, groups remaining tabs
  by domain into Chrome's native colored tab groups, leaves pinned
  tabs alone.
- **Sessions** — save the current window as a named workspace, restore
  it later in a new window.
- **Tags & rules** — attach freeform tags to URLs / domains; override
  the built-in 200+ category dictionary with your own domain or regex
  rules; per-category daily focus budgets with progress bars.
- **First-run history import** — opt-in modal at first install offers to
  pre-populate the dashboard from the user's existing Chrome history
  (30 days / 90 days / 1 year / All), so the product has data to show
  on day 1 instead of waiting 7 days.
- **Bilingual UI (EN / 中文)** — every label, message, and category
  display name has both an English and Chinese version, switchable in
  Settings.
- **Free trial period** — every install gets 7 days of full unlocked
  access before any paywall, no credit card required.

### Privacy

- All visit data lives in your browser's IndexedDB on this device only.
- User preferences (rules, tags, pinned sites, budgets) sync via
  `chrome.storage.sync` if Chrome Sync is enabled. Visit data does not.
- The only outbound network calls are Google Fonts CSS (will be
  self-hosted in a future release) and the license server when
  activating a paid license.

---

[0.1.2]: https://github.com/shyenx/sitelogs-site/releases/tag/v0.1.2
[0.1.1]: https://github.com/shyenx/sitelogs-site/releases/tag/v0.1.1
[0.1.0]: https://github.com/shyenx/sitelogs-site/releases/tag/v0.1.0
