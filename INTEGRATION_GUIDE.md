# Minimal3DP Design System – Integration Guide

**Purpose**: A comprehensive guide for integrating the Minimal3DP Design System into new projects and content workflows. Use this for strategy planning, developer onboarding, and multi-site rollout.

---

## Executive Summary

The Minimal3DP Design System (M3DP DS) is a Hugo module providing:
- **Reusable components**: Badges, buttons, CTAs, cards, and affiliate product displays
- **Design tokens**: Colors, typography, spacing, radii, shadows, breakpoints
- **Analytics integration**: GA4 click tracking for CTAs and badges
- **Responsive defaults**: Mobile-first, accessible component patterns

**Benefits**:
- Consistent brand experience across all Minimal3DP properties
- Faster development cycles with pre-built, tested components
- Centralized updates propagate to all consuming sites via module versioning
- Reduced CSS bloat and maintenance burden

---

## Content Strategy Integration

### Content Types That Benefit from DS Components

| Content Type | Recommended Components | Use Case |
|--------------|----------------------|----------|
| **Landing Pages** | Badges, CTAs, Cards | Drive conversions, highlight tools/apps |
| **Product Reviews** | Affiliate cards, Buttons | Amazon/affiliate links with disclosure |
| **Tutorials/Guides** | Cards, CTAs | Section navigation, newsletter signup prompts |
| **Tool Pages** | Badges, CTAs, Buttons | Calculator/app promotion, action buttons |
| **Blog Posts** | CTAs, Cards | Newsletter capture, related content |

### Content Workflow Checklist

1. **Define component needs** during content planning (badges for CTAs, affiliate cards for reviews)
2. **Use shortcodes** in Markdown for badges/CTAs instead of raw HTML
3. **Track conversions** via built-in GA4 helpers (set `location` parameter consistently)
4. **Review analytics** monthly to optimize badge/CTA placement and types
5. **Update tokens centrally** when brand guidelines evolve (DS repo → all sites)

### Editorial Guidelines

- **Badges**: Use for high-value CTAs (calculators, apps, YouTube). Limit 1-2 per page to maintain impact.
- **CTAs**: Place strategically (intro, mid-content, footer). Match type to content (e.g., `calculator` on tool pages).
- **Affiliate cards**: Always include disclosure. Use for recommended products in reviews.
- **Cards**: Group related links or content blocks. Avoid overuse—reserve for navigation/feature highlights.

---

## Technical Integration

### Prerequisites

- Hugo Extended v0.152.0+
- Go modules enabled (`GO111MODULE=on` or default in Go 1.16+)
- Git (for module fetching)
- SCSS compiler (Hugo Pipes handles this automatically)

### Installation Steps

#### 1. Add the Module to `hugo.toml`

```toml
[module]
  [[module.imports]]
    path = "github.com/minimal3dp/m3dp-design-system"
    [[module.imports.mounts]]
      source = "assets/scss"
      target = "assets/scss/m3dp-design-system"
    [[module.imports.mounts]]
      source = "layouts"
      target = "layouts"
```

**Why mounts?**
- `assets/scss` mount: Allows importing `@import "m3dp-design-system/m3dp-design-system";` in your site's SCSS.
- `layouts` mount: Makes shortcodes (`m3dp-badge.html`, `m3dp-cta.html`, etc.) available in your content.

#### 2. Fetch the Module

```zsh
hugo mod get github.com/minimal3dp/m3dp-design-system@latest
hugo mod tidy
```

To pin a specific version:
```zsh
hugo mod get github.com/minimal3dp/m3dp-design-system@v0.5.1
```

#### 3. Import the SCSS

In your site's main SCSS file (e.g., `assets/scss/_styles_project.scss` for Docsy themes):

```scss
// Import Minimal3DP Design System
@import "m3dp-design-system/m3dp-design-system";

// Your site-specific overrides below
```

**Important**: Import *after* theme base styles if you want DS to override theme defaults, or *before* if you want theme to take precedence.

#### 4. Verify Installation

Build your site and check for DS classes in compiled CSS:

```zsh
hugo --gc --minify
grep -E "\.m3dp-badge|\.m3dp-btn|\.m3dp-cta" public/css/*.css
```

Expected: Multiple matches indicating DS styles are compiled.

#### 5. Test a Component

Add a badge to a test page:

```markdown
---
title: Test Page
---

{{< m3dp-badge type="calculator" href="/test/" location="test_page" >}}
  🧮 Test Badge
{{< /m3dp-badge >}}
```

Run `hugo server -D` and verify the badge renders with correct styling and GA4 tracking.

---

## Project-Specific Integration Examples

### Hugo + Docsy Site (Like minimal3dp.github.io)

**Context**: Docsy theme with `assets/scss/main.scss` importing `_styles_project.scss`.

**Steps**:
1. Add module import and mounts in `hugo.toml` (see above).
2. In `assets/scss/_styles_project.scss`, add:
   ```scss
   @import "m3dp-design-system/m3dp-design-system";
   ```
3. Ensure `layouts/partials/hooks/head-end.html` defines GA4 helpers (`trackCtaClick`, `trackAffiliateClick`) or copy from DS examples.
4. Replace local badge/CTA/affiliate styles with DS shortcodes and classes.
5. Test homepage, tool pages, and blog posts for rendering and analytics.

**Migration Pattern**:
- Before: Local SCSS in `assets/scss/components/_badge.scss`, raw HTML in content.
- After: Import DS SCSS, use `{{< m3dp-badge >}}` shortcode in Markdown.

### Standalone Hugo Site (No Existing Theme)

**Context**: Greenfield project or minimal theme.

**Steps**:
1. Initialize Hugo module: `hugo mod init github.com/yourorg/yoursite`
2. Add DS module in `hugo.toml` with mounts.
3. Create `assets/scss/main.scss`:
   ```scss
   @import "m3dp-design-system/m3dp-design-system";
   // Add site-specific styles here
   ```
4. In `layouts/_default/baseof.html`, include compiled CSS:
   ```html
   {{ $css := resources.Get "scss/main.scss" | toCSS | minify | fingerprint }}
   <link rel="stylesheet" href="{{ $css.Permalink }}">
   ```
5. Add GA4 helpers in `layouts/partials/analytics.html` (copy from DS `examples/` or minimal3dp.github.io).
6. Use DS shortcodes in content Markdown files.

### Next.js or Non-Hugo Projects

**Limitation**: DS is currently Hugo-specific (SCSS + shortcodes).

**Workaround Options**:
1. **Extract SCSS only**: Copy `assets/scss/` from DS repo; compile with your build tool (Webpack, Vite, etc.). Use CSS classes in JSX/templates.
2. **Port components**: Rewrite shortcodes as React/Vue components using DS tokens. Keep token values synced manually or via a shared JSON config.
3. **Future work**: Consider extracting design tokens to JSON (`tokens.json`) for cross-platform use (Hugo, React, Vue, etc.).

**Recommendation**: For non-Hugo projects, start by adopting DS tokens (colors, spacing, typography) in your CSS/SCSS. Port badge/button/CTA HTML patterns manually, referencing DS SCSS for structure.

---

## Version Management

### Semantic Versioning Strategy

DS follows [Semantic Versioning](https://semver.org/):
- **Major (v1.0.0)**: Breaking changes (class renames, removed components, incompatible token changes).
- **Minor (v0.5.0)**: New components, new tokens, backwards-compatible features.
- **Patch (v0.5.1)**: Bug fixes, documentation updates, backwards-compatible tweaks.

### Updating DS in Your Project

```zsh
# Update to latest
hugo mod get -u github.com/minimal3dp/m3dp-design-system

# Update to specific version
hugo mod get github.com/minimal3dp/m3dp-design-system@v0.6.0

# Clean module cache (if experiencing issues)
hugo mod clean
hugo mod get -u
```

**Best Practice**: Pin to a specific minor version in production (e.g., `@v0.5.x`) until you've tested a new major/minor release.

### Changelog Review

Before upgrading:
1. Check [CHANGELOG.md](https://github.com/minimal3dp/m3dp-design-system/blob/main/CHANGELOG.md) for breaking changes.
2. Review [GitHub Releases](https://github.com/minimal3dp/m3dp-design-system/releases) for detailed notes.
3. Test in a staging environment with `hugo server` before deploying.

---

## Component Reference

### Badges

**Purpose**: High-impact CTAs for tools, apps, YouTube, email.

**Usage**:
```hugo
{{< m3dp-badge type="calculator" href="/tools/m3dp-fdm-cost-calculator/" location="home_hero" >}}
  🧮 Try FDM Cost Calculator
{{< /m3dp-badge >}}
```

**Parameters**:
- `type`: `calculator` | `app` | `youtube` | `email` | `support`
- `href`: Target URL (required)
- `location`: GA4 tracking label (e.g., `home_hero`, `blog_post_footer`)

**Customization**:
- Sizes: Add `.m3dp-badge--sm` or `.m3dp-badge--lg` classes via Hugo params (advanced).
- Colors: Override `$m3dp-badge-bg-{type}` in your site's SCSS.

### Buttons

**Purpose**: General-purpose action buttons.

**Usage** (HTML in templates or shortcodes):
```html
<a class="m3dp-btn m3dp-btn--primary" href="#">Primary Action</a>
<button class="m3dp-btn m3dp-btn--secondary m3dp-btn--lg">Large Secondary</button>
```

**Variants**:
- `m3dp-btn--primary`: Blue, high-contrast
- `m3dp-btn--secondary`: Gray, subtle
- `m3dp-btn--amazon`: Orange (for affiliate links)
- `m3dp-btn--youtube`: Red
- `m3dp-btn--success`: Green
- `m3dp-btn--outline`: Transparent with border
- `m3dp-btn--ghost`: No background, text only

**Sizes**: `m3dp-btn--sm`, `m3dp-btn--lg`, `m3dp-btn--xl`

### CTAs

**Purpose**: Inline content CTAs (newsletter, calculator, support).

**Usage**:
```hugo
{{< m3dp-cta type="email" location="blog_post_footer" >}}
  📧 Subscribe for weekly 3D printing tips!
{{< /m3dp-cta >}}
```

**Parameters**:
- `type`: `calculator` | `app` | `youtube` | `email` | `support`
- `location`: GA4 tracking label

**Backwards Compatibility**: `.cta` classes still work (aliased to `.m3dp-cta`).

### Cards

**Purpose**: Group related content or navigation blocks.

**Usage**:
```html
<div class="m3dp-card">
  <h3>Card Title</h3>
  <p>Card content goes here.</p>
  <a class="m3dp-btn m3dp-btn--primary" href="#">Learn More</a>
</div>
```

**Shortcode**:
```hugo
{{< m3dp-card >}}
  <h3>Featured Tool</h3>
  <p>Our FDM cost calculator helps you...</p>
{{< /m3dp-card >}}
```

### Affiliate Product Cards

**Purpose**: Amazon affiliate links with product info and disclosure.

**Usage**:
```html
<div class="affiliate-product-card">
  <div class="product-image">
    <img src="/images/product.jpg" alt="Product Name">
  </div>
  <div class="product-info">
    <h3 class="product-title">Product Name</h3>
    <div class="price">$99.99</div>
    <div class="product-description">Brief description of the product.</div>
  </div>
</div>
<a href="https://amazon.com/..." class="btn btn-amazon" data-location="review_page">Buy on Amazon</a>
<p class="affiliate-disclosure">As an Amazon Associate, we earn from qualifying purchases.</p>
```

**Analytics**: Add `data-location` to track affiliate clicks via global GA4 helpers.

---

## Analytics Integration

### GA4 Helpers Setup

DS components rely on global JavaScript functions for click tracking. Ensure your site's `layouts/partials/hooks/head-end.html` (or equivalent) includes:

```html
<script>
  // GA4 CTA Click Tracking
  function trackCtaClick(ctaType, location) {
    if (typeof gtag !== 'undefined') {
      gtag('event', 'cta_click', {
        cta_type: ctaType,
        location: location
      });
    }
  }

  // GA4 Affiliate Click Tracking
  function trackAffiliateClick(productName, location) {
    if (typeof gtag !== 'undefined') {
      gtag('event', 'affiliate_click', {
        product_name: productName,
        location: location
      });
    }
  }

  // Auto-bind affiliate links
  document.addEventListener('DOMContentLoaded', function() {
    document.querySelectorAll('.btn-amazon').forEach(function(link) {
      link.addEventListener('click', function() {
        var location = this.getAttribute('data-location') || 'unknown';
        var productName = this.closest('.affiliate-product-card')?.querySelector('.product-title')?.textContent || 'unknown';
        trackAffiliateClick(productName, location);
      });
    });
  });
</script>
```

### Tracking Best Practices

- **Consistent `location` values**: Use descriptive, hierarchical labels (e.g., `home_hero`, `blog_post_footer`, `tools_index_grid`).
- **Document location taxonomy**: Maintain a list of standard locations in your content guidelines.
- **Review regularly**: Check GA4 for `cta_click` and `affiliate_click` events to identify high-performing placements.
- **A/B test**: Experiment with badge types, CTA copy, and placement using location parameter to segment results.

---

## Customization and Overrides

### Token Overrides

To customize colors, typography, or spacing without forking the DS:

In your site's `assets/scss/_styles_project.scss` (or equivalent), define overrides *before* importing DS:

```scss
// Override DS tokens
$m3dp-blue-600: #1e40af; // Custom brand blue
$m3dp-space-md: 1.5rem;  // Adjust spacing

// Import DS (will use overridden tokens)
@import "m3dp-design-system/m3dp-design-system";
```

**Note**: Only override tokens defined in DS `_tokens.scss`. Overriding component internals may break on DS updates.

### Component Class Overrides

To tweak a component's appearance:

```scss
@import "m3dp-design-system/m3dp-design-system";

// Override badge hover state
.m3dp-badge:hover {
  transform: scale(1.05);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.15);
}
```

**Best Practice**: Scope overrides to specific pages or sections if possible to minimize global impact.

### Adding New Components Locally

For site-specific components (not suitable for DS):

1. Create `assets/scss/components/_mycomponent.scss` in your site.
2. Import it *after* DS in `_styles_project.scss`:
   ```scss
   @import "m3dp-design-system/m3dp-design-system";
   @import "components/mycomponent";
   ```
3. Use DS tokens in your component for consistency:
   ```scss
   .my-component {
     background: $m3dp-gray-100;
     border-radius: $m3dp-radius-md;
     padding: $m3dp-space-md;
   }
   ```

---

## Troubleshooting

### Module Not Found

**Error**: `module "github.com/minimal3dp/m3dp-design-system" not found`

**Solution**:
1. Ensure `hugo.toml` has correct module import path.
2. Run `hugo mod get github.com/minimal3dp/m3dp-design-system@latest`.
3. Check network connectivity (Hugo fetches modules from GitHub).
4. Try `hugo mod clean && hugo mod get -u`.

### Styles Not Compiling

**Error**: `Error: file "_m3dp-design-system.scss" not found`

**Solution**:
1. Verify module mounts in `hugo.toml` (see Installation Steps).
2. Check import path in your SCSS: `@import "m3dp-design-system/m3dp-design-system";`.
3. Ensure Hugo Extended is installed: `hugo version` (should say "extended").
4. Clear Hugo cache: `hugo mod clean && hugo --gc`.

### Shortcodes Not Rendering

**Error**: Shortcode outputs raw HTML or doesn't render.

**Solution**:
1. Verify `layouts` mount in `hugo.toml` (maps DS layouts to site layouts).
2. Check shortcode syntax: `{{< m3dp-badge ... >}}` (note space before closing `>`).
3. Ensure content file is Markdown (`.md`) and not HTML.
4. Test with `hugo server -D --verbose` to see processing logs.

### GA4 Tracking Not Firing

**Error**: Clicks on badges/CTAs don't appear in GA4.

**Solution**:
1. Verify `layouts/partials/hooks/head-end.html` includes GA4 helper functions (see Analytics Integration).
2. Check browser console for JavaScript errors.
3. Ensure GA4 tracking ID is configured in `hugo.toml` or site config.
4. Test with GA4 DebugView in Google Analytics to see real-time events.
5. Confirm `location` parameter is set in shortcode calls.

### Version Conflicts

**Error**: `SCSS variable undefined` after upgrading DS.

**Solution**:
1. Review CHANGELOG.md for breaking changes.
2. Check if token names changed (e.g., `$m3dp-primary` → `$m3dp-blue-600`).
3. Update your site's SCSS to use new token names.
4. Pin to previous DS version temporarily: `hugo mod get github.com/minimal3dp/m3dp-design-system@v0.5.1`.

---

## Multi-Site Rollout Strategy

### Phase 1: Pilot Site (Completed)

- **Site**: minimal3dp.github.io
- **Status**: ✅ Migrated to DS v0.5.1
- **Components Used**: Badges, CTAs, affiliate cards, buttons
- **Learnings**: Module mounts work well; backwards compatibility aliases reduce migration friction.

### Phase 2: Secondary Sites

For each additional Minimal3DP property:

1. **Audit current components**: Identify custom badges, CTAs, buttons that can be replaced with DS.
2. **Install DS module**: Follow Installation Steps.
3. **Migrate incrementally**: Replace components page-by-page (start with high-traffic pages).
4. **Test analytics**: Verify GA4 tracking works on migrated pages.
5. **Document deviations**: If site needs custom component variants, consider upstreaming to DS.

### Phase 3: Content Hub Standardization

- **Goal**: All Minimal3DP sites use DS for core components.
- **Timeline**: 3-6 months
- **Milestones**:
  - Month 1: Pilot complete (✅)
  - Month 2-3: Migrate 2-3 secondary sites
  - Month 4-5: Train content team on DS shortcodes
  - Month 6: Achieve 80%+ DS adoption across sites

### Governance

- **DS Owner**: Designate a maintainer to triage issues, review PRs, release versions.
- **Change Process**:
  1. Propose changes via GitHub issue in DS repo.
  2. Prototype in consuming site, then upstream to DS if broadly useful.
  3. Release minor version for new components, major version for breaking changes.
- **Communication**: Announce DS updates in team Slack/email with migration notes.

---

## Support and Resources

### Documentation

- **README**: [github.com/minimal3dp/m3dp-design-system](https://github.com/minimal3dp/m3dp-design-system)
- **Detailed Docs**: `DOCUMENTATION.md` in DS repo
- **Usage Examples**: `examples/USAGE.md` in DS repo
- **Changelog**: `CHANGELOG.md` and GitHub Releases

### GitHub Pages Docs

- **URL**: [minimal3dp.github.io/m3dp-design-system](https://minimal3dp.github.io/m3dp-design-system) (pending screenshots)
- **Contents**: Component previews, live examples, interactive token reference

### Contributing

To suggest improvements or report bugs:
1. Open an issue at [github.com/minimal3dp/m3dp-design-system/issues](https://github.com/minimal3dp/m3dp-design-system/issues).
2. Include:
   - **Component/token affected**
   - **Current behavior**
   - **Expected behavior**
   - **Proposed solution** (optional)
3. Maintainer will triage and respond within 1-2 business days.

To contribute code:
1. Fork the DS repo.
2. Create a feature branch (`feature/new-component-name`).
3. Add your component under `assets/scss/components/` and `layouts/shortcodes/`.
4. Update `DOCUMENTATION.md` with usage instructions.
5. Test in a Hugo site (use `examples/` Hugo site in DS repo).
6. Submit a PR with clear description and screenshots (if visual component).

---

## Appendix: Quick Reference

### Common Commands

```zsh
# Install DS in a new Hugo site
hugo mod init github.com/yourorg/yoursite
hugo mod get github.com/minimal3dp/m3dp-design-system@latest

# Update DS to latest version
hugo mod get -u github.com/minimal3dp/m3dp-design-system

# Pin DS to specific version
hugo mod get github.com/minimal3dp/m3dp-design-system@v0.5.1

# Clean module cache
hugo mod clean

# Build site with DS
hugo --gc --minify

# Dev server with drafts
hugo server -D
```

### Module Configuration Template

```toml
# hugo.toml
[module]
  [[module.imports]]
    path = "github.com/minimal3dp/m3dp-design-system"
    [[module.imports.mounts]]
      source = "assets/scss"
      target = "assets/scss/m3dp-design-system"
    [[module.imports.mounts]]
      source = "layouts"
      target = "layouts"
```

### SCSS Import Template

```scss
// assets/scss/_styles_project.scss or main.scss

// Optional: Override DS tokens here
// $m3dp-blue-600: #custom-blue;

// Import Minimal3DP Design System
@import "m3dp-design-system/m3dp-design-system";

// Site-specific styles below
// ...
```

### Shortcode Cheat Sheet

```hugo
{{< m3dp-badge type="calculator" href="/tools/" location="page_hero" >}}Badge Text{{< /m3dp-badge >}}

{{< m3dp-cta type="email" location="footer" >}}CTA Text{{< /m3dp-cta >}}

{{< m3dp-card >}}Card Content{{< /m3dp-card >}}
```

---

## Next Steps

1. **For new projects**: Follow Installation Steps → test a badge → adopt DS components site-wide.
2. **For existing sites**: Audit components → install DS → migrate incrementally → track analytics impact.
3. **For content team**: Review Component Reference → bookmark Shortcode Cheat Sheet → use DS components in all new content.
4. **For maintainers**: Monitor GitHub issues → release patches for bugs → plan quarterly DS reviews for new component needs.

**Questions?** Open an issue at [github.com/minimal3dp/m3dp-design-system/issues](https://github.com/minimal3dp/m3dp-design-system/issues).

---

*Last updated: 2025-11-28 | DS Version: v0.5.1*
