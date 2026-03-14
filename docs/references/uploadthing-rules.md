# UploadThing — Integration Rules

## Overview

UploadThing handles file uploads for the platform — primarily property photos for 3D generation, profile avatars, and floor plan documents.

- **Docs:** https://docs.uploadthing.com
- **Framework:** `uploadthing/next` (App Router integration)
- **Client components:** `@uploadthing/react`

---

## Setup Pattern

### 1. File Router Definition

```typescript
// frontend/app/api/uploadthing/core.ts
import { createUploadthing, type FileRouter } from 'uploadthing/next';
import { UploadThingError } from 'uploadthing/server';

const f = createUploadthing();

export const uploadRouter = {
  // Property photos for 3D generation
  propertyPhotos: f({
    image: { maxFileSize: '20MB', maxFileCount: 50 },
  })
    .middleware(async ({ req }) => {
      const user = await getAuthUser(req);
      if (!user) throw new UploadThingError('Unauthorized');
      return { userId: user.id };
    })
    .onUploadComplete(async ({ metadata, file }) => {
      // Store file URL in database
      return { uploadedBy: metadata.userId, url: file.ufsUrl };
    }),

  // Profile avatar
  avatar: f({
    image: { maxFileSize: '4MB', maxFileCount: 1 },
  })
    .middleware(async ({ req }) => {
      const user = await getAuthUser(req);
      if (!user) throw new UploadThingError('Unauthorized');
      return { userId: user.id };
    })
    .onUploadComplete(async ({ metadata, file }) => {
      return { url: file.ufsUrl };
    }),

  // Floor plan documents
  floorPlan: f({
    image: { maxFileSize: '10MB', maxFileCount: 1 },
    pdf: { maxFileSize: '10MB', maxFileCount: 1 },
  })
    .middleware(async ({ req }) => {
      const user = await getAuthUser(req);
      if (!user) throw new UploadThingError('Unauthorized');
      return { userId: user.id };
    })
    .onUploadComplete(async ({ metadata, file }) => {
      return { url: file.ufsUrl };
    }),
} satisfies FileRouter;

export type OurFileRouter = typeof uploadRouter;
```

### 2. Route Handler

```typescript
// frontend/app/api/uploadthing/route.ts
import { createRouteHandler } from 'uploadthing/next';
import { uploadRouter } from './core';

export const { GET, POST } = createRouteHandler({ router: uploadRouter });
```

### 3. Client Utilities

```typescript
// frontend/lib/uploadthing/index.ts
import { generateReactHelpers } from '@uploadthing/react';
import type { OurFileRouter } from '@/app/api/uploadthing/core';

export const { useUploadThing, uploadFiles } = generateReactHelpers<OurFileRouter>();
```

### 4. Upload Component Usage

```tsx
'use client';

import { UploadButton } from '@uploadthing/react';
import type { OurFileRouter } from '@/app/api/uploadthing/core';

export function PhotoUploader() {
  return (
    <UploadButton<OurFileRouter, 'propertyPhotos'>
      endpoint="propertyPhotos"
      onClientUploadComplete={(res) => {
        const urls = res.map(file => file.ufsUrl);
        // Save URLs to state / database
      }}
      onUploadError={(error) => {
        console.error('Upload failed:', error.message);
      }}
    />
  );
}
```

---

## Rules

1. **Always authenticate uploads** — check user session in middleware before allowing uploads.
2. **Validate file types server-side** — don't rely solely on client-side validation.
3. **Set appropriate file size limits** per upload route (property photos: 20MB, avatars: 4MB).
4. **Store file URLs in Supabase** after successful upload via `onUploadComplete`.
5. **Use the `UPLOADTHING_TOKEN`** env var — never expose it client-side.
6. **Handle upload errors gracefully** — show user-friendly messages, not raw error objects.
7. **Rate limit uploads** per user tier to prevent abuse.
8. **Clean up orphaned files** — if a tour/property is deleted, remove associated uploaded files.

