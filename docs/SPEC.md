# SPEC.md — Technical Specification

## Dubai Property 3D World Platform

**Version:** 1.0 · **Date:** March 2026 · **Status:** Active

---

## 1. System Architecture

Four interconnected layers sharing a unified session/state system:

| Layer | Component | Primary Tech |
|---|---|---|
| 1 | Landing Web App | Next.js 16 App Router, TailwindCSS 4 |
| 2 | Dubai ROI Wizard | React 19, Recharts, Server Actions |
| 3 | Dubai Prop Pulse | React 19, Mapbox GL JS, Recharts |
| 4 | 3D World Viewer | Three.js, WebGL 2.0, Gaussian Splat shaders |

**Backend:** Node.js + Express REST API
**Database:** Supabase (PostgreSQL with RLS)
**Auth:** Supabase Auth (JWT-based)
**File Uploads:** UploadThing (property photos)
**CDN:** Uploadcare (image transformations, delivery)
**3D Engine:** World Labs Marble API (Gaussian Splat generation)

---

## 2. Frontend Specification

### 2.1 Framework & Rendering

- **Next.js 16** with App Router — all routing via `frontend/app/` directory
- **React 19** — leverage Server Components by default, `'use client'` only for interactive components
- **TypeScript strict mode** — no implicit `any`, strict null checks enabled
- **TailwindCSS 4** — utility-first styling, custom Dubai luxury design tokens

### 2.2 Route Structure

```
frontend/app/
├── (marketing)/          # Landing pages — static/ISR
│   ├── page.tsx          # Hero, value prop, CTAs
│   ├── pricing/page.tsx  # Plans & pricing
│   └── about/page.tsx    # About page
├── (auth)/               # Auth flows
│   ├── login/page.tsx
│   ├── signup/page.tsx
│   └── forgot/page.tsx
├── (tools)/              # Authenticated tool pages
│   ├── roi-wizard/page.tsx
│   └── prop-pulse/page.tsx
├── (viewer)/             # 3D Viewer
│   └── tour/[id]/page.tsx
├── (dashboard)/          # User/broker dashboard
│   ├── properties/page.tsx
│   ├── tours/page.tsx
│   └── settings/page.tsx
├── api/                  # Route handlers
│   ├── uploadthing/route.ts
│   └── webhooks/route.ts
├── layout.tsx            # Root layout
└── page.tsx              # Home redirect
```

### 2.3 State Management

- **Server state:** React Server Components fetch data directly from Supabase
- **Client state:** React 19 `use()` hook + `useOptimistic` for forms
- **URL state:** Search params for filters, pagination (Prop Pulse)
- **Cross-tool context:** Shared cookie-based session carries selected property ID, ROI results between tools and 3D Viewer

### 2.4 3D Viewer Technical Requirements

- **Renderer:** Three.js with custom Gaussian Splat shader (WebGL 2.0)
- **Scene format:** Compressed `.splat` files served via Uploadcare CDN
- **Load time target:** < 8 seconds on standard broadband
- **Navigation:** Click-to-move, orbit, zoom, first-person walk mode
- **Mobile:** Touch controls — swipe orbit, pinch zoom, tap move
- **Annotations:** HTML overlay elements anchored to X,Y,Z coordinates in 3D space
- **Browser support:** Chrome, Safari, Firefox, Edge (latest 2 versions)

---

## 3. Backend Specification

### 3.1 API Architecture

- **Framework:** Express.js on Node.js
- **API style:** RESTful with JSON request/response
- **Auth middleware:** Validates Supabase JWT on every protected route
- **Error format:** `{ error: string, code: string, details?: object }`

### 3.2 Route Groups

| Group | Base Path | Purpose |
|---|---|---|
| Auth | `/api/auth/*` | Login, signup, session refresh, profile |
| Properties | `/api/properties/*` | CRUD for property records |
| ROI | `/api/roi/*` | ROI calculations, benchmarks |
| Market | `/api/market/*` | Prop Pulse data, area stats, trends |
| Tours | `/api/tours/*` | 3D tour management, annotations |
| Jobs | `/api/jobs/*` | 3D generation job status, video export |
| Uploads | `/api/uploads/*` | File upload coordination |
| AI | `/api/ai/*` | Gemini-powered content generation (descriptions, ROI narratives) |
| Developers | `/api/developers/*` | Developer intelligence — Crustdata enrichment + Gemini report generation |

### 3.3 3D Processing Pipeline

1. User uploads 10–50 property photos via UploadThing
2. Backend creates a job record in Supabase (`status: 'queued'`)
3. Backend sends photo URLs to World Labs Marble API
4. Marble API processes asynchronously (target: < 5 minutes)
5. Backend polls Marble API for job completion
6. On completion: stores 3D scene URL, updates job status to `'complete'`
7. Frontend loads the `.splat` scene in the Three.js viewer

### 3.4 Video Export Pipeline

1. User clicks "Generate Tour" in 3D Viewer
2. Backend receives camera path keyframes + scene ID
3. Server-side headless browser renders the 3D scene along the camera path
4. FFmpeg encodes frames to H.264 MP4 (1080p, < 25MB for WhatsApp)
5. Video stored via UploadThing, URL returned to user

---

## 4. Database Specification

See `docs/DATABASE.md` for full schema and RLS policies.

### 4.1 Core Tables

| Table | Purpose |
|---|---|
| `profiles` | User profiles (extends Supabase `auth.users`) |
| `properties` | Property metadata, address, price, bedroom count |
| `roi_calculations` | Saved ROI wizard results per property |
| `market_data` | Area statistics, historical price data |
| `tours` | 3D tour records (scene URL, status, owner) |
| `annotations` | Spatial annotations (X,Y,Z, type, text, tour_id) |
| `tour_videos` | Exported video records |
| `subscriptions` | User subscription tier and billing |

### 4.2 RLS Policy Summary

- All tables have RLS enabled
- Users can only read/write their own data
- Shared tours are publicly readable (view-only)
- Broker accounts can manage properties for their agency
- Service role key used **only** in backend for admin operations

---

## 5. Third-Party Integrations

| Service | Purpose | Docs Reference |
|---|---|---|
| Supabase | Auth, DB, RLS, Storage | `docs/references/supabase-rules.md` |
| UploadThing | File upload handling | `docs/references/uploadthing-rules.md` |
| Uploadcare | CDN, image transforms | `docs/references/uploadcare-rules.md` |
| World Labs Marble API | 3D scene generation | `docs/references/world-api-rules.md` |
| Google Gemini AI | AI content generation (descriptions, ROI narratives, developer reports) | `docs/references/gemini-ai-rules.md` |
| Crustdata | B2B data enrichment for developer intelligence (headcount, funding, hiring) | `docs/references/crustdata-rules.md` |
| Mapbox GL JS | Maps, geocoding, heatmaps | Standard Mapbox docs |
| Recharts | Charts for ROI and market data | Standard Recharts docs |

---

## 6. Environment Variables

```env
# Frontend (.env.local)
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
NEXT_PUBLIC_MAPBOX_TOKEN=
NEXT_PUBLIC_UPLOADCARE_PUBLIC_KEY=
UPLOADTHING_TOKEN=

# Backend (.env)
SUPABASE_URL=
SUPABASE_SERVICE_ROLE_KEY=
SUPABASE_ANON_KEY=
WORLD_LABS_API_KEY=
GEMINI_API_KEY=
CRUSTDATA_API_KEY=
UPLOADTHING_TOKEN=
UPLOADCARE_PUBLIC_KEY=
UPLOADCARE_SECRET_KEY=
PORT=3001
```

