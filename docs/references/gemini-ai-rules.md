# Google Gemini AI — Integration Rules

## Overview

The platform uses Google Gemini AI via the `@google/genai` SDK for three content generation features:

1. **AI Property Description Generator** — generates luxury-toned marketing descriptions from property metadata.
2. **AI ROI Report Narrative** — generates plain-language investment summaries from calculated ROI metrics.
3. **Developer Intelligence Report** — synthesizes Crustdata B2B data into institutional-grade developer due diligence reports.

- **SDK:** `@google/genai` (unified Google Gen AI SDK for JavaScript/TypeScript)
- **Model:** `gemini-2.5-flash` (fast, cost-effective for text generation)
- **Backend only** — all Gemini calls go through the Express backend. Never expose the API key to the frontend.

---

## SDK Setup

```typescript
// backend/src/config/gemini.ts
import { GoogleGenAI } from '@google/genai';

export const gemini = new GoogleGenAI({
  apiKey: process.env.GEMINI_API_KEY!,
});

export const GEMINI_MODEL = 'gemini-2.0-flash';
```

---

## Feature 1: Property Description Generator

### Service Pattern

```typescript
// backend/src/services/ai.service.ts
import { gemini, GEMINI_MODEL } from '../config/gemini';

interface PropertyDescriptionInput {
  address: string;
  area: string;
  propertyType: string;
  bedrooms: number;
  bathrooms: number;
  sqft: number;
  priceAed: number;
  annualRentAed?: number;
}

export const generatePropertyDescription = async (
  property: PropertyDescriptionInput
): Promise<string> => {
  const response = await gemini.models.generateContent({
    model: GEMINI_MODEL,
    contents: `Generate a property listing description for this Dubai property:
Address: ${property.address}
Area: ${property.area}
Type: ${property.propertyType}
Bedrooms: ${property.bedrooms} | Bathrooms: ${property.bathrooms}
Size: ${property.sqft} sqft
Price: AED ${property.priceAed.toLocaleString()}
${property.annualRentAed ? `Annual Rent: AED ${property.annualRentAed.toLocaleString()}` : ''}`,
    config: {
      systemInstruction: `You are a luxury Dubai real estate copywriter. Write a compelling 150-250 word property description. Be specific to the area and property type. Highlight key selling points (location, views, finishes, lifestyle, investment potential). Tone: confident, aspirational, professional. Do not use generic filler. Every sentence must add value. Do not include the price or address — those are shown separately in the UI.`,
      temperature: 0.8,
      maxOutputTokens: 512,
    },
  });

  return response.text ?? '';
};
```

---

## Feature 2: ROI Report Narrative

### Service Pattern

```typescript
// backend/src/services/ai.service.ts (continued)
interface RoiNarrativeInput {
  area: string;
  purchasePrice: number;
  annualRent: number;
  grossYield: number;
  netYield: number;
  monthlyCashflow: number;
  irr5yr: number;
  irr10yr: number;
  holdingPeriodYears: number;
  appreciationRatePct: number;
  hasMortgage: boolean;
}

export const generateRoiNarrative = async (
  roi: RoiNarrativeInput
): Promise<string> => {
  const response = await gemini.models.generateContent({
    model: GEMINI_MODEL,
    contents: `Generate an investment analysis narrative for this Dubai property:
Area: ${roi.area}
Purchase Price: AED ${roi.purchasePrice.toLocaleString()}
Annual Rent: AED ${roi.annualRent.toLocaleString()}
Gross Yield: ${roi.grossYield.toFixed(2)}%
Net Yield: ${roi.netYield.toFixed(2)}%
Monthly Cashflow: AED ${roi.monthlyCashflow.toLocaleString()}
5-Year IRR: ${roi.irr5yr.toFixed(2)}%
10-Year IRR: ${roi.irr10yr.toFixed(2)}%
Holding Period: ${roi.holdingPeriodYears} years
Capital Appreciation Rate: ${roi.appreciationRatePct}%
Has Mortgage: ${roi.hasMortgage ? 'Yes' : 'No'}`,
    config: {
      systemInstruction: `You are a Dubai real estate investment analyst. Write a 2-3 paragraph (100-200 words) objective investment summary for a non-technical investor. Include: whether yield is competitive for the area, cashflow timeline, key risk factors (vacancy, rate sensitivity), and overall investment quality. Tone: data-driven and objective like an analyst briefing — not salesy. Use specific numbers from the data provided.`,
      temperature: 0.6,
      maxOutputTokens: 512,
    },
  });

  return response.text ?? '';
};
```

---

## Route Handler Pattern

```typescript
// backend/src/routes/ai.routes.ts
import { Router } from 'express';
import { z } from 'zod';
import { validate } from '../middleware/validate';
import { generatePropertyDescription, generateRoiNarrative } from '../services/ai.service';

const router = Router();

const descriptionSchema = z.object({
  address: z.string().min(1),
  area: z.string().min(1),
  propertyType: z.string(),
  bedrooms: z.number().int().min(0),
  bathrooms: z.number().int().min(0),
  sqft: z.number().positive(),
  priceAed: z.number().positive(),
  annualRentAed: z.number().positive().optional(),
});

router.post('/generate-description', validate(descriptionSchema), async (req, res, next) => {
  try {
    const description = await generatePropertyDescription(req.body);
    res.json({ data: { description } });
  } catch (error) {
    next(error);
  }
});

export { router as aiRoutes };
```

---

## Rules

1. **Backend only** — all Gemini API calls go through Express routes. Never call from the frontend.
2. **Use `gemini-2.0-flash`** — fast and cost-effective for text generation tasks. Use `gemini-2.0-flash` unless a task specifically requires a larger model.
3. **Use `systemInstruction`** — always provide a system instruction to control tone and output format. This keeps prompts consistent and outputs predictable.
4. **Set `temperature`** appropriately — `0.8` for creative copy (descriptions), `0.6` for analytical text (ROI narratives). Never use `1.0+` for production.
5. **Set `maxOutputTokens`** — cap at `512` for descriptions and narratives. Prevents runaway responses and controls cost.
6. **Handle errors gracefully** — Gemini API calls can fail (rate limits, content filtering, network). Always wrap in try/catch and return a user-friendly error.
7. **Rate limit AI endpoints** — apply per-user rate limits based on subscription tier:
   - Free: 5 description generations/month, 3 ROI narratives/month
   - Investor: unlimited
   - Broker Pro: unlimited
8. **Never store the API key client-side** — `GEMINI_API_KEY` is a backend-only env var.
9. **Cache responses** — if the same property metadata hasn't changed, serve the cached description instead of calling Gemini again. Store generated descriptions in the `properties` table.
10. **Validate inputs before calling Gemini** — use Zod schemas to ensure all required property fields are present. Don't send incomplete data to the model.
11. **Sanitise outputs** — strip any unexpected formatting, HTML, or markdown that the model might inject before returning to the frontend.
12. **Log usage** — track Gemini API calls per user for billing awareness and abuse detection.

