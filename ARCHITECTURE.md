# Marketing Analytics Auditor — Architecture & Project Plan

**Codename:** Auditor
**Author:** John Williams / It All Started With A Idea
**Repository:** `github.com/itallstartedwithaidea/analytics-auditor`
**Live URL:** `googleadsagent.ai/tools/auditor/`
**Status:** Architecture Phase
**Date:** February 14, 2026

---

## Table of Contents

- [Vision](#vision)
- [Product Scope](#product-scope)
- [Architecture](#architecture)
- [Phase 1: Page Scanner & Tag Detector](#phase-1-page-scanner--tag-detector)
- [Phase 2: GA4 Property Audit](#phase-2-ga4-property-audit)
- [Phase 3: Pixel & CAPI Audit](#phase-3-pixel--capi-audit)
- [Phase 4: Remediation Engine](#phase-4-remediation-engine)
- [Phase 5: AI Page Vision](#phase-5-ai-page-vision)
- [Phase 6: Multi-Site Portfolio](#phase-6-multi-site-portfolio)
- [GA4 Audit Checks](#ga4-audit-checks)
- [GTM Audit Checks](#gtm-audit-checks)
- [Pixel Audit Checks](#pixel-audit-checks)
- [Remediation Code Generation](#remediation-code-generation)
- [AI Page Scanning Spec](#ai-page-scanning-spec)
- [Tech Stack](#tech-stack)
- [API Requirements](#api-requirements)
- [File Structure](#file-structure)
- [Competitive Landscape](#competitive-landscape)
- [Open Source Strategy](#open-source-strategy)

---

## Vision

A free, open-source marketing analytics auditor that scans websites, audits GA4/GTM/pixel configurations, and — critically — generates the exact remediation code to fix what it finds. The tool that does what every agency charges $5K–$15K for, then goes one step further by handing dev teams copy-paste code instead of a PDF of problems.

**The gap in the market:** Everyone does the "here's what's wrong" part. Nobody automates the "here's the exact code to fix it" part. This tool does both.

**Target users:**
- Marketing operators running audits for clients
- Agency analytics teams managing 10–100+ properties
- In-house teams who need to hand dev teams specific fixes
- Scott Zakrajsek's use case: portfolio companies with many brands needing standardized data collection

---

## Product Scope

### Three Core Pillars

| Pillar | What It Does | Competitive Edge |
|---|---|---|
| **Audit** | Scan GA4 properties, GTM containers, and marketing pixels for misconfigurations, missing events, and data quality issues | Free, open source, covers GA4 + GTM + pixels in one tool (competitors do one or two) |
| **Remediate** | Generate copy-paste dataLayer code, GTM container JSON imports, CAPI endpoint boilerplate, and developer handoff docs | Nobody else does this — this is the moat |
| **Discover** | AI vision scans pages to identify interactive elements, suggest events to track, and map against existing tracking | AI-native approach that didn't exist before Gemini/Claude vision models |

### What's In / What's Out

| In Scope | Out of Scope (for now) |
|---|---|
| GA4 property configuration audit | GA4 data quality (sampling, cardinality) |
| GTM container structure audit | GTM server-side container audit |
| Client-side pixel detection (Meta, Google, TikTok, LinkedIn, Pinterest, Snapchat, Twitter/X) | Ad platform API performance data |
| CAPI configuration validation | CAPI event deduplication testing |
| Remediation code generation | Automated code deployment |
| AI page scanning for event suggestions | Full site crawl (limit to provided URLs) |
| Recurring scheduled audits | Real-time monitoring |
| Multi-property portfolio view | Cross-property data comparison |

---

## Architecture

### High-Level Flow

```
┌─────────────┐     ┌──────────────┐     ┌──────────────────┐
│  User Input  │────▶│  Audit Engine │────▶│  Report Generator │
│  - URL       │     │  - GA4 API    │     │  - Score card     │
│  - GA4 ID    │     │  - GTM API    │     │  - Issue list     │
│  - GTM ID    │     │  - Page Scan  │     │  - Remediation    │
│  - API keys  │     │  - Pixel Scan │     │  - Dev handoff    │
└─────────────┘     └──────────────┘     └──────────────────┘
                           │
                    ┌──────┴──────┐
                    │  AI Vision   │
                    │  - Screenshot │
                    │  - Element ID │
                    │  - Event map  │
                    └─────────────┘
```

### Two Runtime Modes

**Mode 1: Browser-Only (No Backend)**
- Page scanning via proxy service or browser extension
- GA4/GTM audit via user-provided OAuth tokens
- All processing client-side
- Export reports as PDF/CSV
- Good for: individual audits, demos, open source distribution

**Mode 2: Full Backend (SaaS)**
- Server-side page scanning via Puppeteer/Playwright
- OAuth token management for recurring audits
- Scheduled audit runs with email alerts
- Multi-property dashboard
- Good for: agencies, portfolio companies, Scott's use case

**Recommendation:** Build Mode 1 first (like the Google Ads Builder), add Mode 2 backend later.

---

## Phase 1: Page Scanner & Tag Detector

**Goal:** Scan any URL and detect every marketing tag/pixel on the page.
**No auth required.** This is the hook — immediate value with zero setup.

### How It Works

1. User enters a URL
2. Backend proxy (or browser extension) fetches the page
3. Scanner analyzes the DOM, network requests, and JavaScript objects for known tag patterns
4. Returns a report of detected tags with configuration details

### Detection Methods

| Method | What It Catches |
|---|---|
| **DOM Script Tags** | GTM container IDs, GA4 measurement IDs, Meta Pixel IDs, TikTok pixel IDs |
| **Global JS Objects** | `window.dataLayer`, `window.google_tag_manager`, `window.fbq`, `window.ttq`, `window._linkedin_data_partner_ids` |
| **Network Requests** | Outbound hits to `google-analytics.com`, `connect.facebook.net`, `analytics.tiktok.com`, `snap.licdn.com`, `googleads.g.doubleclick.net` |
| **Meta Tags** | `facebook-domain-verification`, `google-site-verification` |
| **Cookie Inspection** | `_ga`, `_gid`, `_fbp`, `_fbc`, `_ttp`, `_gcl_aw`, `_uetmsclkid` |
| **Consent Detection** | Cookie banner presence, Google Consent Mode v2 signals, TCF 2.0 |

### Tag Detection Signatures

```javascript
const TAG_SIGNATURES = {
  ga4: {
    scriptPatterns: [/gtag\/js\?id=G-[A-Z0-9]+/, /googletagmanager\.com\/gtag/],
    globalObjects: ['gtag', 'google_tag_data'],
    networkPatterns: [/google-analytics\.com\/g\/collect/, /analytics\.google\.com/],
    cookiePatterns: ['_ga', '_ga_*', '_gid'],
    extractId: (html) => html.match(/G-[A-Z0-9]{6,12}/g)
  },
  gtm: {
    scriptPatterns: [/googletagmanager\.com\/gtm\.js\?id=GTM-[A-Z0-9]+/],
    globalObjects: ['google_tag_manager', 'dataLayer'],
    extractId: (html) => html.match(/GTM-[A-Z0-9]{6,8}/g)
  },
  meta_pixel: {
    scriptPatterns: [/connect\.facebook\.net\/.*\/fbevents\.js/],
    globalObjects: ['fbq', '_fbq'],
    networkPatterns: [/facebook\.com\/tr/],
    cookiePatterns: ['_fbp', '_fbc'],
    extractId: (html) => html.match(/fbq\('init',\s*'(\d+)'\)/)?.[1]
  },
  tiktok_pixel: {
    scriptPatterns: [/analytics\.tiktok\.com\/i18n\/pixel\/events\.js/],
    globalObjects: ['ttq'],
    networkPatterns: [/analytics\.tiktok\.com/],
    cookiePatterns: ['_ttp'],
    extractId: (html) => html.match(/ttq\.load\('([A-Z0-9]+)'\)/)?.[1]
  },
  google_ads: {
    scriptPatterns: [/gtag\/js\?id=AW-\d+/],
    networkPatterns: [/googleads\.g\.doubleclick\.net/],
    cookiePatterns: ['_gcl_aw', '_gcl_dc'],
    extractId: (html) => html.match(/AW-\d+/g)
  },
  linkedin_insight: {
    scriptPatterns: [/snap\.licdn\.com\/li\.lms-analytics/],
    globalObjects: ['_linkedin_data_partner_ids', 'lintrk'],
    extractId: (html) => html.match(/_linkedin_data_partner_ids\s*=\s*\[(\d+)\]/)?.[1]
  },
  pinterest_tag: {
    scriptPatterns: [/pintrk/],
    globalObjects: ['pintrk'],
    extractId: (html) => html.match(/pintrk\('load',\s*'(\d+)'\)/)?.[1]
  },
  snapchat_pixel: {
    scriptPatterns: [/sc-static\.net\/scevent\.min\.js/],
    globalObjects: ['snaptr'],
    extractId: (html) => html.match(/snaptr\('init',\s*'([a-f0-9-]+)'\)/)?.[1]
  },
  twitter_pixel: {
    scriptPatterns: [/static\.ads-twitter\.com\/uwt\.js/],
    globalObjects: ['twq'],
    extractId: (html) => html.match(/twq\('init',\s*'([a-z0-9]+)'\)/)?.[1]
  },
  microsoft_uet: {
    scriptPatterns: [/bat\.bing\.com\/bat\.js/],
    globalObjects: ['UET'],
    cookiePatterns: ['_uetmsclkid'],
    extractId: (html) => html.match(/uetq.*?ti:\s*"(\d+)"/)?.[1]
  }
};
```

### Phase 1 Deliverable

A single-page HTML tool (like the Google Ads Builder):
- Input: URL
- Output: Tag inventory card showing every detected pixel/tag with ID, load method, consent status
- Score: Tag health score (0–100) based on completeness and configuration
- Export: JSON/CSV of detected tags

---

## Phase 2: GA4 Property Audit

**Goal:** Connect to GA4 Admin API and run a comprehensive property health check.
**Requires:** Google OAuth with `analytics.readonly` scope.

### GA4 Admin API Endpoints Used

| Endpoint | What We Audit |
|---|---|
| `listAccountSummaries` | Account structure, property count |
| `getProperty` | Timezone, currency, industry category |
| `listDataStreams` | Web/iOS/Android streams, measurement IDs |
| `getEnhancedMeasurementSettings` | Scroll, outbound clicks, file downloads, video engagement, site search |
| `listKeyEvents` | Conversion/key event configuration |
| `getDataRetentionSettings` | 2-month vs 14-month retention |
| `getGoogleSignalsSettings` | Google Signals enabled/disabled |
| `listCustomDimensions` | Custom dimension setup |
| `listCustomMetrics` | Custom metric setup |
| `listGoogleAdsLinks` | Google Ads linking |
| `listSearchAds360Links` | SA360 linking |
| `listDisplayVideo360AdvertiserLinks` | DV360 linking |
| `listFirebaseLinks` | Firebase linking |
| `getAttributionSettings` | Attribution model, lookback windows |
| `listAudiences` | Audience definitions |
| `getDataSharingSettings` | Data sharing configuration |

### GA4 Audit Checks (40-Point Checklist)

**Property Configuration (10 checks)**

| # | Check | Severity | What We Look For |
|---|---|---|---|
| 1 | Timezone set correctly | Medium | Timezone matches business location, not default |
| 2 | Currency configured | Medium | Currency matches business reporting currency |
| 3 | Industry category set | Low | Industry selected (affects benchmarks) |
| 4 | Data retention = 14 months | High | Default 2-month is almost always wrong |
| 5 | Google Signals enabled | Medium | Required for cross-device, demographics |
| 6 | Data collection acknowledged | Low | User data collection consent acknowledged |
| 7 | Reporting attribution model | Medium | Data-driven vs last-click vs other |
| 8 | Attribution lookback windows | Medium | Appropriate for business (30 vs 90 day) |
| 9 | Data filters configured | Medium | Internal/dev traffic filtered |
| 10 | Property change history reviewed | Low | Recent unexpected changes flagged |

**Data Streams (6 checks)**

| # | Check | Severity | What We Look For |
|---|---|---|---|
| 11 | Web stream exists | Critical | At least one web data stream |
| 12 | Measurement ID valid | Critical | G-XXXXXXX format, actively collecting |
| 13 | Enhanced measurement ON | High | Master toggle enabled |
| 14 | Page views tracking | Critical | Should always be on |
| 15 | Scroll tracking enabled | Medium | 90% scroll depth |
| 16 | Outbound click tracking | Medium | External link tracking |

**Enhanced Measurement (5 checks)**

| # | Check | Severity | What We Look For |
|---|---|---|---|
| 17 | Site search enabled | Medium | Query parameter configured correctly |
| 18 | Video engagement tracking | Medium | YouTube embeds tracked |
| 19 | File download tracking | Medium | PDF/XLSX/etc downloads |
| 20 | Form interaction tracking | High | Form start/submit events |
| 21 | Search term parameter | Medium | Correct query param (q, s, search, etc.) |

**Key Events / Conversions (5 checks)**

| # | Check | Severity | What We Look For |
|---|---|---|---|
| 22 | At least 1 key event defined | Critical | Must have conversions set up |
| 23 | Industry-appropriate events | High | Ecommerce: purchase, add_to_cart; Lead gen: generate_lead, form_submit |
| 24 | No duplicate conversions | Medium | Same event not marked twice |
| 25 | Event naming conventions | Medium | snake_case, no spaces, descriptive |
| 26 | Counting method correct | Medium | Once per session vs every event |

**Product Linking (6 checks)**

| # | Check | Severity | What We Look For |
|---|---|---|---|
| 27 | Google Ads linked | High | If running Google Ads, must be linked |
| 28 | Search Console linked | High | Should always be linked for SEO data |
| 29 | BigQuery export enabled | Medium | Recommended for any serious analytics |
| 30 | DV360 linked (if applicable) | Medium | Programmatic advertisers |
| 31 | SA360 linked (if applicable) | Medium | Enterprise search advertisers |
| 32 | Firebase linked (if applicable) | Medium | App developers |

**Custom Definitions (4 checks)**

| # | Check | Severity | What We Look For |
|---|---|---|---|
| 33 | Custom dimensions defined | Medium | At least user-scoped dimensions for segmentation |
| 34 | Dimension naming conventions | Low | Descriptive names, no duplicates |
| 35 | Custom metrics defined | Low | Business-specific metrics |
| 36 | No orphaned definitions | Low | Definitions that aren't being populated |

**Audiences & Advanced (4 checks)**

| # | Check | Severity | What We Look For |
|---|---|---|---|
| 37 | Audiences created | Medium | At least purchasers, engaged users |
| 38 | Audience triggers configured | Medium | Auto-event on audience membership |
| 39 | User access audit | High | No unauthorized users |
| 40 | Data sharing settings appropriate | Medium | Business needs vs privacy |

### Scoring

Each check receives: Pass (✅), Warning (⚠️), Fail (❌), or N/A.

**Score calculation:**
- Critical fail = -15 points
- High fail = -10 points
- Medium fail = -5 points
- Low fail = -2 points
- Start at 100, subtract penalties, floor at 0

**Grade scale:**
- 90–100: A (Excellent)
- 80–89: B (Good)
- 70–79: C (Needs Attention)
- 60–69: D (Significant Issues)
- Below 60: F (Critical — data likely unreliable)

---

## Phase 3: Pixel & CAPI Audit

**Goal:** Validate marketing pixel implementations and server-side event quality.

### Client-Side Pixel Checks

| # | Platform | What We Check |
|---|---|---|
| 1 | **Meta Pixel** | Pixel ID present, base code fires, PageView event, standard events (ViewContent, AddToCart, Purchase), Advanced Matching parameters (em, ph, fn, ln, external_id) |
| 2 | **Google Ads** | Conversion tag present, conversion ID format, remarketing tag, enhanced conversions config |
| 3 | **TikTok Pixel** | Pixel ID, base code, standard events, Advanced Matching |
| 4 | **LinkedIn Insight** | Partner ID, conversion tracking config |
| 5 | **Pinterest Tag** | Tag ID, enhanced match, conversion events |
| 6 | **Snapchat Pixel** | Pixel ID, standard events |
| 7 | **Twitter/X Pixel** | Pixel ID, conversion events |
| 8 | **Microsoft UET** | Tag ID, event goals |

### CAPI Validation

For each platform that supports server-side events:

| Check | Description |
|---|---|
| CAPI endpoint active | Server-side events being sent (detected via API or user-provided access token) |
| Event match quality | Meta: Event Match Quality score via Marketing API |
| Dedup parameter present | `event_id` or `eventID` parameter for client+server dedup |
| Required parameters | `user_data.em`, `user_data.ph`, `client_ip_address`, `client_user_agent` |
| Event coverage | Which events are server-side vs client-only |
| Consent mode integration | Consent signals forwarded to CAPI |

### Pixel Health Score

Per platform:
- Tag present and firing: +30
- Standard events configured: +20
- Advanced Matching / Enhanced Conversions: +20
- CAPI configured: +15
- Dedup working: +10
- Consent integration: +5

---

## Phase 4: Remediation Engine

**Goal:** For every issue found, generate the exact code to fix it.

**This is the moat.** Nobody else does this.

### Remediation Types

| Issue Category | Generated Fix |
|---|---|
| **Missing dataLayer events** | Copy-paste `dataLayer.push()` code with correct event name, parameters, and placement instructions |
| **Missing GTM tags** | Exportable GTM container JSON with preconfigured tags, triggers, and variables |
| **Missing pixels** | Platform-specific base code + event code snippets |
| **GA4 config issues** | Step-by-step instructions with direct links to the correct GA4 admin panel page |
| **CAPI setup** | Boilerplate server endpoint code (Node.js, Python, PHP) for each platform |
| **Enhanced conversions** | Google Ads enhanced conversion code snippets |
| **Consent mode** | Google Consent Mode v2 implementation code |

### Example: Missing `purchase` Event

**Audit finding:** GA4 property is ecommerce but no `purchase` key event configured. No `purchase` event detected in dataLayer.

**Generated remediation:**

```javascript
// === REMEDIATION: Add purchase event to dataLayer ===
// Place this code on your order confirmation / thank you page
// Replace dynamic values with your template engine variables

dataLayer.push({
  event: 'purchase',
  ecommerce: {
    transaction_id: '{{ORDER_ID}}',          // Required: unique order ID
    value: {{ORDER_TOTAL}},                   // Required: total order value
    tax: {{TAX_AMOUNT}},                      // Recommended
    shipping: {{SHIPPING_COST}},              // Recommended
    currency: 'USD',                          // Required: ISO 4217
    coupon: '{{COUPON_CODE}}',                // Optional
    items: [
      // Repeat for each item in the order
      {
        item_id: '{{PRODUCT_SKU}}',           // Required
        item_name: '{{PRODUCT_NAME}}',        // Required
        price: {{PRODUCT_PRICE}},             // Recommended
        quantity: {{QUANTITY}},               // Recommended
        item_category: '{{CATEGORY}}',        // Recommended
        item_brand: '{{BRAND}}'               // Recommended
      }
    ]
  }
});
```

**Plus a GTM container JSON fragment:**

```json
{
  "tag": {
    "name": "GA4 - purchase Event",
    "type": "gaawe",
    "parameter": [
      { "key": "eventName", "value": "purchase" },
      { "key": "measurementId", "value": "{{GA4 Measurement ID}}" }
    ],
    "firingTriggerId": ["purchase_trigger"]
  },
  "trigger": {
    "name": "purchase - dataLayer",
    "type": "customEvent",
    "customEventFilter": [
      { "parameter": [{ "key": "arg0", "value": "purchase" }] }
    ]
  }
}
```

### Developer Handoff Document

For each audit, generate a developer-facing document:
- Priority-ordered fix list (Critical → Low)
- Exact code snippets for each fix
- File/page placement instructions ("Add this to your checkout confirmation template")
- GTM container JSON ready to import
- Testing instructions ("After deploying, verify in GTM Preview Mode that...")
- Estimated implementation time per fix

---

## Phase 5: AI Page Vision

**Goal:** Use AI vision models to analyze page screenshots and suggest tracking events.

### How It Works

1. Capture full-page screenshot of the URL
2. Send to Gemini Vision / Claude Vision with a structured prompt
3. AI identifies interactive elements and suggests events
4. Map suggestions against existing tracking (from Phase 1 scan)
5. Generate remediation code for gaps

### AI Vision Prompt Structure

```
Analyze this webpage screenshot. You are a marketing analytics expert.

Identify every interactive element that should be tracked for marketing analytics:

1. FORMS: Contact forms, signup forms, newsletter opt-ins, search boxes
2. CTAs: Buttons, links that are calls-to-action (Buy Now, Get Started, etc.)
3. NAVIGATION: Menu items, category links, filter controls
4. ECOMMERCE: Product cards, add-to-cart buttons, wishlist, cart icon
5. ENGAGEMENT: Video players, image carousels, tabs, accordions, chat widgets
6. CONVERSION: Phone numbers, email links, booking/scheduling widgets

For each element, provide:
- Element type and location on page
- Suggested GA4 event name (using standard naming conventions)
- Event parameters to capture
- Priority (Critical, High, Medium, Low)
- Whether this is likely already tracked by Enhanced Measurement
```

### Event Mapping Output

| Element | Suggested Event | Parameters | Priority | Already Tracked? |
|---|---|---|---|---|
| Hero CTA "Get Started" | `generate_lead` | `button_text`, `page_location` | Critical | No |
| Newsletter signup form | `sign_up` | `method: email` | High | Partial (form_start only) |
| Video player | `video_start`, `video_progress`, `video_complete` | `video_title`, `video_provider` | Medium | Yes (Enhanced Measurement) |
| Product cards | `view_item` | `item_id`, `item_name`, `price` | Critical | No |

---

## Phase 6: Multi-Site Portfolio

**Goal:** Scott's use case — standardize data collection across multiple sites/brands.

### Portfolio Features

- Add multiple sites to a portfolio
- Run audits across all sites simultaneously
- Compare tracking coverage side-by-side
- Define a "standard schema" (required events, naming conventions, parameters)
- Flag deviations from the standard per site
- Generate unified remediation across all sites
- Track fix implementation progress per site

### Schema Standardization

Define a portfolio-level schema:

```json
{
  "portfolio_name": "Power Digital - Portfolio Companies",
  "required_events": {
    "all_sites": ["page_view", "scroll", "click", "form_submit", "generate_lead"],
    "ecommerce": ["view_item", "add_to_cart", "begin_checkout", "purchase"],
    "lead_gen": ["generate_lead", "contact_form_submit", "phone_call"]
  },
  "required_parameters": {
    "all_events": ["page_location", "page_title"],
    "ecommerce_events": ["currency", "value", "items"],
    "lead_events": ["lead_source", "form_name"]
  },
  "naming_conventions": {
    "style": "snake_case",
    "prefix": "",
    "max_length": 40
  },
  "required_pixels": ["ga4", "meta_pixel", "google_ads"],
  "required_capi": ["meta", "google"]
}
```

---

## Tech Stack

### Phase 1 (Browser-Only MVP)

| Component | Technology | Why |
|---|---|---|
| Frontend | Vanilla JS + HTML (single file, like Google Ads Builder) | No build step, instant deployment |
| Page scanning | Proxy via Cloudflare Worker or Supabase Edge Function | Avoids CORS, serverless, free tier |
| Pixel detection | JavaScript pattern matching on fetched HTML + headers | Client-side, fast |
| Report generation | Client-side HTML/PDF/CSV export | No backend needed |
| AI Vision | Gemini Vision API (user's key) | Best screenshot analysis |
| Styling | Inline CSS (dark theme matching googleadsagent.ai) | Consistent with ecosystem |

### Phase 2+ (Full Backend)

| Component | Technology | Why |
|---|---|---|
| Page scanning | Playwright on Supabase Edge Functions or Cloudflare Workers | Full JS rendering, network interception |
| GA4 API | Google Analytics Admin API v1 | Official API, comprehensive coverage |
| GTM API | Google Tag Manager API v2 | Container and tag inspection |
| Pixel APIs | Meta Marketing API, Google Ads API | CAPI validation, event match quality |
| Scheduling | Supabase cron or Cloudflare Cron Triggers | Recurring audits |
| Storage | Supabase (Postgres) | Audit history, portfolio management |
| Auth | Google OAuth (same as Asset Validator) | Reuse existing auth pattern |

---

## API Requirements

### Google APIs Needed

| API | Scope | What It Provides |
|---|---|---|
| GA4 Admin API | `analytics.readonly` | Property settings, data streams, events, linking |
| GA4 Data API | `analytics.readonly` | Event counts, user counts (to verify tracking is working) |
| GTM API | `tagmanager.readonly` | Container structure, tags, triggers, variables |
| Google Ads API | `adwords` | Conversion tracking config, enhanced conversions |

### Third-Party APIs

| API | What It Provides |
|---|---|
| Meta Marketing API | Pixel diagnostics, event match quality, CAPI status |
| TikTok Marketing API | Pixel status, event match quality |

### No API Required (Page Scan Only)

Phase 1 needs zero API keys from the user — it works purely by fetching and analyzing the page HTML/JS. This is the zero-friction entry point.

---

## File Structure

### Phase 1 (MVP)

```
analytics-auditor/
├── index.html          — Landing page (product overview)
├── app.html            — The auditor application
├── docs.html           — Documentation
└── proxy/
    └── scan.js         — Cloudflare Worker / Supabase Edge Function for page fetching
```

### Full Product

```
analytics-auditor/
├── index.html
├── app.html
├── docs.html
├── auditor-core.js         — Tag detection engine + scoring
├── ga4-audit.js            — GA4 Admin API audit checks
├── gtm-audit.js            — GTM API audit checks
├── pixel-audit.js          — Pixel detection and validation
├── remediation-engine.js   — Code generation for fixes
├── ai-vision.js            — AI page scanning
├── portfolio.js            — Multi-site management
├── report-generator.js     — PDF/CSV/dev-handoff export
├── auditor.css             — Styling
└── proxy/
    ├── scan.js             — Page fetching proxy
    └── screenshot.js       — Screenshot capture for AI vision
```

---

## Competitive Landscape

| Tool | GA4 Audit | GTM Audit | Pixel Detection | CAPI Audit | Remediation Code | AI Vision | Price |
|---|---|---|---|---|---|---|---|
| **ObservePoint** | ❌ | ✅ | ✅ | ❌ | ❌ | ❌ | $$$$ |
| **DataTrue** | ❌ | ✅ | ✅ | ❌ | ❌ | ❌ | $$$ |
| **Adswerve GA4 Auditor** | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | Free (Sheets) |
| **Screaming Frog** | ❌ | ❌ | Partial | ❌ | ❌ | ❌ | $$ |
| **Tag Inspector** | ❌ | ❌ | ✅ | ❌ | ❌ | ❌ | $$$ |
| **Trackingplan** | Partial | ❌ | ✅ | ❌ | ❌ | ❌ | $$$ |
| **OWOX BI** | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | $$ |
| **This Tool** | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | **Free** |

**Our edge:** The only tool that combines all six capabilities. And the remediation engine is something no one has.

---

## Open Source Strategy

Same playbook as the Google Ads API Agent:

1. **Build in public** — Share progress on LinkedIn, tag Scott's post
2. **Ship Phase 1 fast** — Page scanner with zero auth is the hook
3. **Open source the core** — Detection engine, audit checks, remediation templates
4. **Community contribution** — Invite people to add platform detection signatures and remediation templates
5. **Enterprise upsell** — Portfolio management, scheduled audits, team features can be premium later
6. **Conference material** — This becomes INBOUND 2026 content if selected

---

## Build Order

| Phase | What | Timeline | Dependencies |
|---|---|---|---|
| **1** | Page Scanner + Tag Detector | Week 1 | Proxy function only |
| **2** | GA4 Property Audit (40-point) | Week 2–3 | Google OAuth + Admin API |
| **3** | Pixel & CAPI Audit | Week 3–4 | Meta/TikTok API access |
| **4** | Remediation Engine | Week 4–5 | Phases 1–3 complete |
| **5** | AI Page Vision | Week 5–6 | Gemini Vision API |
| **6** | Multi-Site Portfolio | Week 7–8 | Supabase backend |

**Phase 1 is the LinkedIn post.** Ship it, tag Scott, show the page scan working on his company's site. Then iterate.

---

*Architecture v1.0 — February 14, 2026*
*It All Started With A Idea*
