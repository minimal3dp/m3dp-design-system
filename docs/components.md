# Components Gallery

Below are component-specific examples with references. Screenshots are stored in `docs/images/`.

## Badges
```hugo
{{< m3dp-badge type="calculator" href="/tools/m3dp-fdm-cost-calculator/" >}}🧮 Try Calculator{{< /m3dp-badge >}}
{{< m3dp-badge type="app" href="https://settings.minimal3dp.com/" >}}🧠 OrcaSlicer Assistant{{< /m3dp-badge >}}
```

Screenshot: `docs/images/badges.png`

## Buttons
```html
<a class="m3dp-btn m3dp-btn--primary">Primary</a>
<a class="m3dp-btn m3dp-btn--secondary">Secondary</a>
<a class="m3dp-btn m3dp-btn--amazon">Amazon</a>
```

Screenshot: `docs/images/buttons.png`

## CTAs
```hugo
{{< m3dp-cta type="youtube" >}}Subscribe for more tutorials!{{< /m3dp-cta >}}
{{< m3dp-cta type="email" >}}Join the newsletter for weekly tips!{{< /m3dp-cta >}}
```

Screenshot: `docs/images/ctas.png`

## Cards
```html
<div class="m3dp-card">
  <h3>Card Title</h3>
  <p>Card description text.</p>
</div>
```

Screenshot: `docs/images/cards.png`

## Affiliate Cards
```html
<div class="affiliate-product-card">
  <div class="product-image"><img src="/images/product.jpg" alt="Product"></div>
  <div class="product-info">
    <h3>Product Name</h3>
    <div class="price">$99.99</div>
    <div class="product-description">Short description...</div>
  </div>
</div>
<a href="#" class="btn btn-amazon">Buy on Amazon</a>
<p class="affiliate-disclosure">As an Amazon Associate, we earn from qualifying purchases.</p>
```

Screenshot: `docs/images/affiliate.png`
