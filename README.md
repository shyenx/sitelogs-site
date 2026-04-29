# sitelogs-site

Marketing site for SiteLogs — a local-first browsing-analytics Chrome
extension.

## Stack

Plain static HTML + Tailwind CDN. No build step.

## Pages

- `index.html` — landing (hero, features, pricing, install)
- `guide.html` — user guide
- `privacy.html` — privacy policy (canonical)
- `contact.html` — email + GitHub issues
- `404.html`

## Local preview

```bash
python3 -m http.server 8000
# open http://localhost:8000
```

## Deployment

GitHub Pages, served from `main` branch root. Settings → Pages →
Branch: `main`, Folder: `/ (root)`.

Default URL: `https://shyenx.github.io/sitelogs-site/`

## TODO before launch

- [ ] Replace placeholder email in `contact.html` (`support.sitelogs@gmail.com`) with the real address.
- [ ] Take real product screenshots, drop in `assets/screenshots/`, link them on the landing page.
- [ ] Generate `assets/og.png` (1200×630) for social sharing.
- [ ] When the Chrome Web Store listing is live, replace the "Coming soon" install button on `index.html` with the actual store URL.
- [ ] If/when registering a custom domain, set CNAME and update internal links if any go absolute.
