# GroceryScan — Changelog

All notable changes to this project are documented here.
Format: `[version] YYYY-MM-DD — description`

---

## [1.0.0] 2026-03-08 — Stable Clean Rebuild

### ✅ What's working
- Barcode / QR code scanner using `html5-qrcode` library (Android Chrome compatible)
- Camera requires **HTTPS** — works via Netlify URL, not local file
- Manual barcode entry fallback (no camera needed)
- Product database saved in browser localStorage — scan once, remembered forever
- Inline product editing — tap price badge or ✏️ to edit name/price without leaving the screen
- **Price change confirmation modal** — when editing a product price, shows old vs new with % difference, lets you Update / Keep / Cancel
- **Verify before add toggle** — when ON, every scan shows a confirmation before adding to cart
- Cart with quantity controls (+ / −) and per-item delete
- Budget ring with visual fill, 80% warning, and over-budget alert
- Shopping history — each checkout is saved with full item breakdown
- Fully offline after first load (data stays on device)

### 🔧 Technical notes
- Single HTML file, no build step required
- Scanner library: `html5-qrcode@2.3.8` via jsDelivr CDN
- State stored in `localStorage` key `gs3`
- Compatible: Android Chrome (HTTPS), Desktop Chrome (HTTPS)

---

## [0.3.0] 2026-03-08 — Camera Debugging (Broken)

### Changes attempted
- Switched from ZXing to html5-qrcode
- Added CDN fallback chain (unpkg → jsdelivr)
- Added `typeof Html5Qrcode` safety check
- Added retry button

### Issues
- Multiple partial edits caused duplicate JS blocks
- `unpkg` CDN failed to load library on some networks
- Scanner code became inconsistent across edits

---

## [0.2.0] 2026-03-07 — Price Confirmation + Inline Edit

### Added
- Inline product editing in Products tab (tap price badge to open editor)
- Price change confirmation modal with old/new comparison and diff badge
- "Always confirm before adding" toggle on Scan screen
- "Add to Cart" button directly from product list

### Fixed
- Nav active state now updates across all screens correctly
- Products now sorted alphabetically

---

## [0.1.0] 2026-03-07 — Initial Build

### Added
- 5-screen PWA layout: Home, Scan, Cart, Products, History
- Barcode scanner using ZXing library
- Product database (scan once, save forever)
- Budget ring with warning system
- Shopping history with itemized checkout records
- Manual barcode entry
- Mobile-first dark green theme

---

## How to deploy updates

1. Make changes to `index.html`
2. Commit: `git add . && git commit -m "vX.X.X — description"`
3. Drag `index.html` to Netlify **Deploys** tab
4. App updates automatically for all users within seconds
