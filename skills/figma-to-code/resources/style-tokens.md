# Style Tokens

How to extract design tokens from Figma and format them for each target stack.

## What to extract vs what to derive

| Property | Source |
|----------|--------|
| Color values | Extract from Figma fills/strokes |
| Border radius | Extract from Figma `cornerRadius` |
| Box shadow | Extract from Figma `effects` |
| Font family, size, weight, line-height | Extract from Figma text styles |
| Padding, gap (from Auto Layout) | Extract from `paddingTop/Right/Bottom/Left`, `itemSpacing` |
| `display`, `flex-direction`, `justify-content` | **Derive from layout intent** |
| `width`, `max-width`, `grid-template-columns` | **Derive from responsive intent** |
| `position`, `top`, `left` | **Derive — almost never copy from Figma** |

---

## CSS custom properties (default output)

```css
/* tokens.css — generated from Figma */
:root {
  /* Colors */
  --color-brand:          #3B82F6;
  --color-brand-hover:    #2563EB;
  --color-text-primary:   #111827;
  --color-text-secondary: #6B7280;
  --color-text-inverse:   #FFFFFF;
  --color-bg:             #FFFFFF;
  --color-bg-subtle:      #F9FAFB;
  --color-border:         #E5E7EB;
  --color-success:        #10B981;
  --color-error:          #EF4444;

  /* Typography */
  --font-sans:    'Inter', system-ui, sans-serif;
  --font-display: 'Sora', sans-serif;

  --text-xs:   0.75rem;   /* 12px */
  --text-sm:   0.875rem;  /* 14px */
  --text-base: 1rem;      /* 16px */
  --text-lg:   1.125rem;  /* 18px */
  --text-xl:   1.25rem;   /* 20px */
  --text-2xl:  1.5rem;    /* 24px */
  --text-3xl:  1.875rem;  /* 30px */
  --text-4xl:  2.25rem;   /* 36px */

  --font-normal:   400;
  --font-medium:   500;
  --font-semibold: 600;
  --font-bold:     700;

  --leading-tight:   1.25;
  --leading-normal:  1.5;
  --leading-relaxed: 1.625;

  /* Spacing */
  --space-1:  4px;
  --space-2:  8px;
  --space-3:  12px;
  --space-4:  16px;
  --space-5:  20px;
  --space-6:  24px;
  --space-8:  32px;
  --space-10: 40px;
  --space-12: 48px;
  --space-16: 64px;
  --space-20: 80px;

  /* Radius */
  --radius-sm:   4px;
  --radius-md:   8px;
  --radius-lg:   12px;
  --radius-xl:   16px;
  --radius-full: 9999px;

  /* Shadows */
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.05);
  --shadow-md: 0 4px 6px -1px rgba(0,0,0,0.1), 0 2px 4px -1px rgba(0,0,0,0.06);
  --shadow-lg: 0 10px 15px -3px rgba(0,0,0,0.1);
}
```

---

## Tailwind config extend (if project uses Tailwind)

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        brand: { DEFAULT: '#3B82F6', hover: '#2563EB' },
        text: { primary: '#111827', secondary: '#6B7280' },
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
        display: ['Sora', 'sans-serif'],
      },
      borderRadius: {
        xl: '12px',
        '2xl': '16px',
      },
      boxShadow: {
        card: '0 4px 6px -1px rgba(0,0,0,0.1), 0 2px 4px -1px rgba(0,0,0,0.06)',
      },
    },
  },
}
```

---

## React Native theme object

```ts
// theme.ts
export const theme = {
  colors: {
    brand:         '#3B82F6',
    brandHover:    '#2563EB',
    textPrimary:   '#111827',
    textSecondary: '#6B7280',
    bg:            '#FFFFFF',
    bgSubtle:      '#F9FAFB',
    border:        '#E5E7EB',
  },
  spacing: { 1:4, 2:8, 3:12, 4:16, 5:20, 6:24, 8:32, 10:40, 12:48 },
  radii:   { sm:4, md:8, lg:12, xl:16, full:9999 },
  fonts:   { sans: 'Inter', display: 'Sora' },
  fontSizes: { xs:12, sm:14, base:16, lg:18, xl:20, '2xl':24, '3xl':30 },
  fontWeights: { normal:400, medium:500, semibold:600, bold:700 },
} as const;
```

---

## Color conversion

Figma returns colors as `{ r, g, b, a }` in 0–1 range. Convert to hex:

```js
const toHex = ({ r, g, b }) =>
  '#' + [r, g, b].map(v => Math.round(v * 255).toString(16).padStart(2, '0')).join('');
// { r: 0.231, g: 0.510, b: 0.965 } → "#3B82F6"
```
