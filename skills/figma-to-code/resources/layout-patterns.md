# Layout Patterns

Quick reference for mapping Figma layouts to CSS. Read the screenshot — not the layer tree — to decide which pattern to use.

## Decision tree

```
Is it a full page or large section?
├── Yes → wrap in max-width container:
│         .container { max-width: 1280px; width: 100%; margin: 0 auto; padding: 0 var(--space-6); }
└── No  → skip container, style component directly

Do elements stack top-to-bottom?
├── Yes → flex column
│         display: flex; flex-direction: column; gap: [figma spacing];
└── No  → continue ↓

Do elements sit side by side?
├── Yes, same height, one row → flex row
│         display: flex; align-items: center; gap: [figma spacing];
└── Yes, multiple rows of repeated items → CSS grid
          display: grid; grid-template-columns: repeat([n], 1fr); gap: [row] [col];

Do elements overlap or stack on the Z axis?
└── Yes → position: relative on parent, position: absolute on overlay only
          Use sparingly — badges, tooltips, modals only
```

## Auto Layout → CSS mapping

| Figma Auto Layout | CSS |
|-------------------|-----|
| Direction: Vertical | `flex-direction: column` |
| Direction: Horizontal | `flex-direction: row` |
| Spacing between items | `gap` |
| Padding (all sides) | `padding` |
| Align: Left / Top | `align-items: flex-start` |
| Align: Center | `align-items: center` |
| Align: Right / Bottom | `align-items: flex-end` |
| Justify: Space between | `justify-content: space-between` |
| Item: Fill container | `flex: 1` or `width: 100%` |
| Item: Hug content | no explicit width needed |
| Item: Fixed px | `width: Npx; flex-shrink: 0` |

## Width rules

```css
/* Page container — never hardcode 1440px as a fixed width */
.container { max-width: 1280px; width: 100%; margin: 0 auto; }

/* Fluid element — grows with parent */
.hero-text { width: 100%; }

/* Fixed-size element — avatar, icon, thumbnail */
.avatar { width: 40px; height: 40px; flex-shrink: 0; }

/* Responsive image */
img { max-width: 100%; height: auto; }
```

## Grid vs Flex

Use **Grid** when:
- 2-dimensional layout (rows AND columns matter)
- Items must align across rows (card grid, pricing table)
- Number of columns is fixed or uses `auto-fill`

Use **Flex** when:
- 1-dimensional layout (one axis is primary)
- Items wrap naturally (tag list, nav items)
- Alignment of a single row/column is the goal
