# GroceryScan — Changelog

All notable changes to this project are documented here.
Format: `[version] YYYY-MM-DD — description`

---

## [1.2.0] 2026-06-29 — On-screen camera debug panel

### ✅ Added
- **Camera info panel on Scan screen** — a small card shows the number of cameras detected, their names, and which one is currently active. No need for DevTools anymore — everything is visible on screen
- **Debug panel updates on toggle** — when you tap "🔄 Switch Camera", the panel shows which camera was switched to
- **Debug panel clears on Reset** — tapping "↺ Reset" clears the debug panel and localStorage
- **Debug panel hidden when camera is off** — stops cluttering the UI when not needed
- **Debug info shown in startScanner** — after the camera starts successfully, the panel adds a line showing which camera is actually active

### 🔧 Why this was needed
Chrome on mobile doesn't have DevTools, so the console logging from v1.1.9 was useless. This on-screen panel lets you see what's happening with camera detection without any developer tools.

---

## [1.1.9] 2026-06-29 — Camera reset + toggle fix + better rear detection

### ✅ Fixed
- **Added "↺ Reset" button** — clears the saved camera preference from localStorage and stops the camera. Tap it, then tap Start Camera again to force fresh camera enumeration without any saved state
- **Toggle now works even when `getCameras()` fails** — fallback mode toggles between `facingMode: 'environment'` and `facingMode: 'user'` when device enumeration isn't available
- **Better rear camera detection** — now also checks for "facing back", "world" in camera labels. Added `console.log` so you can check DevTools to see what cameras were detected
- **Toggle button now visible in fallback mode** — even if only 1 camera was enumerated, the toggle shows if the camera started successfully (some devices report 1 camera but actually support both)
- **Console logging for camera names** — open Chrome DevTools → Console to see the full list of detected cameras with labels and IDs

### 🔧 Why the front camera was opening
The `_savedCamId` in localStorage was likely pointing to the front camera from a previous session. The saved ID persists across page reloads, so every time you opened the app, it kept using the front camera. The **Reset** button clears this.

---

## [1.1.8] 2026-06-29 — Camera resolution + captured frame preview

### ✅ Changed
- **Added `videoConstraints` back** — `{ width: { ideal: 1280 }, height: { ideal: 720 } }` requests higher resolution from the camera without forcing it (uses `ideal` not `exact`, so it won't crash on unsupported cameras)
- **Larger qrbox** — increased from 260×160 to 320×200. This makes the user hold the phone slightly further back, so the full label (barcode + name + price) is more likely to be in the camera frame
- **Captured frame preview in modal** — when a new product is scanned, the modal now shows a small thumbnail of the exact frame that was captured. You can see what the OCR is trying to read. If the frame is blurry, dark, or missing the label, tap "📷 Re-capture" while pointing the camera at the full label
- **Better status messages** — "Could not read label" now says: "Make sure name + price are visible in the frame above, then tap Re-capture."
- **Re-capture also updates preview** — tapping Re-capture refreshes both the preview image and runs OCR again

### 🔧 Why the OCR was failing
The browser defaults to a low camera resolution (often 320×240 or 640×480) when no `videoConstraints` are specified. At that resolution, the small text on supermarket shelf tags is too tiny for Tesseract to read. The higher resolution request + larger qrbox should fix this.

---

## [1.1.7] 2026-06-29 — Frame capture before modal + canvas fallback

### ✅ Fixed
- **Frame captured BEFORE modal opens** — the `drawImage()` call now happens before `openPM()`, so the frame is the exact moment the barcode was detected, not after the user moves the camera
- **Canvas fallback** — if the `<video>` element can't be read, the code falls back to `html5-qrcode`'s internal `<canvas>` element (which also contains the live frame)
- **Dimension validation** — checks `video.videoWidth > 0` and `video.videoHeight > 0` before drawing; if zero, falls back to canvas
- **15-second OCR timeout** — prevents the "Reading label..." spinner from hanging forever if Tesseract gets stuck
- **Same fixes applied to manual "Re-capture" button** — the manual capture button also tries video then canvas fallback

---

## [1.1.6] 2026-06-29 — Fix camera toggle + async OCR feedback

### ✅ Fixed
- **Removed duplicate `startCamera` function** — a leftover from v1.1.4 was overriding the v1.1.5 code, causing the toggle button logic to never run
- **Fixed object comparison bug** — `cameraId === { facingMode: 'environment' }` was always false (object reference comparison), so saved camera IDs weren't being used properly. Now uses a boolean flag `usingSaved`
- **Always enumerate cameras** — even when a saved camera ID exists, `getCameras()` is called so `_camDevices` is populated, which is required for the toggle button to appear
- **OCR now opens modal immediately** — instead of making the user wait while OCR runs, the modal opens instantly with "Reading label..." status. Fields auto-fill when OCR completes. If OCR fails, the status updates to show the error and the user can type manually or use Re-capture/Photo
- **Removed `ocrStatus` override from `openPM`** — the modal no longer resets the status text, so the caller's status messages ("Reading label...", error messages) are preserved

---

## [1.1.5] 2026-06-29 — Camera toggle + scan-time OCR capture

### ✅ Changed
- **Removed camera dropdown, replaced with "🔄 Switch Camera" toggle button** — tap to cycle through available cameras. Only shows when 2+ cameras detected. Selection still saved to localStorage
- **OCR now runs at scan time, not modal time** — when a new product barcode is detected, the app immediately captures the current video frame and runs OCR BEFORE opening the modal. This means the label is captured while still in frame, not after the user moves the camera
- **Known products bypass OCR** — if the barcode is already in the database, it adds to cart immediately without any OCR delay. OCR only runs for new products
- **Removed modal auto-capture** — the 600ms delayed auto-capture in the modal is gone since capture now happens at scan time

---

## [1.1.4] 2026-06-29 — Camera selector + simplified config

### ✅ Fixed
- **Removed `videoConstraints` and `aspectRatio`** from scanner config — these were interfering with camera selection on Android Chrome, causing the front camera to open
- **Added camera selector dropdown** — when multiple cameras are detected, a dropdown appears in the Scan screen letting you manually pick which camera to use. Your selection is saved to localStorage and remembered across sessions
- **Simplified camera detection** — uses `getCameras()` to enumerate devices, then auto-selects by label ("back", "rear", "environment") or falls back to the last device in the list. If you previously selected a camera, that saved ID is used directly

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
