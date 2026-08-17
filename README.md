# 🏠 Gurukripa Estate — MERN Real Estate Website

A complete real estate website for normal homes (houses, flats, bungalows, havelis) built on the **MERN stack**, with a **fully separated admin panel** that can change website content **dynamically** — no code changes, no redeploy.

The design follows an **Indian theme** (Rajasthani haveli aesthetics): saffron–marigold–teal palette, mandala & rangoli motifs, flickering diyas, Mughal-arch imagery, Hindi+English typography — plus **motion graphics** and a **scroll-scrubbed video animation**.

---

## ✨ Features

### Public website (`client/`)
- **Home** — cinematic hero with looping video, floating diyas, rotating mandala, city marquee strip, featured properties, parallax about section, count-up stats, **scroll-video section** (a 12-second film of the hero home from morning → golden hour → dusk, scrubbed frame-by-frame as you scroll), feature cards, auto-scrolling testimonials, CTA band
- **Properties** — search, filter (buy/rent, type, city), sort (price, newest, popular), pagination, skeletons, empty states
- **Property detail** — gallery, specs, amenities, key highlights, enquiry form, similar homes
- **About, Contact** — story with parallax + rangoli draw-on-scroll, contact form (goes to admin inbox), office hours, stylised map
- Live content sync — the site polls the backend every 60 s (and instantly on tab focus), so **admin edits appear automatically**

### Admin panel (`admin/`) — fully separated, own app & port
- 🔐 JWT login (separate URL, own React app)
- 📊 **Dashboard** — property count, total views, enquiries, type breakdown, recent listings
- 🏡 **Properties CRUD** — create/edit/delete, image upload (stored on server), reorder photos, set cover, amenities chips, featured toggle, active toggle
- ✏️ **Site Content editor** — schema-driven forms for every block of the site: hero headline & buttons, about story, stats numbers, feature cards, testimonials, city marquee, contact details, footer text, CTA banner, SEO title/description. Change any text/image and it goes live on the website within a minute
- 📥 **Enquiries inbox** — read, mark new/read/contacted, delete
- 🔔 **Real-time notifications** — jaise hi koi visitor enquiry bhejta hai, admin panel pe turant notification aata hai (bell icon + unread badge + browser notification + soft chime, Server-Sent Events se real-time push)
- ⚙️ **Settings** — change password + **Account Ownership Transfer**

### 🔐 Security & Ownership Transfer (sell-ready)
- **Account Ownership Transfer** — the seller (you) enters their current password, the buyer's new admin email and a new strong password; the account flips over and **every session on every device is terminated instantly** (server-side `tokenVersion` invalidation — not just client logout)
- **Forgot Password** — from the login page: secure 256-bit token (stored SHA-256 hashed), **15-minute expiry** (MongoDB TTL auto-delete), emailed reset link, single-use (deleted after use), revokes all sessions
- **CAPTCHA on login** — progressive (appears after failed attempts), server-side math captcha with hashed answers, one-time use
- **OTP system for Forgot Password** — 6-digit OTP emailed (10 min expiry, 5 attempts, hashed storage, single-use) + link fallback
- **2FA (TOTP)** — Google Authenticator / Authy support with QR code, encrypted secret at rest, required at login when enabled
- **Account lockout** — 5 failed attempts → 15-minute lock (IP+email tracked), plus rate limiting on all auth endpoints
- **Password history** — last 3 passwords can't be reused
- **Activity Log (audit trail)** — every login, failure, lockout, password change, transfer, 2FA change, content/property edit logged with IP & user-agent, viewable in the panel
- Strong password policy: ≥ 8 chars + uppercase + number + special char (with live checklist in the UI)
- bcrypt password hashing, JWT 12 h expiry, **rate limiting** on auth endpoints (brute-force protection), account-enumeration-safe responses, Helmet security headers (production), CORS allow-list (production)
- Dynamic `sitemap.xml` + `robots.txt` for SEO
- **Never-goes-down toolkit**: PM2 ecosystem file (auto-restart, memory restart, backoff), graceful shutdown, MongoDB reconnect retry at boot, `/api/health` for uptime monitors
- 📖 Full hosting/domain/SSL/handover instructions: **`DEPLOYMENT.md`**

### Motion graphics & scroll animation
| Effect | Where |
|---|---|
| Scroll-scrubbed video (frame driven by scroll) | Home, "Din se Shaam" section |
| Rotating SVG mandala | Hero, page heroes, footer |
| Self-drawing rangoli (stroke animation on scroll) | About sections |
| Flickering diya lamps with glow | Hero, CTA band, page heroes |
| Infinite marquee (Hindi + English city names) | Under hero |
| Parallax images (scroll-linked translate) | About, story |
| Count-up statistics | Green stats band |
| Scroll-reveal / stagger animations | Everywhere (IntersectionObserver) |
| Scroll progress bar, hover lifts, image zoom, back-to-top | Global |
| `prefers-reduced-motion` support | Global |

---

## 🧱 Tech stack

- **M**ongoDB — Mongoose ODM (properties, content blocks, enquiries, admins)
- **E**xpress — REST API, JWT auth, multer uploads, static serving
- **R**eact — two independent Vite apps (public site + admin)
- **N**ode.js — runtime

No UI frameworks — all styling is hand-written CSS with an Indian design system.

## 📁 Project structure

```
realestate/
├── server/               Express + MongoDB API (:5000)
│   ├── src/
│   │   ├── models/       User, Property, Content, Enquiry
│   │   ├── routes/       auth, properties, content, enquiries, uploads
│   │   ├── middleware/   JWT auth, multer upload
│   │   ├── index.js      app entry + /api/site + /api/stats
│   │   └── seed.js       seeds admin, 9 properties, 10 content blocks
│   └── uploads/          images & the scroll video (served at /uploads)
├── client/               Public website (Vite, :5173)
│   └── src/              pages, components (motion graphics), styles.css
├── admin/                Admin panel (Vite, :5174, served at /admin in prod)
│   └── src/              login, dashboard, CRUD, content editor, inbox
└── scripts/              setup-mongodb.sh (one-command DB install + seed)
```

## 🚀 Quick start

### 🪟 Windows — MongoDB is bundled, nothing to install!

The zip includes a **portable MongoDB** (`.runtime\mongodb-win\mongod.exe`) — no
MongoDB installation, no Docker, no WSL needed. The batch files start it for you.

```bat
:: 1. Double-click:  setup.bat
::    (installs npm dependencies + seeds the database)

:: 2. Double-click:  start.bat
::    (starts bundled MongoDB -> seeds if needed -> runs website + admin + API)
```

That's it — open http://localhost:5173 (website) and http://localhost:5174
(admin — fresh setup par .env ka ADMIN_EMAIL / admin123; ownership transfer ke baad naya email).

> **Notes**
> - If you already have your own MongoDB running on port 27017, the bundled one
>   is skipped automatically.
> - If you see *"vcruntime140.dll is missing"*, run
>   `.runtime\mongodb-win\bin\vc_redist.x64.exe` once, then `start.bat` again.
> - The API server now *retries* MongoDB for ~3 minutes instead of crashing, so
>   even plain `npm run dev` won't die if MongoDB starts a few seconds late.
> - Prefer PowerShell 7 (`winget install Microsoft.PowerShell`) and `&&` works there too.

Manual equivalent (cmd.exe or PowerShell 7+; in PowerShell 5.1 run each line separately):

```cmd
npm install
npm --prefix server install
npm --prefix client install
npm --prefix admin install
scripts\start-mongodb.bat
npm --prefix server run seed
npm run dev
```

### 🐧 macOS / Linux

```bash
# 1. Install dependencies (root, server, client, admin)
npm install
npm --prefix server install
npm --prefix client install
npm --prefix admin install

# 2. Start MongoDB (auto-downloads a local mongod, no root needed) + seed data
npm run setup          # or use your own MongoDB and set MONGO_URI in server/.env

# 3. Run everything (API + website + admin)
npm run dev
```

| Service | URL |
|---|---|
| 🌐 Website | http://localhost:5173 |
| 🔐 Admin panel | http://localhost:5174 |
| ⚙️ API | http://localhost:5000/api/health |

**Admin login:** fresh setup par `.env` ka `ADMIN_EMAIL` / `admin123` — ownership transfer ke baad naya email, purana poora delete ho jata hai

### Production mode
```bash
npm run build   # builds client + admin into static bundles
npm start       # Express serves website at / and admin panel at /admin
```
→ Website: http://localhost:5000 · Admin: http://localhost:5000/admin

## 🔄 How dynamic content works

1. Every editable block of the site lives in the `contents` collection (key → JSON).
2. The **admin → Site Content** page renders a form from `admin/src/contentSchema.js` and saves via `PUT /api/content/:key`.
3. The public site fetches everything in one call (`GET /api/site`) and re-polls every 60 s + on tab focus — so edits appear live without refreshing or redeploying.
4. Properties are fully CRUD-able, including image upload (`POST /api/upload`, stored in `server/uploads/`, served at `/uploads/...`).

## 🔌 API overview

| Method | Endpoint | Auth | Purpose |
|---|---|---|---|
| POST | `/api/auth/login` | – | Admin login (JWT) |
| GET | `/api/auth/me` | ✓ | Current admin |
| PUT | `/api/auth/password` | ✓ | Change password |
| GET | `/api/site` | – | All content + counts for the website |
| GET | `/api/properties` | – | List (q, type, status, city, price, featured, sort, page) |
| GET | `/api/properties/meta` | – | Filter options |
| GET | `/api/properties/:slug` | – | Detail + similar + view counter |
| GET | `/api/properties/id/:id` | ✓ | Fetch by id (admin) |
| POST/PUT/DELETE | `/api/properties...` | ✓ | Admin CRUD |
| PATCH | `/api/properties/:id/feature` | ✓ | Toggle featured |
| GET/PUT | `/api/content/:key` | ✓(PUT) | Read / update content blocks |
| POST | `/api/enquiries` | – | Contact form |
| GET/PATCH/DELETE | `/api/enquiries...` | ✓ | Inbox management |
| POST/DELETE | `/api/upload` | ✓ | Image upload / delete |
| GET | `/api/stats` | ✓ | Dashboard stats |

## 🛠 Troubleshooting

- **MongoDB connection fails** → run `npm run setup` (downloads mongod locally) or start your own MongoDB and set `MONGO_URI` in `server/.env`.
- **Images don't load in dev** → the Vite apps proxy `/uploads` → `:5000`; keep the API running.
- **Ports busy** → change `PORT` in `server/.env` and the ports in `client/vite.config.js` / `admin/vite.config.js`.
- **Reset data** → `npm --prefix server run seed` (idempotent — won't wipe admin edits; delete the `gurukripa_estate` DB to fully reset).

---

Made with ❤️ and a lot of chai in Jaipur. 🪔
