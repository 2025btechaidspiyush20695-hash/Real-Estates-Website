<<<<<<< HEAD
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
=======
[README.md](https://github.com/user-attachments/files/31147822/README.md)
🏠 Gurukripa Estate — Real Estate Website + Admin Panel (MERN)

Gurukripa Estate ke liye complete real-estate system — **public website** + **alag admin panel** (CRM + Property Management + Website CMS). Single owner ke liye bana hai — **saara content admin panel se edit hota hai, koi code change nahi chahiye**.

---

## 📦 Kya-kya hai (ek nazar me)

| App | Port | Kya hai |
|---|---|---|
| 🌐 **Website** | `http://localhost:5173` | Properties, About, Contact, Find-a-Home — visitors ke liye |
| ⚙️ **Admin Panel** | `http://localhost:5174` | Dashboard, Properties, Leads/CRM, Website CMS, Analytics, Settings |
| 🔌 **API Server** | `http://localhost:5000` | Backend — MongoDB Atlas se connected |
| 🗄️ **Database** | MongoDB Atlas (cloud) | `gurukripa_estate` — properties, enquiries, content, users |

**Login:** Fresh install par `.env` ka `ADMIN_EMAIL` / `admin123` — ownership transfer ke baad naya email/password.

---

## ✨ Features (current)

### 🌐 Website
- **Home** — Hero (brand + trust), stats count-up, selected properties, services, story + founder, areas marquee, why-us, testimonials slider (mobile par 1 card), CTA band, footer
- **Properties** — search, filter (buy/rent, type, city), sort (price/newest/popular), pagination, skeleton loading
- **Property detail** — gallery, specs, amenities, enquiry form, similar homes
- **Find a Home** — requirement form (type/city/budget/bedrooms/timeline) → admin ko lead
- **About / Contact / Privacy / Terms / 404** — sab admin se editable
- **WhatsApp float button** — direct chat (number admin se editable)
- **Live sync** — website har 60s (aur tab focus par) content refresh karti hai → admin edits automatically live

### ⚙️ Admin Panel
- 📊 **Dashboard** — KPI cards, **2 dynamic line charts** (Property Performance + Daily Website Visits), recent enquiries, follow-ups, upcoming visits, notifications
- 🏡 **Properties** — CRUD, 20 photos (cover/order), custom amenities, featured/active toggles, listing status
- 📥 **Leads & Enquiries** — pipeline (new → contacted → qualified → site_visit → negotiation → closed), follow-up dates, notes, call buttons
- 📅 **Site Visits** — schedule, status, feedback
- ✏️ **Website CMS** — 18 content blocks editable (hero, about, stats, services, testimonials, contact, footer, SEO...). **Property types (Duplex/Villa...) bhi yahan se editable** — jo types yahan hain wahi Add Property form me dikhenge
- 📈 **Analytics** — funnel (visitors → views → enquiries → visits → negotiations → closed), most-viewed properties
- 🔔 **Notifications** — real-time (SSE): jaise hi enquiry aaye, panel me turant dikhe + email bhi
- 📋 **Activity Log** — har sensitive action logged (login, password, transfer, edits) with IP
- ⚙️ **Settings** — password change, 2FA (Google Authenticator), **Account Ownership Transfer**, **Email/SMTP card** (UI se sender Gmail + app password)

### 📧 Email System (fully DB-driven — kuch hardcode nahi)
- **Recipient** (kis ko enquiry email jayegi) = **current admin user ka email** — DB se automatically. Ownership transfer karo → mail auto naye owner ko
- **Sender** (kis Gmail SE jayegi) = **Admin Panel → Settings → Email/SMTP card** se change hota hai — DB me save, koi file edit nahi
- **Send Test Email** button — 1 click me verify, exact error screen par
- Gmail app password (16-char) required — Google Account → Security → 2-Step ON → App passwords

### 🔐 Security
- JWT auth + bcrypt + rate limiting + CAPTCHA (failed attempts par) + 2FA TOTP + account lockout + password history (last 3)
- **Ownership Transfer** — purana email **puri tarah saaf** hota hai: duplicate users, audit logs, OTPs, reset tokens sab delete; sab sessions terminate; email-recipient cache reset
- Activity log (audit trail) with IP + user-agent
- Helmet headers, CORS allow-list, graceful shutdown, `/api/health`

### 📊 Daily Website Visits
- **1 device = 1 visit per day** (unique device ID in localStorage, server par dedupe)
- Day boundary **IST** me (raat 12 baje reset)
- Dashboard me 14-din ka line chart + today/total/unique

---

## 🛠️ Tech Stack

- **MongoDB Atlas** (cloud DB — direct connection URI, SRV nahi kyunki kuch ISPs par SRV fail hota hai)
- **Express** (Node.js API) — JWT auth, SSE notifications, Nodemailer
- **React + Vite** (website `client/`, admin `admin/` — alag apps)
- Self-hosted fonts, no external CDN dependencies

---

## 📁 Folder Structure

```
realestate/
├── client/          # Public website (React + Vite, port 5173)
├── admin/           # Admin panel (React + Vite, port 5174)
├── server/          # API (Express, port 5000)
│   ├── src/
│   │   ├── models/      # User, Property, Content, Enquiry, Notification, SiteVisit, Visit, Setting, ...
│   │   ├── routes/      # auth, properties, content, enquiries, sitevisits, settings, uploads, audit
│   │   ├── services/    # mailer (DB-driven SMTP), notifier (SSE), otp, totp
│   │   └── seed.js      # sample data (admin, 9 properties, 18 content blocks)
│   └── .env             # config (MONGO_URI, JWT_SECRET, SMTP fallback...)
├── setup.bat        # Windows one-time setup (deps + MongoDB + seed)
├── start.bat        # Windows daily start (MongoDB + seed + all 3 servers)
├── START-HERE.txt   # Quick start guide (Hindi)
└── DEPLOYMENT.md    # VPS/domain/SSL deployment guide
```

---

## 🚀 Setup (Windows — easy tarika)

> **Zaroorat:** Node.js (v18+) aur internet (Atlas ke liye). MongoDB **zip ke andar bundled** hai — kuch install nahi karna.

### Pehli baar (ek baar):
1. Zip extract karo (koi folder, e.g. `D:\gurukripa-site`)
2. Folder me `setup.bat` par **double-click**
   - VC++ runtime (agar prompt aaye to **Yes**)
   - npm dependencies install (root + server + client + admin)
   - Bundled MongoDB start + data seed
3. **"Done!"** dikhe to complete ✅

### Har baar (roz):
1. `start.bat` par **double-click** → teeno servers chal jayenge
2. Website: `http://localhost:5173` · Admin: `http://localhost:5174`
3. Band karne ke liye: start.bat wali window me `Ctrl+C`

### Manual (PowerShell/cmd, line by line):
```powershell
cd D:\gurukripa-site\realestate
npm install
npm --prefix server install
npm --prefix client install
npm --prefix admin install
npm run dev
```

> ⚠️ Note: PowerShell 5.1 me `&&` use mat karo — commands line-by-line chalao.

---

## 📧 Email Setup (enquiry emails — 5 minute)

1. **Google App Password banao:** Google Account → Security → **2-Step Verification ON** → **App passwords** → app: Mail → Generate → 16-char code (e.g. `abcd efgh ijkl mnop`)
2. **Admin Panel → Settings → "Email / SMTP (Sender)"** card:
   - Sender Gmail: apna Gmail (jis account ka app password hai)
   - App password: 16-char code (spaces hata ke)
   - **💾 Save SMTP** → **📧 Send Test Email**
3. ✅ Success = email aayi (inbox/spam check). Ab har enquiry par **"📩 You have a new enquiry"** email jayegi current admin ke email par.

> ⚡ **Recipient change karna ho?** Admin Panel → Settings → **Account Ownership Transfer** → naya email. Mail automatically naye owner ko jayegi — purana email puri tarah saaf (users, logs, OTPs sab delete).

---

## 🔄 Ownership Transfer (website bechne par)

**Admin Panel → Settings → Account Ownership Transfer:**
- Current password + buyer ka naya email + naya strong password
- Kya hota hai:
  - Admin email + password change
  - **Sab sessions terminate** (server-side tokenVersion — har device logout)
  - 2FA reset (naya owner apna 2FA set karega)
  - **Purana email ka nishan mit jata hai** — duplicate users, audit logs, OTPs, reset tokens
  - Enquiry emails ab naye owner ke email par
- Naya owner login: naye email + naye password se

---

## 🧹 Maintenance

- **Data storage full?** Cleanup script:
  ```
  npm run cleanup            # dry-run (dikhata hai)
  npm run cleanup -- --yes   # asli cleanup (purane notifications/audits/OTPs)
  ```
- **Backup:** Atlas UI (Data Explorer → Export) ya `mongodump` — saare collections ki periodic backup karo
- **Logs:** start.bat wali window me — errors yahin dikhte hain

---

## 🐛 Troubleshooting

| Problem | Fix |
|---|---|
| **ECONNREFUSED startup par** | Normal hai — API 5-8s me ready hota hai. Website ab khud retry karti hai (har 2s, 6 baar). Bas F5 karo ya 10s wait karo. |
| **Website content 60s tak nahi aata** | `client/src/SiteContext.jsx` me `setSite(data);` line honi chahiye (tryLoad me). Zip fresh extract karo. |
| **Settings page crash — "useEffect is not defined"** | `admin/src/pages/Settings.jsx` line 1: `import React, { useEffect, useState } from 'react';` |
| **Email nahi aa rahi** | Admin → Settings → Send Test Email. Error padho: EAUTH = galat app password / galat account; ESOCKET = internet/firewall. Spam folder check karo. |
| **White screen** | Incognito kholo. Ad-blocker ho to production mode (`start-prod.bat`). Console me `GURUKRIPA v2.3` dikhna chahiye. |
| **Mongo connection fail** | Internet check. `.env` me **direct URI** (mongodb://...ac-5goiirv...) hona chahiye — SRV kuch ISPs par fail hota hai. |
| **Admin login fail** | Ownership transfer ke baad purana email kaam nahi karta (by design). Naye email + naye password se login karo. |

---

## 🚀 Deployment (production)

`DEPLOYMENT.md` padho — VPS setup, PM2, domain, SSL, reverse proxy, Atlas whitelist. Short version:
1. Server par code + `.env` (production values)
2. `npm run build` (client + admin dist)
3. `npm start` (API serves website at `/`, admin at `/admin`)
4. PM2 (`ecosystem.config.js`) + Nginx + SSL

---

## 📝 License / Owner

Single-owner business tool — Gurukripa Estate ke liye. Saara data owner ka hai (Atlas cloud me). Handover = Ownership Transfer (upar dekho).
es, listings, inquiries, users, and website content from one centralized dashboard.
>>>>>>> 13a12882393ff640d7320370509705089bae4b40
