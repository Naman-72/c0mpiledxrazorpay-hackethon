# Uploadcare — CDN & Image Processing Rules

## Overview

Uploadcare serves as the CDN and image transformation layer for the platform. Property images, thumbnails, and marketing assets are served through Uploadcare's CDN with on-the-fly transformations.

- **CDN URL pattern:** `https://{subdomain}.ucarecdn.net/{uuid}/`
- **Transformations:** Applied via URL parameters (resize, crop, format, quality)
- **Components:** `@uploadcare/file-uploader` web components

---

## Image Transformation Patterns

### URL-Based Transformations

```
# Resize to 800px width, maintain aspect ratio
https://ucarecdn.com/{uuid}/-/resize/800x/

# Crop to 16:9 and optimize for web
https://ucarecdn.com/{uuid}/-/scale_crop/1920x1080/center/-/format/webp/-/quality/smart/

# Thumbnail for property cards (400x300, smart crop)
https://ucarecdn.com/{uuid}/-/scale_crop/400x300/smart/-/format/webp/-/quality/lighter/

# Avatar (circular crop, 200x200)
https://ucarecdn.com/{uuid}/-/scale_crop/200x200/center/-/format/webp/
```

### Helper Utility

```typescript
// frontend/lib/utils/imageUrl.ts
const UPLOADCARE_CDN = 'https://ucarecdn.com';

export const imageUrl = (uuid: string, transforms: string = '') =>
  `${UPLOADCARE_CDN}/${uuid}/${transforms}`;

export const propertyThumbnail = (uuid: string) =>
  imageUrl(uuid, '-/scale_crop/400x300/smart/-/format/webp/-/quality/lighter/');

export const propertyHero = (uuid: string) =>
  imageUrl(uuid, '-/resize/1920x/-/format/webp/-/quality/smart/');

export const avatarUrl = (uuid: string) =>
  imageUrl(uuid, '-/scale_crop/200x200/center/-/format/webp/');
```

---

## Next.js Integration

### Image Component with Uploadcare CDN

```typescript
// next.config.ts
const nextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: '*.ucarecdn.net',
      },
      {
        protocol: 'https',
        hostname: 'ucarecdn.com',
      },
    ],
  },
};
```

### Usage with next/image

```tsx
import Image from 'next/image';
import { propertyThumbnail } from '@/lib/utils/imageUrl';

export function PropertyCard({ imageUuid, title }: Props) {
  return (
    <Image
      src={propertyThumbnail(imageUuid)}
      alt={title}
      width={400}
      height={300}
      className="rounded-lg object-cover"
    />
  );
}
```

---

## Responsive Image Component

```tsx
// frontend/components/ui/UploadcareImage.tsx
import Image from 'next/image';

interface UploadcareImageProps {
  uuid: string;
  alt: string;
  width: number;
  height: number;
  className?: string;
}

const UPLOADCARE_CDN = 'https://ucarecdn.com';

export function UploadcareImage({ uuid, alt, width, height, className }: UploadcareImageProps) {
  const src = `${UPLOADCARE_CDN}/${uuid}/-/resize/${width}x${height}/-/format/webp/-/quality/smart/`;
  return <Image src={src} alt={alt} width={width} height={height} className={className} />;
}
```

---

## Rules

1. **Always use Uploadcare CDN URLs** for serving images — never serve raw uploaded files.
2. **Apply transformations via URL** — resize, crop, and format images on the fly.
3. **Use WebP format** (`-/format/webp/`) for all web-served images — smaller file sizes.
4. **Use `quality/smart/`** for automatic quality optimization based on content.
5. **Generate responsive images** — serve different sizes for mobile/tablet/desktop.
6. **Configure `next.config.ts`** to allow Uploadcare domains in `next/image`.
7. **Cache aggressively** — Uploadcare CDN URLs are immutable (same UUID = same content).
8. **Use signed URLs** for premium/private content to prevent unauthorized access.
9. **Store only UUIDs** in the database — construct full CDN URLs at render time.
10. **Public key** can be exposed client-side (`NEXT_PUBLIC_UPLOADCARE_PUBLIC_KEY`).
11. **Secret key** is backend-only — used for signed URLs and admin operations.

