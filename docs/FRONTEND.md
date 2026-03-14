# FRONTEND.md — Frontend Development Rules

## Dubai Property 3D World · Next.js 16 + React 19 + TailwindCSS 4

---

## 1. Framework Rules

### Next.js 16 App Router

- **Always use the App Router** (`frontend/app/`). Never use Pages Router.
- **Server Components by default.** Only add `'use client'` directive when the component requires:
  - Event handlers (`onClick`, `onChange`, etc.)
  - Browser APIs (`window`, `document`, `navigator`)
  - React hooks (`useState`, `useEffect`, `useRef`, etc.)
  - Third-party client libraries (Mapbox, Three.js, Recharts)
- **File conventions:**
  - `page.tsx` — Route page (default export required)
  - `layout.tsx` — Shared layout (default export required)
  - `loading.tsx` — Loading UI (Suspense boundary)
  - `error.tsx` — Error boundary (must be `'use client'`)
  - `not-found.tsx` — 404 page
- **Route Groups** — use `(groupName)` folders for logical grouping without affecting URL:
  - `(marketing)` — Landing, pricing, about
  - `(auth)` — Login, signup
  - `(tools)` — ROI Wizard, Prop Pulse
  - `(viewer)` — 3D Viewer
  - `(dashboard)` — User dashboard

### React 19

- Use the `use()` hook for reading promises and context in render.
- Use `useOptimistic` for optimistic UI updates on form submissions.
- Use Server Actions for form mutations when possible (avoid client-side fetch for writes).
- Use `useTransition` for non-urgent state updates to keep UI responsive.

---

## 2. Data Fetching

### Server Components (preferred)

```tsx
// ✅ Fetch data directly in Server Components
export default async function PropertiesPage() {
  const properties = await getProperties(); // Direct Supabase call
  return <PropertyList properties={properties} />;
}
```

### Client Components (when necessary)

```tsx
'use client';
// ✅ Use SWR or React Query for client-side data that needs real-time updates
import useSWR from 'swr';

export function LiveMarketData({ areaId }: { areaId: string }) {
  const { data } = useSWR(`/api/market/${areaId}`, fetcher);
  return <MarketChart data={data} />;
}
```

### Rules

- **Never use `getServerSideProps` or `getStaticProps`** — these are Pages Router patterns.
- Use `fetch()` with `cache: 'force-cache'` for static data, `cache: 'no-store'` for dynamic.
- Use `next: { revalidate: N }` for ISR (Incremental Static Regeneration).
- Co-locate data fetching with the component that consumes it.

---

## 3. Component Patterns

### File Naming

- Components: `PascalCase.tsx` (e.g., `PropertyCard.tsx`, `RoiForm.tsx`)
- Utilities: `camelCase.ts` (e.g., `formatCurrency.ts`, `calculateYield.ts`)
- Hooks: `use*.ts` (e.g., `usePropertySearch.ts`)
- Types: `*.types.ts` (e.g., `property.types.ts`)

### Component Structure

```tsx
// 1. Imports (grouped: react, next, libs, internal)
import { type FC } from 'react';
import Link from 'next/link';
import { formatCurrency } from '@/lib/utils/formatCurrency';
import { type Property } from '@/types/property.types';

// 2. Types (co-located or imported)
interface PropertyCardProps {
  property: Property;
  showRoi?: boolean;
}

// 3. Named export (prefer over default, except page/layout files)
export const PropertyCard: FC<PropertyCardProps> = ({ property, showRoi = false }) => {
  return (
    <article className="rounded-xl border border-gold-200/20 bg-navy-900 p-6">
      {/* ... */}
    </article>
  );
};
```

### Rules

- **Max 300 lines per file.** If longer, extract sub-components or utilities.
- **Named exports** for all components (except `page.tsx` and `layout.tsx` which require default exports).
- **No inline styles.** Use TailwindCSS utility classes exclusively.
- **No CSS modules** unless absolutely unavoidable (e.g., third-party library conflicts).
- **Co-locate tests** with components: `PropertyCard.test.tsx` next to `PropertyCard.tsx`.

---

## 4. Styling — TailwindCSS 4

### Design Tokens (Dubai Luxury Theme)

Use custom theme tokens defined in `tailwind.config.ts`. Reference `docs/DESIGN.md` for the full palette.

```
Navy:   navy-900 (#0A1628), navy-800 (#132240), navy-700 (#1C3358)
Gold:   gold-500 (#C9A84C), gold-400 (#D4B85C), gold-300 (#DFCA7C)
White:  white (#FFFFFF), gray-100 (#F5F5F5), gray-200 (#E5E5E5)
```

### Rules

- Use `className` with Tailwind utilities. Group by concern: layout → spacing → typography → colors → effects.
- Use `cn()` utility (clsx + tailwind-merge) for conditional classes.
- Responsive: mobile-first (`sm:`, `md:`, `lg:`, `xl:`, `2xl:`).
- Dark mode: use `dark:` variant. Default theme is dark (navy background).

---

## 5. 3D Viewer Component Rules

- The 3D Viewer is always a `'use client'` component tree.
- Lazy-load Three.js and Gaussian Splat shaders via `next/dynamic` with `ssr: false`.
- Use a Web Worker for heavy 3D computations (scene parsing, splat sorting).
- Implement `<Suspense>` with a loading skeleton while the 3D scene loads.
- Mobile: detect touch devices and switch to touch-optimized controls.
- Dispose of Three.js resources (`geometry`, `material`, `texture`) on component unmount.

---

## 6. Performance Rules

- Use `next/image` for all images (automatic optimization + Uploadcare CDN).
- Use `next/font` for font loading (self-hosted, no layout shift).
- Lazy-load heavy components: charts, maps, 3D viewer via `next/dynamic`.
- Keep Client Component boundaries as small as possible — push `'use client'` down the tree.
- Avoid importing large libraries in Server Components.

---

## 7. Import Aliases

Configure in `tsconfig.json`:

```json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./frontend/*"],
      "@/components/*": ["./frontend/components/*"],
      "@/lib/*": ["./frontend/lib/*"],
      "@/hooks/*": ["./frontend/hooks/*"],
      "@/types/*": ["./frontend/types/*"]
    }
  }
}
```

