# Framework Adapters

Output templates for each supported stack. Apply the same DOM structure and tokens — only the syntax changes.

---

## HTML / CSS (no framework)

```html
<!-- section-name.html -->
<section class="features">
  <div class="container">
    <h2 class="features__heading">Section Title</h2>
    <div class="features__grid">

      <article class="feature-card">
        <!-- TODO: export "Icons/Shield" → use inline SVG or icon library -->
        <div class="feature-card__icon" aria-hidden="true">…svg…</div>
        <h3 class="feature-card__title">Card Title</h3>
        <p class="feature-card__body">Description text.</p>
      </article>

    </div>
  </div>
</section>
```

```css
/* features.css */
.features { padding: var(--space-20) 0; }

.features__heading {
  font-size: var(--text-3xl);
  font-weight: var(--font-bold);
  color: var(--color-text-primary);
  margin-bottom: var(--space-12);
}

.features__grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: var(--space-6);
}

.feature-card {
  display: flex;
  flex-direction: column;
  gap: var(--space-4);
  padding: var(--space-6);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  background: var(--color-bg);
}

@media (max-width: 768px) {
  .features__grid { grid-template-columns: 1fr; }
}
```

---

## React + CSS Modules

```tsx
// FeatureCard/index.tsx
import styles from './FeatureCard.module.css';

interface FeatureCardProps {
  icon: React.ReactNode;
  title: string;
  description: string;
  href?: string;
}

export function FeatureCard({ icon, title, description, href }: FeatureCardProps) {
  return (
    <article className={styles.card}>
      <div className={styles.iconWrapper} aria-hidden="true">{icon}</div>
      <h3 className={styles.title}>{title}</h3>
      <p className={styles.body}>{description}</p>
      {href && <a className={styles.link} href={href}>Learn more <span aria-hidden="true">→</span></a>}
    </article>
  );
}
```

```css
/* FeatureCard.module.css */
.card {
  display: flex;
  flex-direction: column;
  gap: var(--space-4);
  padding: var(--space-6);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  background: var(--color-bg);
  transition: box-shadow 0.2s ease;
}
.card:hover { box-shadow: var(--shadow-md); }
.title { font-size: var(--text-lg); font-weight: var(--font-semibold); color: var(--color-text-primary); }
.body  { font-size: var(--text-base); color: var(--color-text-secondary); line-height: var(--leading-relaxed); }
```

---

## React + Tailwind

```tsx
export function FeatureCard({ icon, title, description, href }: FeatureCardProps) {
  return (
    <article className="flex flex-col gap-4 p-6 border border-gray-200 rounded-xl bg-white hover:shadow-md transition-shadow">
      <div className="w-10 h-10 text-blue-500" aria-hidden="true">{icon}</div>
      <h3 className="text-lg font-semibold text-gray-900">{title}</h3>
      <p className="text-base text-gray-500 leading-relaxed">{description}</p>
      {href && <a href={href} className="text-sm font-medium text-blue-600 hover:underline">Learn more →</a>}
    </article>
  );
}
```

---

## Vue 3 SFC

```vue
<!-- FeatureCard.vue -->
<template>
  <article class="feature-card">
    <div class="feature-card__icon" aria-hidden="true">
      <slot name="icon" />
    </div>
    <h3 class="feature-card__title">{{ title }}</h3>
    <p class="feature-card__body">{{ description }}</p>
    <a v-if="href" :href="href" class="feature-card__link">Learn more →</a>
  </article>
</template>

<script setup lang="ts">
defineProps<{ title: string; description: string; href?: string }>();
</script>

<style scoped>
.feature-card {
  display: flex;
  flex-direction: column;
  gap: var(--space-4);
  padding: var(--space-6);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  background: var(--color-bg);
}
</style>
```

---

## React Native

```tsx
// FeatureCard.tsx
import { View, Text, Pressable, StyleSheet } from 'react-native';
import { theme } from '../theme';

interface Props { title: string; description: string; onPress?: () => void; }

export function FeatureCard({ title, description, onPress }: Props) {
  return (
    <Pressable style={({ pressed }) => [s.card, pressed && s.pressed]} onPress={onPress}>
      <Text style={s.title}>{title}</Text>
      <Text style={s.body}>{description}</Text>
    </Pressable>
  );
}

const s = StyleSheet.create({
  card: {
    padding: theme.spacing[6],
    borderRadius: theme.radii.lg,
    backgroundColor: theme.colors.bg,
    borderWidth: 1,
    borderColor: theme.colors.border,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.08,
    shadowRadius: 4,
    elevation: 2,
    gap: theme.spacing[3],
  },
  pressed: { opacity: 0.9 },
  title: { fontSize: theme.fontSizes.lg, fontWeight: '600', color: theme.colors.textPrimary },
  body:  { fontSize: theme.fontSizes.base, color: theme.colors.textSecondary, lineHeight: theme.fontSizes.base * 1.5 },
});
```

---

## Next.js additions

When generating for Next.js, apply these on top of the React adapter:

- Replace `<img>` with `<Image>` from `next/image` (add `width`, `height`, `priority` for above-fold images)
- Add `export const metadata: Metadata = { title, description }` to page files
- Mark interactive components with `'use client'` only when they use hooks/events
- Server components are the default — no annotation needed

---

## WordPress / PHP Theme

When generating for WordPress themes (e.g., Underscores-based themes):

- Output PHP template files (e.g., `template-name.php`) with semantic HTML5 structure
- Use WordPress template tags (`the_title()`, `the_content()`, `get_template_part()`, etc.)
- Wrap content in theme structure (`get_header()`, `get_footer()`)
- Use CSS custom properties for colors, spacing, typography
- Keep components as reusable PHP includes when appropriate

```php
<!-- template-name.php -->
<?php get_header(); ?>

<section class="features">
  <div class="container">
    <h2 class="features__heading"><?php echo esc_html( get_the_title() ); ?></h2>
    <div class="features__grid">

      <article class="feature-card">
        <?php get_template_part( 'template-parts/components/feature-icon' ); ?>
        <h3 class="feature-card__title"><?php echo esc_html( $title ); ?></h3>
        <p class="feature-card__body"><?php echo esc_html( $description ); ?></p>
      </article>

    </div>
  </div>
</section>

<?php get_footer(); ?>
```

```css
/* template-name.css */
.features { padding: var(--space-20) 0; }
.features__heading { font-size: var(--text-3xl); font-weight: var(--font-bold); color: var(--color-text-primary); }
.features__grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: var(--space-6); }
@media (max-width: 768px) { .features__grid { grid-template-columns: 1fr; } }
```
