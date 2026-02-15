# Changelog

All notable changes to the Marketing Analytics Auditor will be documented in this file.

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

## Planned

### [1.1.0] — Phase 2: GA4 Property Audit
- Google OAuth integration with `analytics.readonly` scope
- 40-point GA4 Admin API checklist
- Property configuration, data streams, enhanced measurement, key events, product linking, custom definitions, audiences
- Separate GA4 property health score

### [1.2.0] — Phase 3: Pixel & CAPI Audit
- Meta Marketing API integration for Event Match Quality
- Cross-platform CAPI validation
- Deduplication parameter checking
- Server-side vs. client-side event coverage comparison

### [1.3.0] — Phase 4: Full Remediation Engine
- GTM container JSON export (importable)
- Developer handoff document generator
- CAPI endpoint boilerplate in Python and PHP
- Platform-specific remediation for all detected platforms

### [1.4.0] — Phase 5: AI Page Vision
- Gemini Vision / Claude Vision integration
- Full-page screenshot analysis
- Interactive element identification and event suggestion
- Gap mapping against existing tracking

### [1.5.0] — Phase 6: Multi-Site Portfolio
- Portfolio management for agencies
- Standardized tracking schema definition
- Cross-site coverage comparison
- Deviation flagging and unified remediation
