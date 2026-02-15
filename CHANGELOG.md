# Changelog

All notable changes to the Marketing Analytics Auditor will be documented in this file.

## [3.0.0] — 2026-02-15

### Added

- **GTM Container Audit** — New tab connecting to Tag Manager API v2
  - Inspects every tag, trigger, variable, and folder in any container
  - Tag type breakdown (GA4 Event, GA4 Config, Custom HTML, etc.)
  - Detects: paused tags, tags without triggers, naming convention issues
  - Full tag list with firing/blocking trigger counts
  - Container health score with A–F grading
- **GTM Container JSON Export** — Importable fix files for every remediation issue
  - Each missing ecommerce event generates a downloadable GTM container JSON
  - Includes GA4 Event tags, Custom Event triggers, and Data Layer variables
  - "Export GTM Import JSON" button generates combined container for all fixes
  - Drag into GTM Admin → Import Container → Merge → done
- **Social Sharing Audit** — Open Graph and Twitter Card meta tag validation
  - Checks og:title, og:description, og:image, og:type, og:url, og:site_name
  - Checks twitter:card, twitter:title, twitter:image, twitter:site
  - Remediation code for every missing tag
- **SEO Health Audit** — Title, description, canonical, viewport, H1, lang, hreflang, robots
  - Title tag length validation (50-60 chars optimal)
  - Meta description length validation (120-160 chars)
  - H1 count validation (exactly one recommended)
- **Schema.org / Structured Data Detection** — JSON-LD parsing and Microdata detection
  - Parses each JSON-LD block and displays @type, name, URL
  - Supports @graph arrays
  - Remediation code for missing Organization schema
- **Ecommerce Funnel Completeness** — GA4's full 9-stage spec
  - Checks: view_item_list, select_item, view_item, add_to_wishlist, add_to_cart, view_cart, begin_checkout, add_shipping_info, add_payment_info, purchase
  - Each missing stage generates dataLayer code AND GTM Import JSON
  - Priority scaled by funnel position (purchase = critical)
- **Per-Platform Pixel Health Scores** — 0-100 score per detected platform
  - Scoring: Tag present (+30), Standard events (+20), Advanced Matching (+20), CAPI (+15), Dedup (+10), Consent (+5)
  - Visual health bar with color coding per platform
- **Deprecated Tag Detection** — Universal Analytics, legacy Facebook, legacy DoubleClick, legacy conversion.js
- **Duplicate Container Detection** — Flags multiple GTM or GA4 IDs on same page
- **Cross-Domain Tracking Detection** — Detects linker config and lists domains
- **Tag Firing Order Validation** — Checks dataLayer before GTM, GTM in head
- **Performance Signals** — Script counts, render-blocking scripts, lazy loading, page weight
- **Server-Side GTM Detection** — Detects first-party domain proxy patterns
- **Consent Mode v2 Completeness** — Checks all 5 consent types
- **Playwright Deep Scan** — Cloudflare Worker integration for full JS rendering
  - Default endpoint: scan.googleadsagent.ai
  - Core Web Vitals (LCP, CLS, FCP, TTFB)
  - Analytics network request capture (proves tags fire)
  - dataLayer event capture in real-time
  - Console error/warning capture
  - Resource count and transfer sizes
- **Developer Handoff Export** — Priority-ordered, with time estimates and testing instructions
- **Google Ads Remarketing Tag Check** — Detects presence of remarketing vs conversion-only

### Changed

- App.html rebuilt from 1,017 to 1,538 lines
- 5-tab interface (added GTM Audit tab)
- Export bar expanded: GTM Import JSON, Developer Handoff buttons
- Issues sorted by severity (Critical → Low)
- Score calculation uses architecture-defined severity weights (Critical -15, High -10, Medium -5, Low -2)
- Deep Scan button always visible (defaults to scan.googleadsagent.ai)

## [2.0.0] — 2026-02-15

### Added

- **GA4 Property Audit (Phase 2)** — Full 40-point checklist via GA4 Admin API
  - Google OAuth token via OAuth Playground (read-only `analytics.readonly` scope)
  - Property dropdown to select from all accessible GA4 properties
  - Checks: timezone, currency, industry, data retention, attribution, data streams, enhanced measurement (all 8 toggles), key events, Google Ads linking, custom dimensions, naming conventions, audiences
  - Separate GA4 health score with A–F grading
- **AI Page Vision (Phase 5)** — Gemini 2.0 Flash event discovery
  - Free Gemini API key integration via Google AI Studio
  - AI identifies trackable elements: forms, CTAs, ecommerce, navigation, engagement, conversion
  - Returns GA4 event name, parameters, priority, and tracked status for each element
  - Score card showing total elements, priority breakdown, and untracked count
- **4-Tab Interface** — Page Scanner, GA4 Audit, AI Vision, Settings
- **Settings Tab** — View/clear all session keys, session status dashboard, "Clear All" option
- **Multi-proxy fallback** — Now tries 4 CORS proxies (allorigins, corsproxy.io, codetabs, corsproxy.org) with 12-second timeouts instead of just one
- **Expanded remediation code** — New fix templates for `view_item`, `begin_checkout`, `dataLayer` initialization before GTM
- **Session persistence** — All API keys stored in `sessionStorage`, restored on page load within same tab
- **Enhanced error handling** — GA4 API errors show specific messages (401 expired, 403 forbidden, etc.)

### Changed

- App.html rebuilt from 720 lines to 1,017 lines
- Proxy fetch now has multiple fallbacks instead of single proxy
- Landing page Phase 2 label updated to "Live Now"
- Documentation completely rewritten with accurate OAuth Playground instructions, AI Vision docs, and session storage reference

## [1.0.0] — 2026-02-14

### Added

- **Phase 1: Page Scanner** — Zero-auth URL scanning and HTML paste analysis
- **Tag detection** for 10 platforms: GA4, GTM, Meta Pixel, Google Ads, TikTok, LinkedIn, Pinterest, Snapchat, X/Twitter, Microsoft UET
- **Detection methods:** DOM script analysis, JavaScript global object inspection, network pattern matching, cookie signature scanning
- **Consent detection:** Google Consent Mode v2, IAB TCF 2.0, common cookie consent platforms
- **Tag Health Score** — 0–100 scoring with A–F grading based on issue severity
- **Issue identification** with severity levels: Critical, High, Medium, Low
- **Remediation code generation** — Copy-paste `dataLayer.push()` snippets, Meta CAPI boilerplate (Node.js), Enhanced Conversions setup, Consent Mode v2 templates, full pixel installation code
- **Export formats:** JSON, CSV, Remediation TXT, Print/PDF
- **Demo mode** — Simulated ecommerce site scan for testing
- **Landing page** at `index.html` with animated terminal demo
- **Documentation** at `docs.html` with sidebar navigation
- **Full GitHub files:** README, ARCHITECTURE, CONTRIBUTING, CHANGELOG, LICENSE
- **LLM-ready files:** llms.txt and llms-full.txt for AI discoverability
- **SEO:** Schema.org markup, sitemap.xml, robots.txt, semantic HTML, noscript fallback

### Security

- All analysis runs client-side — no data sent to any server
- No API keys required for Phase 1 (page scanner)
- Future API keys stored in sessionStorage only (cleared on tab close)
- No telemetry, no analytics tracking, no cookies from the tool
- Zero third-party scripts (Google Fonts only)

---

*All core phases (1–5) are now live. Ongoing improvements include additional platform validation, expanded remediation templates, and community contributions.*
