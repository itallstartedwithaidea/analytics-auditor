# Marketing Analytics Auditor

**Free, open-source GA4 + GTM + pixel auditor with auto-generated remediation code.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Live Demo](https://img.shields.io/badge/Live-googleadsagent.ai-blue)](https://googleadsagent.ai/tools/auditor/)
[![Docs](https://img.shields.io/badge/Docs-Read-green)](https://googleadsagent.ai/tools/auditor/docs.html)

Scan any URL for marketing tags, run a 40-point GA4 property audit, validate pixel and CAPI configurations, and get the exact copy-paste code to fix every issue found. The tool that does what agencies charge $10K for — then goes one step further.

**Live at:** [googleadsagent.ai/tools/auditor](https://googleadsagent.ai/tools/auditor/)

---

## What makes this different

Every analytics audit tool tells you what's wrong. This one generates the code to fix it.

| Feature | Other Tools | This Tool |
|---|---|---|
| GA4 property audit | Some (Sheets-based) | ✅ 40-point Admin API checklist |
| GTM container audit | Enterprise only ($$$) | ✅ Free, open source |
| Pixel detection | Tag scanners exist | ✅ 10 platforms + consent detection |
| CAPI validation | Platform-specific only | ✅ Cross-platform in one tool |
| **Remediation code** | ❌ Nobody does this | ✅ Copy-paste dataLayer, GTM JSON, CAPI boilerplate |
| **AI page vision** | ❌ Nobody does this | ✅ AI suggests events from screenshots |

---

## Quick start

### Zero-auth page scan (no setup needed)

1. Go to [googleadsagent.ai/tools/auditor/app.html](https://googleadsagent.ai/tools/auditor/app.html)
2. Enter any URL or paste HTML source
3. Get instant tag inventory, health score, and remediation code

### Run locally

```bash
git clone https://github.com/itallstartedwithaidea/analytics-auditor.git
cd analytics-auditor
# Open app.html in your browser — that's it
open app.html
```

No build step. No npm install. No dependencies. It's a single HTML file.

---

## Features

### Phase 1: Page Scanner + Tag Detection ✅ Live

Detects 10 marketing platforms by analyzing DOM script tags, JavaScript global objects, network request patterns, and cookie signatures:

| Platform | Detection | Validation | Remediation |
|---|---|---|---|
| Google Analytics 4 | ✅ | Enhanced Measurement, dataLayer events, ecommerce | ✅ |
| Google Tag Manager | ✅ | dataLayer init, noscript fallback | ✅ |
| Meta Pixel | ✅ | PageView, Advanced Matching, standard events, CAPI | ✅ |
| Google Ads | ✅ | Conversion tracking, Enhanced Conversions | ✅ |
| TikTok Pixel | ✅ | Standard events (AddToCart, Purchase) | ✅ |
| LinkedIn Insight | ✅ | Presence | ✅ |
| Pinterest Tag | ✅ | Presence | Planned |
| Snapchat Pixel | ✅ | Presence | Planned |
| X / Twitter Pixel | ✅ | Presence | Planned |
| Microsoft UET | ✅ | Presence | Planned |

Plus: Google Consent Mode v2, IAB TCF 2.0, and cookie consent banner detection.

### Phase 2: GA4 Property Audit 🔧 In Development

40-point checklist via Google Analytics Admin API:

- **Property Configuration (10 checks):** Timezone, currency, data retention, Google Signals, attribution model, data filters, change history
- **Data Streams (6 checks):** Web stream active, measurement ID valid, enhanced measurement
- **Enhanced Measurement (5 checks):** Site search, video, file downloads, forms
- **Key Events (5 checks):** Conversions configured, industry-appropriate, naming conventions
- **Product Linking (6 checks):** Google Ads, Search Console, BigQuery, DV360, SA360, Firebase
- **Custom Definitions (4 checks):** Dimensions, metrics, naming, orphan detection
- **Audiences & Access (4 checks):** Audiences, triggers, user access, data sharing

### Phase 3: Pixel & CAPI Audit 🔧 Planned

Cross-platform pixel validation and server-side event quality checking.

### Phase 4: Remediation Engine ✅ Live (Phase 1 scope)

For every issue found, generates:
- `dataLayer.push()` code with correct event parameters
- GTM container JSON ready to import
- CAPI endpoint boilerplate (Node.js)
- Google Consent Mode v2 implementation
- Enhanced Conversions setup code
- Full pixel installation snippets

### Phase 5: AI Page Vision 🔧 Planned

Gemini/Claude vision analyzes page screenshots to suggest tracking events.

### Phase 6: Multi-Site Portfolio 🔧 Planned

Standardize tracking across multiple sites/brands with portfolio-level schemas.

---

## Scoring

**Tag Health Score (0–100):**

| Severity | Deduction | Example |
|---|---|---|
| Critical | -15 | No purchase event on ecommerce site |
| High | -10 | Meta Advanced Matching missing |
| Medium | -5 | CAPI not detected |
| Low | -2 | GTM noscript fallback missing |

**Grades:** A (90+), B (80–89), C (70–79), D (60–69), F (<60)

---

## Security & Privacy

- **Zero server-side storage.** No database, no user accounts, no cookies.
- **All analysis runs client-side.** HTML content is never sent to our servers.
- **API keys in sessionStorage only.** Cleared when the tab closes. Never persisted.
- **No telemetry.** No analytics tracking on the tool itself.
- **API calls go direct.** GA4 Admin API, Gemini, etc. are called directly from your browser.
- **Open source.** Every line is auditable.

---

## File structure

```
analytics-auditor/
├── index.html          Landing page with animated demo
├── app.html            The auditor application (Phase 1)
├── docs.html           Full documentation with sidebar nav
├── README.md           This file
├── ARCHITECTURE.md     Technical architecture & build plan
├── CONTRIBUTING.md     How to contribute
├── CHANGELOG.md        Version history
├── LICENSE             MIT License
└── .gitignore
```

---

## Exports

| Format | Contents | Use Case |
|---|---|---|
| JSON | Complete scan results | CI/CD integration, programmatic use |
| CSV | Tag inventory + issues | Spreadsheet analysis, client reports |
| Remediation TXT | All fix code + descriptions | Developer handoff |
| Print / PDF | Full visual report | Client presentations |

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines. The easiest ways to contribute:

1. **Add a platform detection signature** — Follow the pattern in `TAG_DEFS` in app.html
2. **Add an audit check** — Each check is a function that returns pass/warn/fail
3. **Add a remediation template** — Code snippet templates for common fixes
4. **Report bugs** — Open an issue with the URL (or sanitized HTML) that caused the problem

---

## Roadmap

- [x] Phase 1: Page scanner + tag detection + remediation code
- [ ] Phase 2: GA4 Admin API property audit (40-point checklist)
- [ ] Phase 3: Pixel & CAPI validation via platform APIs
- [ ] Phase 4: Full remediation engine (GTM JSON exports, CAPI boilerplate)
- [ ] Phase 5: AI page vision (Gemini/Claude screenshot analysis)
- [ ] Phase 6: Multi-site portfolio management

---

## Part of the Google Ads Agent ecosystem

This tool is part of the [Google Ads Agent](https://googleadsagent.ai) project — an open-source AI agent for Google Ads management.

| Tool | Description |
|---|---|
| [Google Ads API Agent](https://github.com/itallstartedwithaidea/google-ads-api-agent) | 28 Python actions, 6 sub-agents, live Google Ads API access |
| [Google Ads Builder](https://googleadsagent.ai/tools/google-ads-builder/) | AI-powered ad copy generator for RSAs, sitelinks, callouts |
| **Analytics Auditor** (this tool) | GA4/GTM/pixel audit with auto-remediation |

---

## Author

**John Williams** — Senior Paid Media Specialist at [Seer Interactive](https://www.seerinteractive.com). 15+ years managing $48M+ in digital advertising. Built with [Cursor](https://cursor.sh) and Claude.

- Website: [itallstartedwithaidea.com](https://itallstartedwithaidea.com)
- LinkedIn: [johnmichaelwilliams](https://www.linkedin.com/in/johnmichaelwilliams)
- X: [@_johnmwilliams](https://x.com/_johnmwilliams)

---

## License

MIT — see [LICENSE](LICENSE) for details.
