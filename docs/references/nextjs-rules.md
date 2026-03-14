# Next.js 16 — Quick Reference Rules

## App Router Conventions

### File System Routing

| File | Purpose |
|---|---|
| `page.tsx` | Route UI (required for route to be accessible) |
| `layout.tsx` | Shared layout wrapping child routes |
| `loading.tsx` | Loading UI (automatic Suspense boundary) |
| `error.tsx` | Error boundary (`'use client'` required) |
| `not-found.tsx` | 404 UI |
| `route.ts` | API route handler (GET, POST, PUT, DELETE) |
| `template.tsx` | Re-rendered layout (no state preservation) |

### Route Groups

Use `(groupName)` folders to organize routes without affecting the URL path:

```
app/
├── (marketing)/page.tsx    → /
├── (auth)/login/page.tsx   → /login
├── (tools)/roi-wizard/     → /roi-wizard
```

---

## Server vs Client Components

### Server Components (default)

- Can `async/await` and fetch data directly
- Can access backend resources (DB, file system)
- Cannot use hooks, event handlers, or browser APIs
- Cannot use `useState`, `useEffect`, `useRef`, etc.

### Client Components (`'use client'`)

- Required for interactivity (clicks, inputs, animations)
- Required for React hooks
- Required for browser APIs (`window`, `document`)
- Keep them as leaf components — push `'use client'` down the tree

---

## Data Fetching Patterns

```tsx
// Server Component — fetch directly
export default async function Page() {
  const data = await fetch('https://api.example.com/data', {
    cache: 'no-store',        // Dynamic data (SSR)
    // cache: 'force-cache',  // Static data (SSG)
    // next: { revalidate: 60 }, // ISR (revalidate every 60s)
  });
  return <Component data={data} />;
}
```

---

## Route Handlers (API Routes)

```typescript
// app/api/example/route.ts
import { NextRequest, NextResponse } from 'next/server';

export async function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams;
  return NextResponse.json({ data: 'hello' });
}

export async function POST(request: NextRequest) {
  const body = await request.json();
  return NextResponse.json({ created: true }, { status: 201 });
}
```

---

## Middleware

```typescript
// frontend/middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  // Auth check, redirects, header modification
  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*', '/roi-wizard/:path*', '/prop-pulse/:path*'],
};
```

---

## Performance Patterns

1. **`next/dynamic`** — lazy-load heavy components (3D viewer, charts, maps):
   ```tsx
   const HeavyComponent = dynamic(() => import('./Heavy'), { ssr: false });
   ```

2. **`next/image`** — always use for images (auto optimization):
   ```tsx
   <Image src={url} alt="Property" width={800} height={600} />
   ```

3. **`next/font`** — self-host fonts (no layout shift):
   ```tsx
   import { Inter } from 'next/font/google';
   const inter = Inter({ subsets: ['latin'] });
   ```

4. **Streaming** — use `loading.tsx` and `<Suspense>` for progressive rendering.

---

## Key Rules

- **App Router only** — never use Pages Router.
- **Server Components by default** — `'use client'` only when necessary.
- **Default exports** for `page.tsx` and `layout.tsx` — named exports for everything else.
- **TypeScript strict mode** — no `any` types.
- **Co-locate** related files — keep components, hooks, and types near the routes that use them.

