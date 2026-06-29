# GroceryScan 🛒

A mobile-first grocery barcode scanner and expense tracker.  
Single HTML file — no install, no build step, no backend.

---

## How to test WITHOUT uploading to Netlify

The camera requires **HTTPS**. Here are free ways to get HTTPS locally:

### Option A — VS Code Live Server + ngrok (Recommended)
1. Install [VS Code](https://code.visualstudio.com/)
2. Install the **Live Server** extension
3. Right-click `index.html` → **Open with Live Server** (runs on `http://localhost:5500`)
4. Install [ngrok](https://ngrok.com/) (free account)
5. Run: `ngrok http 5500`
6. ngrok gives you a public HTTPS URL like `https://abc123.ngrok.io`
7. Open that URL on your phone — camera will work ✅

### Option B — Python HTTPS server (quick)
```bash
# Generate a self-signed cert (one time)
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes -subj "/CN=localhost"

# Run HTTPS server
python3 -c "
import ssl, http.server
ctx = ssl.SSLContext(ssl.PROTOCOL_TLS_SERVER)
ctx.load_cert_chain('cert.pem','key.pem')
h = http.server.HTTPServer(('0.0.0.0',8443),http.server.SimpleHTTPRequestHandler)
h.socket = ctx.wrap_socket(h.socket,server_side=True)
print('Serving on https://localhost:8443')
h.serve_forever()
"
```
Then open `https://YOUR_LOCAL_IP:8443` on your phone (accept the security warning).

### Option C — GitHub Pages (free, auto-updates from git)
1. Push this repo to GitHub
2. Go to repo Settings → Pages → Source: main branch
3. Your app lives at `https://yourusername.github.io/groceryscan`
4. Every `git push` auto-deploys — **no Netlify needed**

### Option D — Netlify (current setup)
Drop `index.html` into the Netlify Deploys tab.  
Free tier gives 100GB bandwidth/month and 300 build minutes — plenty for personal use.

---

## Testing on Android emulator (Android Studio)

1. Install [Android Studio](https://developer.android.com/studio)
2. Create an emulator (Pixel 6, API 33+)
3. Start emulator, open Chrome inside it
4. Go to `http://10.0.2.2:5500` (points to your PC's localhost)
5. Camera in emulator is simulated — use manual barcode entry for real testing

---

## Project structure

```
groceryscan/
├── index.html      ← The entire app (HTML + CSS + JS in one file)
├── CHANGELOG.md    ← Version history
└── README.md       ← This file
```

---

## Git workflow

```bash
# First time setup
git init
git add .
git commit -m "v1.0.0 — initial stable build"

# After each change
git add .
git commit -m "v1.0.1 — describe what changed"

# Push to GitHub (replace URL with yours)
git remote add origin https://github.com/yourname/groceryscan.git
git push -u origin main
```
