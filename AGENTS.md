# AGENTS.md — AI Coding Agent Instructions

## Project: Dubai Property 3D World Platform

This file provides context and rules for AI coding agents working on this codebase.

---

## Project Overview

Dubai Property 3D World is an immersive real estate intelligence platform for the Dubai market. It combines data-driven ROI analytics with photorealistic 3D spatial experiences (Gaussian Splatting via World Labs Marble API).

**Four-layer architecture:**

1. **Landing Web App** — Brand hub, SEO, authentication, subscription management
2. **Dubai ROI Wizard** — Rental property ROI, yield, and IRR calculator
3. **Dubai Prop Pulse** — Market intelligence and property analytics dashboard
4. **3D World Viewer** — Gaussian Splat-based photorealistic 3D property exploration

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend Framework | Next.js 16 (App Router) + React 19 |
| Styling | TailwindCSS 4 |
| Language | TypeScript (strict mode) |
| Backend API | Node.js + Express |
| Authentication | Supabase Auth |
| Database | Supabase (PostgreSQL) |
| File Uploads | UploadThing |
| CDN / Image Processing | Uploadcare |
| 3D Generation | World Labs Marble API (World API) |
| 3D Rendering | Three.js / WebGL 2.0 (Gaussian Splat shaders) |
| Maps | Mapbox GL JS |
| Charts | Recharts |

---

## Key Documentation Paths

| Document | Path | Purpose |
|---|---|---|
| PRD | `docs/prd.md` | Full product requirements |
| Main Spec | `docs/SPEC.md` | Technical specification |
| Frontend Rules | `docs/FRONTEND.md` | Next.js / React / TailwindCSS conventions |
| Backend Rules | `docs/BACKEND.md` | Node.js / Express / API conventions |
| Database Rules | `docs/DATABASE.md` | Supabase / PostgreSQL / RLS policies |
| Design System | `docs/DESIGN.md` | Dubai luxury design system reference |
| Security | `docs/SECURITY.md` | Security guidelines |
| Architecture | `ARCHITECTURE.md` | System architecture overview |
| DB Schema | `docs/generated/db-schema.md` | Auto-generated database schema |
| References | `docs/references/` | Third-party API and library references |

---

## Agent Rules

### General

1. **Always read the relevant doc** before making changes — start with `SPEC.md`, then the domain-specific file.
2. **Follow TypeScript strict mode** — no `any` types unless absolutely necessary with a comment explaining why.
3. **Use the App Router** — never use the Pages Router in the frontend.
4. **Server Components by default** — only add `'use client'` when interactivity is required.
5. **Never commit secrets** — all API keys go in `.env.local` (frontend) or `.env` (backend).

### Frontend

- Follow `docs/FRONTEND.md` for component patterns, file naming, and data fetching.
- Use TailwindCSS utility classes. No inline styles. No CSS modules unless unavoidable.
- All new pages go in `frontend/app/` using the App Router file conventions.

### Backend

- Follow `docs/BACKEND.md` for route structure, error handling, and middleware patterns.
- All Supabase queries must respect RLS. Never bypass RLS with the service role key in user-facing code.
- Use `docs/DATABASE.md` for any schema changes — always create migrations.

### 3D / World API

- Follow `docs/references/world-api-rules.md` for World Labs API integration patterns.
- All 3D processing is async — use job queues and polling for status.

### Testing

- Write tests for all new backend routes and utility functions.
- Frontend: test complex components and hooks. Simple presentational components don't need tests.

### Code Style

- Use `camelCase` for variables/functions, `PascalCase` for components/types, `SCREAMING_SNAKE_CASE` for constants.
- Maximum file length: 300 lines. If longer, refactor into smaller modules.
- Prefer named exports over default exports (except for Next.js page/layout files).

---

## Core Priorities

- **Performance first.**
- **Reliability first.**
- Keep behavior predictable under load and during failures (session restarts, reconnects, partial streams).
- If a tradeoff is required, choose **correctness and robustness** over short-term convenience.

## Maintainability

- Long-term maintainability is a core priority.
- Before adding new functionality, **check if there is shared logic that can be extracted to a separate module.**
- Duplicate logic across multiple files is a code smell and must be avoided.
- Don't be afraid to change existing code.
- Don't take shortcuts by just adding local logic to solve a problem — refactor properly.

## Development Workflow

- Build features **module by module** — complete one feature fully before moving to the next.
- After completing a feature, **write and run tests** to verify it works correctly.
- Do not move on to the next task until the current feature is tested and confirmed working.
- If tests fail, fix the issues before proceeding — never leave broken code behind.

