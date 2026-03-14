# Supabase — Integration Rules

## Overview

Supabase provides authentication, PostgreSQL database with RLS, and real-time subscriptions for the platform.

---

## Client Setup

### Frontend (Server Components)

```typescript
// frontend/lib/supabase/server.ts
import { createServerClient } from '@supabase/ssr';
import { cookies } from 'next/headers';

export const createSupabaseServer = async () => {
  const cookieStore = await cookies();

  return createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll: () => cookieStore.getAll(),
        setAll: (cookiesToSet) => {
          cookiesToSet.forEach(({ name, value, options }) => {
            cookieStore.set(name, value, options);
          });
        },
      },
    }
  );
};
```

### Frontend (Client Components)

```typescript
// frontend/lib/supabase/client.ts
import { createBrowserClient } from '@supabase/ssr';

export const createSupabaseBrowser = () =>
  createBrowserClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
  );
```

### Backend (Admin)

```typescript
// backend/src/config/supabase.ts
import { createClient } from '@supabase/supabase-js';

// Service role — bypasses RLS. Use ONLY for admin operations.
export const supabaseAdmin = createClient(
  process.env.SUPABASE_URL!,
  process.env.SUPABASE_SERVICE_ROLE_KEY!
);

// Per-request client — respects RLS
export const createUserClient = (jwt: string) =>
  createClient(process.env.SUPABASE_URL!, process.env.SUPABASE_ANON_KEY!, {
    global: { headers: { Authorization: `Bearer ${jwt}` } },
  });
```

---

## Auth Rules

1. Use `@supabase/ssr` for Next.js integration — not `@supabase/auth-helpers-nextjs` (deprecated).
2. Store session in **HTTP-only cookies** — never in `localStorage`.
3. Implement auth middleware in Next.js `middleware.ts` for route protection.
4. Use `supabase.auth.getUser()` (server-side validated) over `getSession()` (client-side only).
5. Handle token refresh automatically via the SSR package.
6. Enable email confirmation for new signups in production.

## Database Query Rules

1. **Always use RLS** — see `docs/DATABASE.md` for policies.
2. Use the **anon key client** with user JWT for user-facing queries.
3. Use the **service role client** only for: webhooks, cron jobs, admin scripts.
4. Use `.select()` to fetch only needed columns — never `SELECT *` in production.
5. Use `.eq()`, `.in()`, `.range()` for filtering — never raw SQL in client queries.
6. Handle errors from every Supabase call: `const { data, error } = await ...`
7. Use database functions for complex operations (aggregations, multi-table updates).

## Real-Time Rules

1. Use Supabase real-time only where necessary (e.g., tour processing status updates).
2. Subscribe to specific rows/tables — never subscribe to entire tables.
3. Unsubscribe on component unmount to prevent memory leaks.
4. Implement reconnection logic for dropped connections.

```typescript
// Example: Listen for tour status changes
'use client';

import { useEffect } from 'react';
import { createSupabaseBrowser } from '@/lib/supabase/client';

export function useTourStatus(tourId: string) {
  useEffect(() => {
    const supabase = createSupabaseBrowser();
    const channel = supabase
      .channel(`tour-${tourId}`)
      .on('postgres_changes', {
        event: 'UPDATE',
        schema: 'public',
        table: 'tours',
        filter: `id=eq.${tourId}`,
      }, (payload) => {
        // Handle status update
      })
      .subscribe();

    return () => { supabase.removeChannel(channel); };
  }, [tourId]);
}
```

