# Flexbox and Grid

**Flexbox** and **Grid** are CSS tools to organize elements in a web page.

## Flexbox

It is designed for aligning and distributing items in a **single direction** (either horizontally or vertically).

Setting: `display: flex;` makes the parent a **flex container** and all of its direct children **flex items**. The parent controls how the children are positioned.

Every **flex container** has a **_main axis_** and a **_cross axis_**. By default, they have these directions:

```
Main axis  →
Cross axis ↓
```

To align items along the **_main axis_** you use: `justify-content`:

```
+------------------------------------------+
|1 2 3                                     |
+------------------------------------------+
+------------------------------------------+
|              1 2 3                       |
+------------------------------------------+
+------------------------------------------+
|                               1 2 3      |
+------------------------------------------+
```

To align items along the **_cross axis_** you use: `align-items`:

```
+------------------------+
| 1 2 3                  |
|                        |
|                        |
+------------------------+

+------------------------+
|                        |
| 1 2 3                  |
|                        |
+------------------------+

+------------------------+
|                        |
|                        |
| 1 2 3                  |
+------------------------+

```

Thus, if `flex-direction: row`:

```
Main axis  →
Cross axis ↓

justify = horizontal
align = vertical
```

And if `flex-direction: column`:

```
Main axis  ↓
Cross axis →

justify = vertical
align = horizontal
```

### Common Flexbox Properties

| CSS                      | Tailwind    | Purpose                                        |
| ------------------------ | ----------- | ---------------------------------------------- |
| `display: flex`          | `flex`      | Enable Flexbox                                 |
| `flex-direction: row`    | `flex-row`  | Horizontal layout (default)                    |
| `flex-direction: column` | `flex-col`  | Vertical layout                                |
| `justify-content`        | `justify-*` | Align items along the **main axis**            |
| `align-items`            | `items-*`   | Align items along the **cross axis**           |
| `gap`                    | `gap-*`     | Space between items                            |
| `flex-grow`              | `flex-1`    | Allow an item to grow and fill remaining space |

## Grid

Grid is **two-dimensional layout system** used to arrange items in **rows and colums** simultaneously.

Setting: `display: grid;` makes the parent a **grid container** and all of its direct children **grid items** placed into a grid of cells. Items in the grid are filled by the browser automatically.

This HTML:

```html
<div class="grid grid-cols-3">
  <div>1</div>
  <div>2</div>
  <div>3</div>
  <div>4</div>
  <div>5</div>
</div>
```

Produces:

```
+---+---+---+
| 1 | 2 | 3 |
+---+---+---+
| 4 | 5 |   |
+---+---+---+
```

Grid also allows to **span an element across multiple rows and columns** using `grid-column: span n` and `grid-row: span n`

For instance, this HTML:

```html
<div class="grid grid-cols-4 gap-4">
  <header class="col-span-4">Header</header>
  <aside class="col-span-1">Sidebar</aside>
  <main class="col-span-3">Content</main>
</div>
```

Produces:

```
+----------------------------------+
| Header                           |
+---------+------------------------+
| Sidebar | Main Content           |
|         |                        |
+---------+------------------------+
```

### Common Grid Properties

| CSS                     | Tailwind      | Purpose               |
| ----------------------- | ------------- | --------------------- |
| `display: grid`         | `grid`        | Enable Grid           |
| `grid-template-columns` | `grid-cols-*` | Define columns        |
| `grid-template-rows`    | `grid-rows-*` | Define rows           |
| `gap`                   | `gap-*`       | Space between cells   |
| `grid-column: span n`   | `col-span-*`  | Span multiple columns |
| `grid-row: span n`      | `row-span-*`  | Span multiple rows    |

## When to use each?

Use **Flexbox** if you're arraging items:

- Navigation menu
- Header
- Footer
- Button group
- Form fields
- Toolbar
- Card content

Use **Grid** if you're organizing a layout:

- Entire webpage
- Dashboard
- Product catalog
- Image gallery
- Multi-column content
- Admin panel
