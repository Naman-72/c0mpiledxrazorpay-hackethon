# PRODUCT REQUIREMENTS DOCUMENT
## Dubai Property 3D World Platform
### Immersive Real Estate Intelligence for the Dubai Market

**Version:** 1.0
**Date:** March 2026
**Status:** Draft — For Internal Review

---

> **Why Now — Dubai Market Context**
>
> - Dubai real estate reached AED 761 billion in transactions in 2024, a record high.
> - Over 60% of high-value deals are closed by brokers communicating via WhatsApp with overseas investors (Russia, UK, India, China).
> - Investors rarely visit in person before committing; trust-building through visual media is critical.
> - No platform currently combines ROI data intelligence with photorealistic 3D exploration in a single seamless flow.

---

## 1. Executive Summary

Dubai Property 3D World is a next-generation real estate discovery platform purpose-built for the Dubai market. It combines data-driven investment intelligence with photorealistic 3D spatial experiences — enabling investors, brokers, and end-users to evaluate, visualise, and share properties with unprecedented depth.

The platform operates as a three-layer ecosystem:

- A marketing landing web app that contextualises the product and routes users into two analytical tools.
- **Tool 1 — Dubai ROI Wizard:** A rental property ROI, yield, and IRR calculator.
- **Tool 2 — Dubai Prop Pulse:** A market pulse and property analytics dashboard.
- **The Core Product — 3D World Viewer:** Gaussian Splat-based photorealistic 3D property exploration, activated from either tool after a user has identified a property of interest.

The 3D Viewer is the primary differentiator and revenue driver. Once a user completes analysis in either tool, they are offered an immersive 3D walk-through of the property — complete with spatial ROI annotations, cinematic video export for WhatsApp distribution, and AI-powered furnishing overlays.

---

## 2. Goals & Success Metrics

### 2.1 Business Goals

- Establish the platform as the go-to digital toolkit for Dubai property investors and brokers.
- Generate revenue through SaaS subscriptions (brokers/agencies), per-property 3D scan fees, and white-label licensing.
- Onboard 500 broker accounts within 6 months of launch.
- Achieve 10,000 monthly active users within 12 months.

### 2.2 Product Goals

- Deliver a seamless funnel from market discovery → financial analysis → immersive 3D experience.
- Reduce the average time for an investor to develop conviction on a property from days to under 30 minutes.
- Enable brokers to generate and share cinematic property videos in under 5 minutes.

### 2.3 Key Performance Indicators (KPIs)

| KPI | Target (6 months) | Target (12 months) |
|---|---|---|
| Monthly Active Users | 2,000 | 10,000 |
| 3D Tours Generated | 500/month | 5,000/month |
| WhatsApp Video Exports | 200/month | 2,500/month |
| Broker Accounts (Paid) | 100 | 500 |
| Avg. Session Duration | > 8 minutes | > 12 minutes |
| Tool → 3D Viewer Conversion | > 20% | > 35% |
| Net Promoter Score | > 45 | > 60 |

---

## 3. Target Audience

### 3.1 Primary Users

| Persona | Profile | Primary Need |
|---|---|---|
| International Investor | HNW individual based in Russia, UK, India, or China seeking Dubai property for capital appreciation or rental yield. Never visits in person. | Photorealistic remote experience + ROI certainty before committing. |
| Dubai-Based Broker | Agency or independent broker managing a portfolio of off-plan and secondary market properties for international clients. | Fast, shareable visual assets and ROI data to close deals over WhatsApp. |
| End Buyer / Renter | Individual planning to buy or rent property for personal use. May be relocating or upgrading. | Understand the actual space, layout, and finishes before site visits. |
| Developer / Project Marketer | Real estate developer launching new off-plan projects needing differentiated digital marketing. | Showcase properties before physical units exist. |

### 3.2 Secondary Users

- Real estate journalists and market analysts using the ROI and pulse tools.
- Property management companies evaluating portfolios.
- Mortgage advisors and financial planners using yield data.

---

## 4. Product Architecture

The platform is composed of four distinct but interconnected components. Each can function independently, but the designed user journey flows linearly through them.

### 4.1 Architecture Overview

| Platform Components | |
|---|---|
| **[Layer 1] Landing Web App** | Main entry point, brand hub, tool router |
| **[Layer 2] Tool 1: Dubai ROI Wizard** | Financial analysis (ROI, yield, IRR, cashflow) |
| **[Layer 3] Tool 2: Dubai Prop Pulse** | Market intelligence, area trends, comparable data |
| **[Layer 4] 3D World Viewer** | Gaussian Splat 3D environment, cinematic tours, spatial annotations |

All four layers share a unified session/state system so that data from the tools (selected property, ROI results, market data) flows into the 3D Viewer as contextual overlays without users needing to re-enter information.

### 4.2 Component Responsibilities

| Component | Responsibility |
|---|---|
| Landing Web App | Brand presence, SEO, value proposition, tool discovery, user authentication, subscription management. |
| ROI Wizard (Tool 1) | Input property price, rental income, service charges, mortgage rate, holding period. Output: Gross yield, net yield, IRR, cashflow table, break-even analysis. |
| Prop Pulse (Tool 2) | Browse and filter Dubai property market data by area, property type, price range. Show comparable transactions, area growth trends, supply pipeline. |
| 3D World Viewer | Upload property photos → generate Gaussian Splat 3D scene → navigate, annotate, export video, overlay AI furniture, share link. |

---

## 5. User Flows

### 5.1 Primary Flow — Investor Journey

| End-to-End Investor Flow | |
|---|---|
| **Step 1** | User lands on the main web app via SEO, paid ad, or broker referral link. |
| **Step 2** | User reads about the platform and is presented with two entry points: ROI Wizard and Prop Pulse. |
| **Step 3a** | User selects ROI Wizard → enters property address/link, purchase price, expected rent, expenses. |
| **Step 3b** | ROI Wizard displays gross yield, net yield, IRR, 5-year cashflow projection, and comparison to Dubai area average. |
| **Step 4** | ROI Wizard surfaces CTA: 'View this property in 3D — See exactly what your investment looks like.' |
| **Step 5** | User clicks CTA → 3D Viewer loads with the property's Gaussian Splat scene. |
| **Step 6** | User navigates the 3D space, reads spatial ROI annotations anchored to room features. |
| **Step 7** | User downloads or shares a cinematic WhatsApp-ready video tour. |
| **Step 8** | User saves/bookmarks property or shares the full report link with a co-investor. |

### 5.2 Primary Flow — Broker Journey

| Broker Fast-Track Flow | |
|---|---|
| **Step 1** | Broker logs into the platform with a verified broker account. |
| **Step 2** | Broker uploads 10–30 photos of a newly listed property. |
| **Step 3** | Platform generates the Gaussian Splat 3D scene (1–5 minutes processing). |
| **Step 4** | Broker adds spatial annotations (view value, finish quality, yield hotspots). |
| **Step 5** | Broker clicks 'Generate Tour' — system renders a 30-second 1080p MP4 flythrough. |
| **Step 6** | Broker downloads the video and shares directly to client WhatsApp group. |
| **Step 7** | Broker shares a viewer link (mobile-optimised) with a personalised ROI summary appended. |
| **Step 8** | Interested investor clicks the link → sees 3D view + ROI data → contacts broker via embedded CTA. |

### 5.3 Alternative Flow — Market Browser

- User enters via Prop Pulse (Tool 2).
- User filters by area (e.g. Dubai Marina), property type (apartment), price range (AED 1.5M–3M).
- User sees a list/map of comparable transactions with average yield and price-per-sqft.
- User selects a specific property → sees ROI summary generated automatically.
- User is offered 3D Viewer CTA if a 3D scan exists for that property.

### 5.4 Authentication Flow

- **Guest users:** Can access ROI Wizard and Prop Pulse with limited features (3 calculations, no save).
- **Free registered users:** Unlimited tool usage, can save up to 3 properties, view shared 3D links.
- **Paid users / brokers:** Full 3D generation, video export, annotation, unlimited saves, white-label sharing.

---

## 6. Feature Specifications

### 6.1 Landing Web App

#### 6.1.1 Hero & Value Proposition Section

- Full-width hero section with embedded looping 3D scene as background (auto-playing Gaussian Splat or high-quality render video).
- Headline communicating the core value proposition: data meets dimension for Dubai real estate.
- Two prominent CTAs routing to Tool 1 (ROI Wizard) and Tool 2 (Prop Pulse).
- Sub-headline explaining the 3D Viewer as the culmination of the journey.

#### 6.1.2 Social Proof & Trust Section

- Number of properties scanned, investors served, brokers onboarded.
- Logo strip of partner agencies, developers, and media mentions.
- Short testimonial video or quote from a broker who closed a deal using the platform.

#### 6.1.3 Feature Showcase

- Interactive walkthrough of the three-step flow (Tools → ROI → 3D).
- Embedded 3D demo scene that visitors can explore without signing up.
- Comparison table showing platform vs. traditional property portals.

#### 6.1.4 Pricing & Plans Section

| Plan | Details |
|---|---|
| Free / Guest | 3 ROI calculations, Prop Pulse browsing, view shared 3D links, no video export. |
| Investor (Paid) | Unlimited calculations, save portfolios, generate up to 5 personal 3D tours/month, video export. |
| Broker Pro (Paid) | All Investor features + unlimited 3D scans, white-label video export, client CRM, priority processing. |
| Agency / Enterprise | Custom volume pricing, API access, branded landing pages, dedicated support. |

---

### 6.2 Tool 1 — Dubai ROI Wizard

#### 6.2.1 Input Parameters

- Property address or unit ID (auto-lookup to fetch listed price if available).
- Purchase price (AED).
- Expected annual rent (AED) — with area benchmark auto-suggested.
- Annual service charge (AED).
- Mortgage: Yes/No toggle. If yes: down payment %, rate %, term (years).
- Holding period (years) for IRR calculation.
- Estimated annual capital appreciation rate (pre-filled with area historical average).

#### 6.2.2 Output Metrics

- Gross rental yield (%).
- Net rental yield after service charges and management fees (%).
- Monthly cashflow (AED) — with and without mortgage.
- 5-year and 10-year IRR.
- Break-even occupancy rate.
- Total return (rental income + capital gain) over holding period.
- Visual cashflow chart (bar chart, year-by-year).
- Benchmark comparison: 'This property's yield vs. Dubai Marina average.'

#### 6.2.3 3D Viewer CTA Integration

- After results are displayed, a persistent 'View in 3D' panel appears below results.
- If a 3D scan exists for the address: loads immediately.
- If no scan: prompts user to upload photos or request a professional scan.

---

### 6.3 Tool 2 — Dubai Prop Pulse

#### 6.3.1 Market Intelligence Dashboard

- Area selector: Browse by community (Downtown, Marina, JBR, Business Bay, Palm, etc.).
- Property type filter: Apartment, villa, townhouse, commercial.
- Price range slider (AED).
- Bedrooms filter (Studio, 1BR, 2BR, 3BR, 4BR+).

#### 6.3.2 Developer Intelligence Reports (Crustdata + Gemini)

This is the **AI-native agency** feature — it replaces what a $500/hour real estate analyst does manually. Users enter a developer name (e.g. Emaar, Sobha, Damac, Nakheel) and receive an institutional-grade due diligence report generated in seconds.

**Data Layer — Crustdata API:**
- **Company profile:** Headcount, founded date, HQ location, LinkedIn URL, website.
- **Growth signals:** Headcount growth over 6/12/24 months — a leading indicator of developer health and expansion.
- **Hiring momentum:** Active job postings count and trend — signals upcoming project launches.
- **Funding & financial health:** Total funding raised, last funding round, investors.
- **Decision-maker contacts:** Key executives (CEO, CFO, Head of Sales) with LinkedIn profiles for broker outreach.
- **Competitive benchmarking:** Compare two developers side-by-side on growth metrics.

**AI Synthesis Layer — Gemini:**
- Crustdata's raw B2B data is fed into Gemini 2.5 Flash with a system instruction to produce an **investment-grade developer assessment**.
- **Output:** A 1–2 page narrative report covering:
  - Developer financial health score (derived from growth signals).
  - Project delivery track record assessment.
  - Workforce momentum analysis (hiring = expansion, layoffs = risk).
  - Key risk factors and red flags.
  - Recommendation: "Strong Buy / Buy / Hold / Caution" rating with justification.
- **Tone:** Institutional analyst briefing — objective, data-driven, no marketing language.
- **Format:** Rendered as a shareable web page with PDF export option.

**Why this is an AI-native agency play:**
- Traditional due diligence on a developer costs AED 5,000–15,000 from a consulting firm and takes 1–2 weeks.
- This platform delivers the same deliverable in 30 seconds for AED 50–200 per report.
- The "finished work" (the report) is what the user pays for — not access to a tool.
- Scale: one backend can serve 10,000 reports/day with zero additional headcount.

**Access:**
- Free tier: 1 report/month (watermarked).
- Investor tier: 10 reports/month.
- Broker Pro: Unlimited reports, white-label PDF with their branding.

#### 6.3.3 Data Outputs

- Average price per sqft by area and bedroom count.
- Average gross rental yield by area.
- Recent transaction volume (last 30, 90, 365 days).
- Supply pipeline: off-plan units expected to complete in next 12/24 months.
- Price trend chart: historical price index over 3/5 years.
- Top-performing areas ranked by yield.

#### 6.3.4 Property Listing Integration

- Optional: Link to live DLD (Dubai Land Department) transaction data feed.
- Property cards with photo, price, beds, sqft, yield, and 3D tour availability badge.
- Map view with heatmap overlay for price/yield intensity by area.

---

### 6.4 Core Product — 3D World Viewer

#### 6.4.1 3D Scene Generation (Gaussian Splat)

- **Photo upload:** broker or user uploads 10–50 photos of a property (JPEG/HEIC/PNG, max 20MB each).
- **Processing pipeline:** photos are processed using Gaussian Splatting algorithms to generate a continuous 3D point-cloud scene.
- **Processing time target:** under 5 minutes for 2-3 photos.
- **Scene quality levels:** Standard (faster, good for sharing), High (full fidelity, for recording).
- **Progress indicator:** real-time processing status with percentage and ETA.
- **Scene storage:** each generated scene is saved to the user's account, accessible and shareable via a unique URL.

#### 6.4.2 Web-Based 3D Navigator

- Browser-native 3D viewer, no plugin or app download required.
- Navigation controls: click-to-move, orbit, zoom, first-person walk mode.
- Mobile-optimised touch controls: swipe to orbit, pinch to zoom, tap to move.
- Load time: under 8 seconds on a standard broadband connection.
- Cross-browser support: Chrome, Safari, Firefox, Edge (latest 2 versions).
- Ambient audio: optional background audio (e.g. city soundscape for Dubai views) to enhance immersion.

#### 6.4.3 Cinematic WhatsApp Tour Generator

This is a flagship feature targeting Dubai brokers who close deals over WhatsApp with international clients.

- **'Generate Tour' button:** One-click initiation of the automated camera path recording.
- **Camera path algorithm:** System analyses the Gaussian Splat to identify key spatial zones (entrance, living room, kitchen, bedroom, bathroom, balcony/view) and generates a smooth, cinematically optimal flythrough path.
- **Duration:** Default 30 seconds. Options: 15s, 30s, 60s.
- **Output format:** 1080p MP4, optimised for WhatsApp delivery (H.264, target file size under 25MB).
- **Branding overlay:** Broker's logo and contact details watermarked in bottom-right corner (configurable in Broker Pro settings).
- **Music:** Optional background music from a licensed royalty-free library (Luxury, Modern, Energetic presets).
- **Processing time:** Under 3 minutes for a 30-second video at 1080p.
- **Delivery:** Downloadable directly from the platform. Optionally sent to broker's registered WhatsApp number via integration.

#### 6.4.4 Spatial ROI & Luxury Annotation System

Annotations are interactive HTML overlay elements anchored to specific X, Y, Z coordinates within the 3D Gaussian Splat scene. They appear as glowing orbs or tag pins and reveal information on hover/click.

**Annotation types:**
- **ROI Hotspot** — e.g. 'Projected Short-Term Rental Yield: 8.5%' anchored to the living room.
- **View Value Tag** — e.g. 'Guaranteed Full Marina View — Adds 15% to Resale Value' anchored to the floor-to-ceiling window.
- **Finish Quality Tag** — e.g. 'Italian Calacatta Marble — Standard Developer Finish' anchored to the kitchen surface.
- **Location Context Tag** — e.g. 'Walking Distance to Dubai Marina Metro' floating near the window view.
- **Custom Tag** — Broker-editable free text + icon.

- **Annotation editor:** Drag-and-drop within the 3D scene to place tags; fill a form for text, icon, and data type.
- **Auto-suggestions:** AI suggests annotation placements and text based on property data from the ROI Wizard (yield, finish tier, area premiums).
- **Visibility in video:** Annotations appear as subtle overlays in the exported cinematic video with smooth fade-in/out transitions.

#### 6.4.5 AI Furnishing & Staging Overlay

- Empty or partially furnished properties can be virtually staged using AI-generated furniture overlays rendered on top of the Gaussian Splat.
- **Style presets:** Minimal Modern, Contemporary Luxury, Arabian Heritage, Scandinavian.
- **Room-level toggle:** Apply staging to specific rooms only.
- **Before/After slider:** User can swipe to compare furnished vs. unfurnished in the 3D view.
- **Export:** Staged version can be recorded as a separate video or screenshot for marketing materials.

#### 6.4.6 Property Sharing & Collaboration

- Each 3D scene generates a unique shareable URL (e.g. `platform.com/tour/XXXXX`).
- Link is publicly accessible without login for the recipient (view-only, no edit).
- Optional password protection for exclusive off-market listings.
- Embedded ROI summary card: shared link shows a property data panel alongside the 3D viewer.
- WhatsApp share button: pre-fills a message template for brokers (property address, yield, price, tour link).
- QR code generation: for physical marketing materials (brochures, hoardings) linking to the 3D tour.

#### 6.4.7 Floor Plan Integration

- If a floor plan (PDF or image) is available, it is displayed as a 2D minimap in the corner of the 3D viewer.
- Position marker: shows the user's current location in the 3D scene mapped onto the floor plan.
- Click on floor plan to teleport to that location in the 3D scene.

#### 6.4.8 Comparison Mode

- Side-by-side comparison of two 3D properties in split-screen view.
- Synchronised navigation: moving in one view mirrors movement in the other.
- ROI comparison panel: both properties' key metrics shown in a data card below the split view.

---

### 6.5 AI-Powered Content Generation (Gemini + Crustdata)

The platform integrates Google Gemini AI (`@google/genai` SDK) and Crustdata API to automate content creation and intelligence tasks that would otherwise require expensive human analysts, copywriters, and consultants. This is the core of the **AI-native agency** model — selling finished work (reports, descriptions, narratives) at software margins.

#### 6.5.1 AI Property Description Generator

When a broker or user creates a property listing with basic metadata (address, area, bedrooms, sqft, price, property type), the platform offers a one-click "Generate Description" action powered by Gemini.

- **Input:** Property metadata from the `properties` table (address, area, property_type, bedrooms, bathrooms, sqft, price_aed, annual_rent_aed) + optional uploaded photos.
- **Output:** A polished, luxury-toned property description (150–250 words) in English, optimised for WhatsApp sharing and marketing materials.
- **Tone:** Professional Dubai real estate marketing — confident, aspirational, specific to the area and property type.
- **Highlights extraction:** Gemini identifies and emphasises key selling points (view, location, finishes, yield potential) based on the metadata.
- **Editable:** The generated description is presented as a draft that the user can edit before saving.
- **Regenerate:** Users can click "Regenerate" to get an alternative version without re-entering data.
- **Access:** Available to all registered users. Free tier: 5 generations/month. Paid tiers: unlimited.

#### 6.5.2 AI ROI Report Narrative

After the ROI Wizard calculates financial metrics (gross yield, net yield, IRR, cashflow), the platform generates a plain-language investment summary using Gemini.

- **Input:** Calculated ROI metrics from the `roi_calculations` table (purchase_price, annual_rent, gross_yield, net_yield, monthly_cashflow, irr_5yr, irr_10yr, holding_period_years, appreciation_rate_pct) + property area for benchmarking context.
- **Output:** A 2–3 paragraph investment narrative (100–200 words) that contextualises the numbers for a non-technical investor.
- **Content includes:**
  - Whether the yield is above or below the area average.
  - Cashflow timeline — when monthly cashflow turns positive (if mortgaged).
  - Risk factors — sensitivity to vacancy, rate changes.
  - Comparison framing — "This property outperforms X% of similar units in [area]."
- **Shareable:** The narrative is included in the ROI report PDF/link that brokers share with clients.
- **Tone:** Objective and data-driven, not salesy — positioned as an analyst briefing.
- **Access:** Available to all registered users. Free tier: 3 narratives/month. Paid tiers: unlimited.

---

## 7. Technical Requirements

### 7.1 3D Processing Stack

| Component | Technology / Approach |
|---|---|
| 3D Scene Generation | World Labs Marble API |
| Photo Ingestion | Upload via pre-signed S3 URLs. JPEG, HEIC, PNG supported. Max 50 photos per session. |
| Processing Backend | Node.js backend with Express. |
| 3D Scene Format | Compressed .ply or .splat format for web delivery. |
| Web 3D Renderer | Three.js or Babylon.js with Gaussian Splat rendering shader. WebGL 2.0 required. |
| Video Generation | Server-side headless browser (Puppeteer/Playwright) captures scene + Gaussian Splat render, FFmpeg encodes to H.264 MP4. |
| Annotation Storage | Scene graph: X,Y,Z coordinates + annotation metadata stored in JSON, linked to scene ID in database. |

### 7.2 Frontend Stack

- **Framework:** React (Next.js) for the landing app and tool pages.
- **3D Viewer:** Standalone React component or iframe-embedded module.
- **Styling:** TailwindCSS with a custom Dubai luxury design system (dark navy, gold accents).
- **Maps:** Mapbox GL JS for area heatmaps and property location.
- **Charts:** Recharts or Chart.js for ROI outputs and market trends.
- **Mobile:** Fully responsive; 3D viewer touch controls tested on iOS Safari and Android Chrome.

### 7.3 Backend Stack

- **API:** Node.js (Express).
- **Authentication:** Supabase Auth.
- **Database:** Supabase for user data, property metadata, annotations.
- **File Storage:** Uploadthing for file uploads.
- **CDN:** Uploadcare CDN
- **AI Content Generation:** Google Gemini AI via `@google/genai` SDK (Gemini 2.5 Flash model).
- **B2B Data Enrichment:** Crustdata API for real-time company/developer intelligence (headcount, funding, hiring signals).

### 7.4 Integrations

| Integration | Purpose |
|---|---|
| Google Gemini AI | AI content generation — descriptions, ROI narratives, developer due diligence reports. |
| Crustdata API | Real-time B2B data enrichment — company growth signals, headcount trends, hiring momentum, funding data for developer intelligence. |
| Dubai Land Department (DLD) API | Live transaction data for Prop Pulse market intelligence. |
| Property portals (Bayut, Property Finder) | Optional: listing data ingestion for auto-populate ROI Wizard. |

---


