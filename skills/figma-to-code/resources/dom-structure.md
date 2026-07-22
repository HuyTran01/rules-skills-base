# DOM Structure

Goal: Build a semantic, clean, and maintainable DOM structure that reflects **content meaning** rather than repeating the Figma node tree hierarchy.

---

## Core principles

### Principle 1: Semantic > Visual
DOM structure represents the meaning of elements, not their position.

```
❌ BAD — Repeating Figma node tree:
<div> → Frame 1440
  <div> → Group 12
    <div> → Rectangle 4
      <div> → Frame 23
        <p>Hello</p>

✅ GOOD — Semantic structure:
<header>
  <div class="container">
    <h1>Hello</h1>
```

---

### Principle 2: Minimize wrapper divs
Only introduce wrapper divs for specific functional needs:
1. Creating layout containers (e.g. flex/grid parent)
2. Applying custom background/border styling rules
3. Controlling clipping/overflow behavior
4. Demarcating a reusable component boundary

Do not add wrappers just because Figma has a Group or Layer.

---

### Principle 3: Consistent naming conventions
Follow project conventions. Default to BEM (Block, Element, Modifier) naming style:
```html
<section class="features">
  <div class="features__header"></div>
  <div class="features__grid">
    <div class="features__card card">
      <div class="card__icon"></div>
      <h3 class="card__title">Card Title</h3>
      <p class="card__desc">Description text.</p>
    </div>
  </div>
</section>
```

---

## Mapping Figma layers to HTML elements

### Page section wrappers
| Figma Layer Name | HTML Element |
|---|---|
| Header / Nav / Navbar | `<header>` |
| Hero / Banner | `<section class="hero">` |
| Main content area | `<main>` |
| Sidebar | `<aside>` |
| Footer | `<footer>` |
| Feature section | `<section class="features">` |
| Modal / Dialog | `<dialog>` or `<div role="dialog">` |

### Interactive elements
| Figma Component | HTML Element | Custom Attributes / Accessibility |
|---|---|---|
| CTA / Rectangle + Text | `<button>` or `<a>` | Use `<button>` for actions, `<a>` for navigation |
| Input field | `<input>` + `<label>` | Always link input to label using `id` and `for` |
| Toggle | `<input type="checkbox">` | Style check box using custom CSS patterns |
| Dropdown | `<select>` or custom | For custom dropdowns, use proper ARIA roles |
| Radio group | `<fieldset>` + `<input type="radio">` | Group inputs with a common `name` attribute |

### Content elements
| Figma Layer | HTML Element |
|---|---|
| Bold heading text | `<h1>` through `<h6>` according to heading hierarchy |
| Paragraph / Body text | `<p>` |
| Short label / Caption | `<span>` or `<label>` |
| Repeating items | `<ul>` + `<li>` |
| Ordered steps | `<ol>` + `<li>` |
| Link text | `<a href="url">` |
| Quote / Testimonial | `<blockquote>` |
| Code block | `<pre>` + `<code>` |

---

## DOM construction workflow

### Step 1: Top-down sectioning
Analyze the layout screenshot and draw a mental map of core sections:
```
[ Header: logo + nav menu + CTA button    ]
[ Hero: headline + subtext + image        ]
[ Features: title + 3-column card grid     ]
[ Testimonials: 2-column testimonial cards ]
[ Footer: sitemap + social links + copy    ]
```

### Step 2: Container pattern wrapping
Most sections require a container wrapper to handle max-width and centering:
```html
<section class="features-section">
  <div class="container">
    <!-- Section content goes here -->
  </div>
</section>
```

### Step 3: Detailed section construction
Inside each section container:
1. Establish proper heading hierarchy (`<h2>` for section heading, `<h3>` for cards).
2. Determine layout system (Flexbox vs Grid).
3. Identify repeating visual lists and map them.

---

## Component boundaries

### When to extract a component
Extract code into a separate reusable component when:
- It appears $\ge$ 2 times with identical layout structure
- It holds internal state (e.g. active tab, opened accordion)
- It has visual variants (e.g. Primary vs Secondary button)
- Its internal complexity justifies encapsulation (e.g. complex card, autocomplete)

### React component example
```tsx
import styles from './FeatureCard.module.css';

interface Props {
  icon: React.ReactNode;
  title: string;
  description: string;
  href?: string;
}

export function FeatureCard({ icon, title, description, href }: Props) {
  return (
    <article className={styles.card}>
      <div className={styles.icon}>{icon}</div>
      <h3 className={styles.title}>{title}</h3>
      <p className={styles.description}>{description}</p>
      {href && <a className={styles.link} href={href}>Learn more</a>}
    </article>
  );
}
```

---

## Accessibility Checklist
- [ ] Headings are ordered sequentially (h1, h2, h3) without skipping levels.
- [ ] Form controls are linked to their corresponding `<label>` elements.
- [ ] Images have descriptive `alt` tags (use `alt=""` for purely decorative elements).
- [ ] Interactive elements can be focused and triggered with a keyboard.
- [ ] Icon-only buttons have an `aria-label` or visually hidden label text.
- [ ] Sufficient contrast exists between text and background colors.
