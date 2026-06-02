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

## [0.1.7] — 2026-06-02

### Fixed

- **One-click Organize no longer creates duplicate tab groups.**
  Every time you ran Organize, it called `chrome.tabs.group()` without
  a target group id — which *always* creates a brand-new group. So if a
  "Github" group already existed from a previous Organize, a second
  identical "Github" group was spawned, and colors reshuffled on every
  run. Organize now indexes the groups already open in the window by
  their label and **merges** matching domains into the existing group
  instead of stacking a new one.
- **Bonus cleanup of pre-existing duplicates.** Because Organize now
  folds every tab of a domain into the *first* group with that label,
  running it once collapses any duplicate groups you'd accumulated —
  the now-empty extras are auto-removed by Chrome.
- **Stray single tabs rejoin their group.** A domain with only one open
  tab is now folded back into its existing group (previously it was
  left ungrouped because grouping required ≥2 tabs).
- **No more color flicker.** Reusing a group never recolors or renames
  it, so groups you've customised stay put. Colors are only assigned to
  newly created groups.

---

## [0.1.6] — 2026-05-12

### Fixed (architectural)

- **Tracker now records only time we have positive evidence for.**
  v0.1.5 capped any single visit at 4 hours to prevent the worst
  symptoms of service-worker death gaps. That was a band-aid: it still
  let a dead SW gap inflate a session by up to 4 hours, and it would
  silently truncate the (rare) legitimate long sessions. v0.1.6
  replaces the cap with a `lastAliveAt` liveness marker: every signal
  that could only come from a live, focused Chrome (heartbeat fire,
  user interaction, tab event) advances the marker. When a session
  flushes, its effective end time is bounded by `lastAliveAt + 90s`,
  so any gap longer than ~1.5 heartbeats is dropped as dead time
  rather than credited as activity. Legitimate long sessions are now
  unaffected — the heartbeat already slices them into ~1-minute Visit
  rows, so the dashboard totals still sum to the real wall time.

### Removed

- The arbitrary 4-hour per-visit cap introduced in v0.1.5 — superseded
  by the liveness-marker fix above.

---

## [0.1.5] — 2026-05-12

### Fixed

- **Critical · tracker no longer accumulates time on unfocused Chrome.**
  Earlier versions credited time spent in other apps (VS Code, WeChat,
  any non-Chrome window) to whichever Chrome tab was last active. The
  worst real-world case observed was a single download page showing
  14 hours of "active" time. The heartbeat now bails when Chrome isn't
  the foreground application, so any miscounting is bounded to the
  60-second heartbeat interval at worst.
- **Defensive cap on single-visit time.** Any individual visit's active
  time is now hard-capped at 4 hours in the database layer. Even if a
  regression slips back into the tracker, no single row can poison the
  dashboard with impossible numbers — you'll see a warning in the
  console instead.
- **Race condition in tracker state.** All session mutations are now
  serialized through a single mutex. Concurrent events (heartbeat +
  tab close + idle change firing at the same moment) can no longer
  read-modify-write the same record and double-count time.
- **Crash on the "All time" view.** Power users with 100,000+ visits
  no longer hit `RangeError: Maximum call stack size exceeded` when
  switching scope to All (caused by `Math.min(...visits)` argument
  overflow — replaced with explicit loop).
- **Switches-per-hour metric** was using accumulated active time as
  the denominator, producing absurd values like "125 switches/hour"
  for users who barely browsed. Now uses the scope's wall-clock window.

### Background

The 14-hour download-page report turned into a full data-correctness
audit of the tracker → DB → queries pipeline. Five Critical-tier
issues were found and fixed in this release; the remaining
medium/low-priority issues from the audit (DST bucket alignment, URL
query-string fragmentation, `chrome.storage.sync` quota guards) are
queued for v0.1.6 / v0.2.0.

Pre-existing bad rows (visits longer than ~30 minutes that you don't
recognise) will remain in your IndexedDB until manually deleted —
this fix prevents new ones but doesn't auto-clean old ones. A
single-URL delete affordance is on the v0.1.6 list.

---

## Marketing site · 2026-05-12

> Pure marketing-site update. **No extension changes.** Extension remains on v0.1.4.

### Added (siteslogs.com)

- **Discount code support on checkout** — `siteslogs.com/checkout.html?discount=CODE`
  now auto-applies a Paddle discount in the checkout overlay (magic-link style,
  zero typing for the recipient). The page also surfaces a "Have a discount code?"
  expandable input as a manual fallback, so paying customers who got a code via
  email or social can apply it without hunting for Paddle's hidden coupon link.
- **Visual confirmation** when a discount is applied (small ember-coloured
  "Discount FRIEND applied" badge above the loader), so recipients know the link
  worked before the Paddle overlay opens.

### Background

Until today, the Paddle overlay's coupon field was technically present but
buried — most recipients would never find it. With promo/complimentary
licenses now flowing through Paddle 100%-off codes (see maintainer
`PROCESS.md` §6), the checkout page needs to make the discount path
first-class, not a hidden corner.

---

## [0.1.4] — 2026-05-11

### Changed

- **CWS listing title + short summary** rewritten for store-search SEO.
  Title: `SiteLogs` → `SiteLogs · Local-first time tracker + tabs`.
  Summary: rewritten to lead with "Local-first browsing analytics +
  one-click tab organizer" — the two highest-volume keywords for the
  category, with the privacy wedge kept in front.

### Unchanged

- **No functional changes.** Same code as 0.1.3, only the
  `manifest.json` `name` and `description` strings were updated.
- Same privacy posture, same features, same data shape.

### Background

CWS internal search weights `name` and `description` heavily. The old
title carried zero search-volume keywords, so users searching for
"time tracker" or "tab manager" in the Chrome Web Store never saw us
in results. This release fixes the discoverability gap.

---

## [0.1.3] — 2026-05-11

### Changed

- **Brand-soul copy refresh** on the three transactional touchpoints
  the user actually reads — first-run history-import modal, dashboard
  empty state, and license-key delivery email. All three now match
  the manifesto's "memory tool / cartographer" voice instead of the
  default "thanks for using our productivity tool" feel. Bilingual.
- **License email signature** changed from a generic "The SiteLogs
  team" to **shyenx**, with a closing italic line: *"May your map of
  hours come back richer than you remember."*

### Site polish

- All outbound `target="_blank"` links got `rel="noopener"` (security:
  closes the tabnabbing window).
- `/404.html` rewritten as a branded recovery page with the
  cartographer voice ("Even a careful cartographer drops a lamp now
  and then") and a 4-tile recovery grid pointing readers at Home /
  Why / Guide / Pricing.
- Language toggle (EN/中) is now visible on mobile — the previous
  `hidden md:inline-flex` kept it desktop-only.

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
