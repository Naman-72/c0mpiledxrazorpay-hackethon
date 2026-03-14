# BACKEND.md — Backend Development Rules

## Dubai Property 3D World · Node.js + Express + Supabase

---

## 1. Project Structure

```
backend/src/
├── index.ts                  # App entry point, Express setup
├── config/
│   ├── env.ts                # Environment variable validation
│   ├── supabase.ts           # Supabase client initialization
│   └── gemini.ts             # Google Gemini AI client initialization
├── middleware/
│   ├── auth.ts               # JWT validation middleware
│   ├── validate.ts           # Request validation (Zod schemas)
│   ├── errorHandler.ts       # Global error handler
│   ├── rateLimiter.ts        # Rate limiting
│   └── cors.ts               # CORS configuration
├── routes/
│   ├── auth.routes.ts        # Auth endpoints
│   ├── property.routes.ts    # Property CRUD
│   ├── roi.routes.ts         # ROI calculations
│   ├── market.routes.ts      # Market data / Prop Pulse
│   ├── tour.routes.ts        # 3D tour management
│   ├── job.routes.ts         # Background job status
│   ├── upload.routes.ts      # File upload coordination
│   ├── ai.routes.ts          # AI content generation (Gemini)
│   └── developer.routes.ts   # Developer intelligence (Crustdata + Gemini)
├── services/
│   ├── auth.service.ts       # Auth business logic
│   ├── property.service.ts   # Property business logic
│   ├── roi.service.ts        # ROI calculation engine
│   ├── market.service.ts     # Market data aggregation
│   ├── tour.service.ts       # Tour management logic
│   ├── worldApi.service.ts   # World Labs Marble API client
│   ├── video.service.ts      # Video export processing
│   ├── ai.service.ts         # Gemini AI content generation
│   └── crustdata.service.ts  # Crustdata API client for developer intelligence
├── models/
│   ├── property.model.ts     # Supabase queries for properties
│   ├── tour.model.ts         # Supabase queries for tours
│   └── user.model.ts         # Supabase queries for users
├── jobs/
│   ├── jobQueue.ts           # Job queue manager
│   ├── generateTour.job.ts   # 3D generation job processor
│   └── exportVideo.job.ts    # Video export job processor
├── utils/
│   ├── logger.ts             # Structured logging
│   ├── errors.ts             # Custom error classes
│   └── helpers.ts            # General utility functions
└── types/
    ├── api.types.ts           # Request/response types
    └── models.types.ts        # Database model types
```

---

## 2. Express Setup Rules

### App Initialization

```typescript
import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import { errorHandler } from './middleware/errorHandler';
import { authMiddleware } from './middleware/auth';

const app = express();

// Security
app.use(helmet());
app.use(cors({ origin: process.env.FRONTEND_URL, credentials: true }));

// Body parsing
app.use(express.json({ limit: '10mb' }));

// Routes
app.use('/api/auth', authRoutes);
app.use('/api/properties', authMiddleware, propertyRoutes);
app.use('/api/roi', authMiddleware, roiRoutes);
app.use('/api/market', marketRoutes);  // Public
app.use('/api/tours', authMiddleware, tourRoutes);
app.use('/api/jobs', authMiddleware, jobRoutes);
app.use('/api/ai', authMiddleware, aiRoutes);

// Error handling (must be last)
app.use(errorHandler);
```

### Rules

- **TypeScript strict mode** — all files must be `.ts`, no `.js`.
- **Always validate request bodies** using Zod schemas in the `validate` middleware.
- **Never use `any`** — define explicit types for all request/response objects.
- **Max 300 lines per file.** Extract into services if route handlers grow.

---

## 3. Route Handler Pattern

```typescript
import { Router } from 'express';
import { z } from 'zod';
import { validate } from '../middleware/validate';
import { propertyService } from '../services/property.service';
import { AppError } from '../utils/errors';

const router = Router();

const createPropertySchema = z.object({
  address: z.string().min(1),
  price: z.number().positive(),
  bedrooms: z.number().int().min(0),
  sqft: z.number().positive(),
});

router.post('/', validate(createPropertySchema), async (req, res, next) => {
  try {
    const property = await propertyService.create(req.body, req.user.id);
    res.status(201).json({ data: property });
  } catch (error) {
    next(error);
  }
});

export { router as propertyRoutes };
```

---

## 4. Authentication Middleware

```typescript
import { createClient } from '@supabase/supabase-js';

export const authMiddleware = async (req, res, next) => {
  const token = req.headers.authorization?.replace('Bearer ', '');
  if (!token) return res.status(401).json({ error: 'Missing token', code: 'AUTH_MISSING' });

  const { data: { user }, error } = await supabase.auth.getUser(token);
  if (error || !user) return res.status(401).json({ error: 'Invalid token', code: 'AUTH_INVALID' });

  req.user = user;
  next();
};
```

---

## 5. Error Handling

### Custom Error Classes

```typescript
export class AppError extends Error {
  constructor(
    public message: string,
    public statusCode: number = 500,
    public code: string = 'INTERNAL_ERROR'
  ) {
    super(message);
  }
}

export class NotFoundError extends AppError {
  constructor(resource: string) {
    super(`${resource} not found`, 404, 'NOT_FOUND');
  }
}

export class ForbiddenError extends AppError {
  constructor() {
    super('Access denied', 403, 'FORBIDDEN');
  }
}
```

### Global Error Handler

```typescript
export const errorHandler = (err, req, res, next) => {
  const statusCode = err.statusCode || 500;
  const code = err.code || 'INTERNAL_ERROR';

  // Log error (never log to console in production)
  logger.error({ err, req: { method: req.method, url: req.url } });

  res.status(statusCode).json({
    error: err.message,
    code,
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack }),
  });
};
```

---

## 6. Supabase Client Setup

```typescript
import { createClient } from '@supabase/supabase-js';

// Service role client — ONLY for backend admin operations
export const supabaseAdmin = createClient(
  process.env.SUPABASE_URL!,
  process.env.SUPABASE_SERVICE_ROLE_KEY!
);

// Per-request client — respects RLS using user's JWT
export const createUserClient = (token: string) =>
  createClient(process.env.SUPABASE_URL!, process.env.SUPABASE_ANON_KEY!, {
    global: { headers: { Authorization: `Bearer ${token}` } },
  });
```

### Rules

- **Never use `supabaseAdmin` in user-facing routes** — always create a per-request client.
- **Always pass the user's JWT** to the per-request client so RLS policies are enforced.
- **Service role key** is only for: webhooks, cron jobs, admin scripts, migrations.

---

## 7. Environment Variables

Validate all env vars at startup:

```typescript
import { z } from 'zod';

const envSchema = z.object({
  PORT: z.string().default('3001'),
  SUPABASE_URL: z.string().url(),
  SUPABASE_SERVICE_ROLE_KEY: z.string().min(1),
  SUPABASE_ANON_KEY: z.string().min(1),
  WORLD_LABS_API_KEY: z.string().min(1),
  GEMINI_API_KEY: z.string().min(1),
  CRUSTDATA_API_KEY: z.string().min(1),
  UPLOADTHING_TOKEN: z.string().min(1),
  FRONTEND_URL: z.string().url(),
});

export const env = envSchema.parse(process.env);
```

