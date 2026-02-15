# Contributing to Marketing Analytics Auditor

Thank you for your interest in contributing! This project is open source and welcomes contributions of all kinds.

## How to contribute

### 1. Add a platform detection signature

The easiest contribution. Each platform is defined in the `TAG_DEFS` object in `app.html`:

```javascript
TAG_DEFS.your_platform = {
  name: 'Platform Display Name',
  short: 'Short',               // 3-8 chars, used in tag badges
  scripts: [/regex-for-script-src/i],
  globals: ['windowObject'],     // JS globals like window.fbq
  cookies: ['_cookie_name'],     // Optional
  extractId: function(html) {    // Return array of IDs found
    var m = html.match(/your-pattern/);
    return m ? [m[1]] : [];
  },
  checks: function(html, ids) {  // Return array of {s:'pass'|'warn', t:'description'}
    var r = [];
    if (html.match(/some-pattern/)) r.push({s:'pass', t:'Feature detected'});
    else r.push({s:'warn', t:'Feature not found'});
    return r;
  }
};
```

**Platforms we'd love signatures for:**
- Amazon Ads pixel
- Criteo OneTag
- Adobe Analytics
- Hotjar / FullStory / Heap
- HubSpot tracking code
- Segment analytics.js
- Klaviyo tracking
- Reddit Pixel
- Quora Pixel

### 2. Add an audit check

For GA4 property audit checks, each check follows this pattern:

```javascript
{
  id: 'check_id',
  category: 'Property Configuration',
  severity: 'critical' | 'high' | 'medium' | 'low',
  title: 'Human-readable check name',
  check: function(propertyData) {
    // Return { status: 'pass' | 'warn' | 'fail', message: '...' }
  }
}
```

### 3. Add a remediation template

Remediation code is generated per-issue. Templates use `{{PLACEHOLDER}}` syntax for values the implementer needs to replace:

```javascript
{
  sev: 'high',
  title: 'Issue description',
  desc: 'Detailed explanation of why this matters',
  fix: "// Code template with {{PLACEHOLDERS}}\ndataLayer.push({\n  event: '{{EVENT_NAME}}'\n});"
}
```

### 4. Report bugs

Open an issue with:
- The URL scanned (or sanitized HTML if the page is private)
- Expected behavior vs. actual behavior
- Browser and OS version
- Screenshot if applicable

### 5. Improve documentation

The docs at `docs.html` are a single HTML file. Improvements to explanations, examples, or formatting are always welcome.

## Development setup

```bash
git clone https://github.com/itallstartedwithaidea/analytics-auditor.git
cd analytics-auditor
open app.html  # or python -m http.server 8080
```

No build step, no npm, no dependencies. The entire app is in `app.html`.

## Code style

- Vanilla JavaScript (ES5 compatible where possible, ES6 is fine for modern browsers)
- No frameworks, no build tools
- Single-file architecture (all JS inline in HTML)
- Consistent variable naming with existing `TAG_DEFS` pattern
- Comments for non-obvious logic

## Pull request process

1. Fork the repo and create a feature branch
2. Make your changes
3. Test with the demo scan and at least 2 real URLs
4. Update `CHANGELOG.md` if adding features
5. Submit a PR with a clear description of what changed and why

## Questions?

- Open a GitHub issue
- Find me on [LinkedIn](https://www.linkedin.com/in/johnmichaelwilliams)
- Or on [X](https://x.com/_johnmwilliams)
