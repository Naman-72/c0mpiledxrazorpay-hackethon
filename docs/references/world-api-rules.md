# World Labs Marble API — Integration Rules

## Overview

The World Labs Marble API generates explorable 3D worlds from text, images, panoramas, multi-view inputs, and video. It produces navigable spatial environments rendered as Gaussian Splat scenes.

- **API Platform:** https://platform.worldlabs.ai
- **Inputs:** Text, single images, multi-image sets, 360° panoramas, video
- **Output:** Navigable 3D world (Gaussian Splat / .splat format)
- **Processing:** Asynchronous — submit request, poll for completion

---

## Integration Pattern

### 1. Job Submission

```typescript
// POST to World Labs API to create a generation job
const createWorldJob = async (photoUrls: string[]): Promise<string> => {
  const response = await fetch('https://api.worldlabs.ai/v1/worlds', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${process.env.WORLD_LABS_API_KEY}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      inputs: photoUrls.map(url => ({ type: 'image', url })),
      quality: 'standard',  // 'standard' or 'high'
    }),
  });

  const data = await response.json();
  return data.job_id;  // Store this for polling
};
```

### 2. Job Status Polling

```typescript
// Poll for job completion — do NOT use webhooks as primary method
const pollJobStatus = async (jobId: string): Promise<WorldJobResult> => {
  const response = await fetch(`https://api.worldlabs.ai/v1/worlds/${jobId}`, {
    headers: { 'Authorization': `Bearer ${process.env.WORLD_LABS_API_KEY}` },
  });

  const data = await response.json();
  // data.status: 'queued' | 'processing' | 'complete' | 'failed'
  // data.scene_url: URL to .splat file (when complete)
  return data;
};
```

### 3. Backend Job Queue Integration

```typescript
// In backend/src/jobs/generateTour.job.ts
const POLL_INTERVAL_MS = 10_000;  // 10 seconds
const MAX_POLL_ATTEMPTS = 60;     // 10 minutes max

const processGenerateTourJob = async (tourId: string, photoUrls: string[]) => {
  // 1. Submit to World Labs
  const jobId = await createWorldJob(photoUrls);

  // 2. Update tour record with job ID
  await supabase.from('tours').update({
    world_api_job_id: jobId,
    status: 'processing',
  }).eq('id', tourId);

  // 3. Poll for completion
  let attempts = 0;
  while (attempts < MAX_POLL_ATTEMPTS) {
    await sleep(POLL_INTERVAL_MS);
    const result = await pollJobStatus(jobId);

    if (result.status === 'complete') {
      await supabase.from('tours').update({
        scene_url: result.scene_url,
        status: 'complete',
        processing_time_ms: result.processing_time_ms,
      }).eq('id', tourId);
      return;
    }

    if (result.status === 'failed') {
      await supabase.from('tours').update({ status: 'failed' }).eq('id', tourId);
      throw new Error(`World API job failed: ${result.error}`);
    }

    attempts++;
  }

  // Timeout
  await supabase.from('tours').update({ status: 'failed' }).eq('id', tourId);
  throw new Error('World API job timed out');
};
```

---

## Rules

1. **Always process asynchronously.** Never block an API response waiting for 3D generation.
2. **Store the `world_api_job_id`** in the `tours` table for tracking and debugging.
3. **Rate limit generation** per user tier:
   - Free: 0 generations
   - Investor: 5/month
   - Broker Pro: unlimited
4. **Validate photos before submission:**
   - Minimum 10 photos, maximum 50
   - Accepted formats: JPEG, PNG, HEIC
   - Max 20MB per photo
   - Photos must be of the same property (no mixing)
5. **Handle failures gracefully** — retry once on transient errors, then mark as failed.
6. **Cache scene URLs** — once generated, a scene is permanent and reusable.
7. **Never expose the API key** to the frontend — all World Labs calls go through the backend.
8. **Monitor API credit usage** via the World Labs Platform dashboard.

---

## Frontend Loading Pattern

```tsx
'use client';

import dynamic from 'next/dynamic';

// Lazy-load the 3D viewer — it's heavy and needs WebGL
const GaussianSplatViewer = dynamic(
  () => import('@/components/viewer/GaussianSplatViewer'),
  { ssr: false, loading: () => <ViewerSkeleton /> }
);

export function TourPage({ sceneUrl }: { sceneUrl: string }) {
  return (
    <Suspense fallback={<ViewerSkeleton />}>
      <GaussianSplatViewer sceneUrl={sceneUrl} />
    </Suspense>
  );
}
```

