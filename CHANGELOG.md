# GroceryScan — Changelog

All notable changes to this project are documented here.
Format: `[version] YYYY-MM-DD — description`

---

## [1.1.3] 2026-06-29 — Rear camera fix (take 2)

### ✅ Fixed
- **Removed `exact: 'environment'` constraint** — this was causing `overconstrained` errors on Android, which fell back to the front camera
- **Better rear camera detection** — now searches for "back", "rear", or "environment" in camera labels; if no match, uses the last camera in the list (typically rear on dual-camera phones)
- **Uses device ID directly** — when `getCameras()` succeeds, passes the actual camera device ID string to `start()`, which is more reliable than `facingMode` constraints
- **Removed buggy retry loop** — the old `overconstrained` catch block created a new scanner instance without cleanup, causing DOM conflicts

---

## [1.1.2] 2026-06-29 — Camera fix + Auto-capture

### ✅ Fixed
- **Rear camera selection** — now explicitly enumerates devices and picks the camera with "back" in its label. Falls back to `facingMode: { exact: 'environment' }`, then simple `environment` if overconstrained
- **Auto-capture on new product** — when the New Product modal opens, OCR auto-runs after 600ms if the camera is still active. No need to tap a button. Button relabeled to "📷 Re-capture" for retries

---

## [1.1.1] 2026-06-29 — Remove Netlify references, update to Cloudflare Pages

### ✅ Changed
- README: replaced all Netlify deploy instructions with Cloudflare Pages setup
- index.html: error messages now reference Cloudflare Pages instead of Netlify

---

## [1.1.0] 2026-06-29 — Scanner Sensitivity + OCR Auto-Capture

### ✅ What's new
- **OCR label capture** — scan a new product barcode, then tap "📷 Capture from Camera" or "🖼️ Photo" in the New Product modal to auto-read the label and fill in product name & price
- **Scanner sensitivity boosted** — FPS bumped from 10 → 30, scan box enlarged (260×160), cooldown reduced 2.5s → 1.2s
- **More barcode formats** — now supports CODE_128, EAN-13, EAN-8, UPC-A, UPC-E, CODE_39
- **Camera resolution hints** — requests 1280×720 ideal resolution for sharper scans
- **Audio beep on scan** — short confirmation tone every successful read
- **Version footer** — shows version and build date at the bottom of every screen

### 🔧 Technical notes
- Tesseract.js loaded lazily from CDN (~10MB, first capture only)
- OCR parser filters out dates, weight, per-kg prices, and supermarket names
- Prefers "Total Price" label, then largest non-per-kg price found
- No external barcode lookup API (keeps scans fast, offline-friendly)

---

## [1.0.0] 2026-03-08 — Stable Clean Rebuild

### ✅ What's working
- Barcode / QR code scanner using `html5-qrcode` library (Android Chrome compatible)
- Camera requires **HTTPS** — works via any HTTPS host (Cloudflare Pages, GitHub Pages, ngrok), not via local file
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
3. Push to GitHub (Cloudflare Pages auto-deploys on every push)
