# SECURITY.md — Security Guidelines

## Dubai Property 3D World Platform

---

## 1. Authentication & Authorization

### Supabase Auth

- Use **Supabase Auth** for all user authentication (email/password, OAuth providers).
- JWTs are issued by Supabase and validated on every protected API request.
- **Never store JWTs in `localStorage`** — use HTTP-only secure cookies.
- Set short JWT expiry (1 hour) with refresh token rotation enabled.
- Implement PKCE flow for OAuth providers.

### Authorization Layers

| Layer | Mechanism |
|---|---|
| Database | Row Level Security (RLS) policies on every table |
| Backend API | JWT validation middleware on protected routes |
| Frontend | Route-level auth checks in `layout.tsx` + middleware |

### Rules

- **Never bypass RLS** with the service role key in user-facing code.
- **Service role key** is restricted to: webhooks, cron jobs, admin scripts, migrations.
- Check user roles (`user`, `broker`, `admin`) for feature-gated endpoints.
- Rate-limit auth endpoints (login, signup, password reset) to prevent brute force.

---

## 2. API Security

### Request Validation

- Validate **all** request bodies, query params, and path params with Zod schemas.
- Reject requests with unexpected fields (`z.object().strict()`).
- Sanitize all user input before database insertion.
- Limit request body size (`express.json({ limit: '10mb' })`).

### Rate Limiting

- Apply rate limiting per IP and per user:
  - Auth endpoints: 5 requests/minute
  - API endpoints: 100 requests/minute
  - File uploads: 10 requests/minute
  - 3D generation: 5 requests/hour (per user tier)

### CORS

- Restrict `Access-Control-Allow-Origin` to the frontend domain only.
- Never use `*` for CORS origin in production.
- Enable `credentials: true` for cookie-based auth.

### Headers

- Use **Helmet.js** for security headers:
  - `Content-Security-Policy` — restrict script sources
  - `X-Content-Type-Options: nosniff`
  - `X-Frame-Options: DENY`
  - `Strict-Transport-Security` — HSTS enabled
  - `Referrer-Policy: strict-origin-when-cross-origin`

---

## 3. Data Protection

### Secrets Management

- **Never commit secrets** to version control.
- All API keys and credentials go in `.env.local` (frontend) or `.env` (backend).
- Add `.env*` to `.gitignore` (except `.env.example`).
- Rotate API keys periodically — World Labs, UploadThing, Uploadcare, Supabase.
- Use environment-specific keys (dev, staging, production).

### Database Security

- All tables must have RLS enabled — see `docs/DATABASE.md`.
- Never expose database connection strings to the frontend.
- Use parameterized queries — never concatenate user input into SQL.
- Encrypt sensitive fields at rest (phone numbers, financial data).
- Regular backups via Supabase's built-in backup system.

### File Upload Security

- Validate file types server-side (not just by extension — check MIME type / magic bytes).
- Enforce file size limits per upload route.
- Scan uploaded files for malware before processing (for 3D generation pipeline).
- Store uploaded files in private buckets — serve via signed URLs with expiry.
- Never allow users to control the file path or filename on the server.

---

## 4. Frontend Security

- **No sensitive data in client-side code** — API keys that appear in `NEXT_PUBLIC_*` must be safe for public exposure (e.g., Supabase anon key, Mapbox public token).
- **XSS prevention:** React handles escaping by default. Never use `dangerouslySetInnerHTML` unless absolutely necessary with sanitized content.
- **CSRF protection:** Use `SameSite=Strict` or `SameSite=Lax` cookies.
- **Content Security Policy:** Restrict script sources to same-origin + trusted CDNs.
- **Subresource Integrity (SRI):** Use SRI hashes for external script/stylesheet tags.

---

## 5. 3D Viewer Security

- Validate `.splat` files before loading — check file header and size limits.
- Sandbox the 3D viewer — limit WebGL resource usage to prevent GPU-based DoS.
- Signed URLs for 3D scene files — prevent unauthorized access to premium content.
- Rate-limit 3D scene generation to prevent abuse of World Labs API credits.

---

## 6. Logging & Monitoring

- Log all authentication events (login, logout, failed attempts, token refresh).
- Log API errors with request metadata (method, path, user ID, status code).
- **Never log:** passwords, JWT tokens, full credit card numbers, or PII in plaintext.
- Use structured logging (JSON format) for easy parsing by log aggregation tools.
- Set up alerts for: repeated auth failures, rate limit breaches, 5xx error spikes.

---

## 7. Dependency Security

- Run `npm audit` regularly to check for known vulnerabilities.
- Pin major dependency versions — avoid `^` for critical security packages.
- Review `package-lock.json` changes in PRs for unexpected dependency additions.
- Keep Supabase client libraries up to date for security patches.

