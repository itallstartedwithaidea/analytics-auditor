# Changelog

All notable changes to the Marketing Analytics Auditor will be documented in this file.

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
