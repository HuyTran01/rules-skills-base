# Asset Detection

Rules for classifying Figma nodes so they are handled correctly in code instead of being reproduced as HTML/CSS chaos.

## Classification rules

### Photo or raster image
**Signs:** `type: RECTANGLE` with `fills[0].type: IMAGE`, or a frame whose main visual is a photo

**Output:**
```html
<!-- TODO: export "LayerName" → /public/images/filename.webp @2x -->
<img src="/public/images/filename.webp" alt="describe the image" width="600" height="400" loading="lazy" />
```

---

### Simple icon (inline SVG)
**Signs:** `type: VECTOR` or boolean operation, size < 64 × 64px, represents a single symbol

**Output:** Inline SVG using `currentColor` so it inherits the parent's color
```html
<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 20 20" fill="none" aria-hidden="true">
  <path d="..." stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
</svg>
```

If the icon matches a known library (Heroicons, Lucide, Phosphor), use the library component instead of writing SVG manually.

---

### Complex SVG (logo, illustration, multi-path graphic)
**Signs:** Group or frame containing many paths/shapes that together form one complete visual

**Output:** Export config entry + `<img>` reference
```html
<!-- TODO: export "Logo/Full" → /public/logo.svg @1x -->
<img src="/public/logo.svg" alt="Company Name" width="120" height="40" />
```

---

### Decorative group (background blobs, gradients, patterns)
**Signs:** Group of shapes with no text or interactive content, sits behind other content, layer name often starts with `BG_`, `Decoration_`, `Pattern_`

**Output options (choose one):**

Option A — CSS background (preferred when it's a repeating pattern or simple gradient):
```css
.section {
  background-image: url('/public/images/bg-pattern.svg');
  background-repeat: no-repeat;
  background-position: right top;
}
```

Option B — Pseudo-element (no file needed, works for simple shapes):
```css
.section { position: relative; }
.section::before {
  content: '';
  position: absolute;
  inset: 0;
  background: radial-gradient(circle at 80% 20%, rgba(59,130,246,0.08) 0%, transparent 60%);
  pointer-events: none;
}
```

Option C — Export + placeholder (when decoration is complex and must come from Figma):
```html
<!-- TODO: export "BG_Decoration" → /public/images/bg-decoration.svg -->
<div class="section__bg-decoration" aria-hidden="true"></div>
```

---

### Mixed group (photo + text overlay + shape)
**Signs:** Frame containing both dynamic text content AND background imagery

**Rule:** Keep text in the DOM. Export the background layer only.
```html
<!-- TODO: export "Card/Background" → /public/images/card-bg.webp @2x -->
<div class="promo-card" style="--bg: url('/public/images/card-bg.webp')">
  <h3 class="promo-card__title">Dynamic Title</h3>
  <p class="promo-card__body">Dynamic body text</p>
</div>
```
```css
.promo-card {
  background-image: var(--bg);
  background-size: cover;
}
```

---

## Asset export table format

Output this block at the top of every response that includes assets:

```
ASSET EXPORT CHECKLIST
Export these layers from Figma before handing off to dev:

| Figma layer            | Output path                        | Format | Scale | Notes                  |
|------------------------|------------------------------------|--------|-------|------------------------|
| Hero/Background_Photo  | /public/images/hero.webp           | WebP   | @2x   | Crop to 1440×800       |
| Logo/Full              | /public/logo.svg                   | SVG    | @1x   | Inline if < 2 KB       |
| Team/Avatar_John       | /public/images/avatar-john.webp    | WebP   | @2x   | 80×80 px circle crop   |
| Icons/Arrow_Right      | use Lucide <ArrowRight />          | —      | —     | No export needed       |
```
