# Tailwind CSS

TODO: Add what is Tailwind CSS

## Cascade Layers

Tailwind organizes its stylesheets into an ordered group of layers. A rule defined in a higher-priority layer always defeats a rule in a lower-priority layer.

They are the four **levels of CSS** that are applied in order:

**Theme --> Base --> Components --> Utilities**

This means that _Utilities_ have the highest precedence among these layers. As described below, this allows to stabilsh defaults in components while still being able to override them with utility classes when needed.

Each layers has a different responsibility:

### Theme

It defines the **design system**. That means it houses the system's **source tokens (variables)**, like colors, spacing, fonts, breakpoints, etc.

For example, defining a color token automatically makes utilities like `bg-*` and `text-*` available.

Declaring these theme variables:

```css
@theme {
  --color-primary: #2563eb;
  --spacing-18: 4.5rem;
  --font-display: Poppins, sans-serif;
}
```

Makes possible to generate utilities like:

```html
<div class="bg-primary p-18 font-display"></div>
```

The Theme layer allows to define the colors, fonts, and design token an application uses.

### Base

This layer contains the **global styles** affecting HTML elements directly. Tailwind already ships with its own Base layer called **Preflight** which is built on top of `modern-normalize`. It resets browser inconsistencies by removing default margins, unstyling headings and lists, making images block-level, and applying other sensible defaults. You can extend it:

```css
@layer base {
  h1 {
    @apply text-4xl font-bold;
  }
}
```

Now every `<h1>` automatically has those styles.

### Components

It allows you to build reusable components: card layouts, buttons, form fields, etc. For example:

```css
@layer components {
  .btn-primary {
    @apply px-4 py-2 bg-brand-primary rounded shadow;
  }
}
```

In contrast with the utilities, their names are typically **semantic names** (`.card`, `.button`, `navbar`, etc.). They describe what the thing is instead of its appearance.

### Utilities

They are helper classes with exactly **one** purpose. Each utility maps to a single CSS declaration (or a very small group). For example:

```css
.p-4 {
  padding: 1rem;
}

.text-center {
  text-align: center;
}

.flex {
  display: flex;
}
```
