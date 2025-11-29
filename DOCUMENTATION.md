# Minimal3DP Design System – Detailed Documentation

## Contents
- Getting Started
- Design Tokens Reference
- Components Reference
- Shortcodes Reference
- Analytics Integration
- Responsive Guidelines
- Changelog

## Getting Started
1. Add module in `hugo.toml` with mounts (assets/scss → assets/scss/m3dp-design-system, layouts → layouts)
2. Run `hugo mod get github.com/minimal3dp/m3dp-design-system@latest`
3. Import SCSS in your site: `@import "m3dp-design-system/m3dp-design-system";`

## Design Tokens Reference
See `assets/scss/_tokens.scss` for the full list.
- Colors: `$m3dp-gray-*`, `$m3dp-blue-*`, `$m3dp-green-*`, `$m3dp-purple-*`, `$m3dp-red-600`, `$m3dp-orange-500`
- Typography: `$m3dp-font-family`, `$m3dp-font-size-*`, `$m3dp-font-weight-*`, `$m3dp-line-height-*`
- Spacing: `$m3dp-space-*`
- Radius: `$m3dp-radius-*`
- Shadows: `$m3dp-shadow-*`
- Breakpoints: `$m3dp-breakpoint-sm|md|lg`

## Components Reference

### Badge
- Base: `.m3dp-badge`
- Variants: `.m3dp-badge--{calculator|app|youtube|email|support}`
- Sizes: `.m3dp-badge--{sm|lg|xl}`
- Shortcode: `m3dp-badge.html`

### Button
- Base: `.m3dp-btn`
- Variants: `.m3dp-btn--{primary|secondary|amazon|youtube|success|outline|ghost}`
- Sizes: `.m3dp-btn--{sm|lg|xl}`

### CTA
- Types: `.m3dp-cta--{calculator|app|youtube|email|support}`
- Backwards compatibility: `.cta`, `.cta--{type}`, `.cta__*`
- Shortcode: `m3dp-cta.html`

### Card
- Base card container: `.m3dp-card`
- Shortcode: `m3dp-card.html`

### Affiliate
- `.affiliate-product-card`, `.btn-amazon`, `.affiliate-disclosure`

## Shortcodes Reference

### m3dp-badge
```hugo
{{< m3dp-badge type="calculator" href="/tools/m3dp-fdm-cost-calculator/" location="home_index" >}}
  🧮 Try FDM Cost Calculator
{{< /m3dp-badge >}}
```

### m3dp-cta
```hugo
{{< m3dp-cta type="email" location="footer" >}}
  Subscribe for weekly tips!
{{< /m3dp-cta >}}
```

### m3dp-card
```hugo
{{< m3dp-card >}}
  <h3>Card Title</h3>
  <p>Card content goes here.</p>
{{< /m3dp-card >}}
```

## Analytics Integration
- Badge and CTA include GA4 click tracking via global helpers (`trackCtaClick`).
- Ensure `layouts/partials/hooks/head-end.html` in your site provides GA4 helpers.

## Responsive Guidelines
- Grid and components adapt at `$m3dp-breakpoint-md` and `$m3dp-breakpoint-lg`.
- Use Bootstrap container/row/col or utility classes for layout alongside DS components.

## Changelog
- v0.5.1: Added CTA backwards compatibility aliases; Affiliate component added; responsive improvements.
- v0.4.4: Completed tokens/typography/spacing; fixed missing intermediate colors.
- v0.3.0: Introduced badge and button components.
