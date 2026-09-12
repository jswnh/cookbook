## 1. Font Properties Reference

| Property | Values | Description |
| :--- | :--- | :--- |
| **`font-family`** | Font names, generic families (`system-ui`, `sans-serif`, `serif`, `monospace`) | Priority list of font family names for the element. |
| **`font-size`** | `rem`, `px`, `clamp()`, `em` | Size of the font. |
| **`font-weight`** | `100`–`900`, `normal` (400), `bold` (700) | Thickness/weight of font glyphs. |
| **`font-style`** | `normal`, `italic`, `oblique` | Slant or italicization of the font. |
| **`font-display`** | `swap`, `fallback`, `optional`, `block` | Loading strategy in `@font-face` to prevent invisible text during load. |
| **`font-optical-sizing`** | `auto`, `none` | Enables dynamic glyph adjustments for small or large sizes. |
| **`font-feature-settings`** | `"tnum" 1`, `"zero" 1`, `"liga" 1` | Low-level OpenType features (e.g. tabular figures, slashed zero). |
| **`font-synthesis`** | `none`, `weight`, `style`, `small-caps` | Controls whether browsers generate synthetic faux bold/italic. |
| **`font-palette`** | `normal`, `light`, `dark`, `@font-palette-values` | Selects color palette for modern multi-color OpenType-SVG / COLRv1 fonts. |
| **`font-variation-settings`**| Four-letter OpenType axis tags | Fine-tuned configuration for variable font axes (e.g. `'wght' 650`). |
| **`font`** | Shorthand | `<style> <weight> <size>/<line-height> <family>` |

### Custom Web Fonts & Variable Fonts with `@font-face`

```css
/* High-performance variable web font declaration */
@font-face {
  font-family: "Geist";
  src: url("/fonts/geist-variable.woff2") format("woff2-variations");
  font-weight: 100 900;
  font-style: normal;
  font-display: swap; /* Renders fallback immediately, then swaps */
}

body {
  font-family: "Geist", system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  font-optical-sizing: auto;
  font-synthesis: none; /* Disables fake bolding and slanting */
}

/* Tabular figures for aligned numeric columns in data tables */
.numeric-data {
  font-feature-settings: "tnum" 1, "zero" 1;
}
```

---

## 2. Text Formatting Properties Reference

| Property | Values | Description |
| :--- | :--- | :--- |
| **`color`** | Color format (`oklch()`, hex, rgb) | Color of text glyphs. |
| **`text-align`** | `start`, `center`, `end`, `justify` | Horizontal alignment of text within its parent box. |
| **`line-height`** | Unitless number (e.g. `1.5`), length, `%` | Height of line boxes (unitless number strongly recommended). |
| **`letter-spacing`**| Length (e.g. `0.05em`, `-0.02em`) | Spacing between characters (tracking). |
| **`word-spacing`** | Length | Spacing between words. |
| **`text-transform`**| `uppercase`, `lowercase`, `capitalize`, `none` | Changes capitalization of text. |
| **`text-indent`** | Length, `%` | Indentation for the first line of a block. |
| **`text-decoration`**| `<line> <color> <style> <thickness>` | Shorthand for text underlines, strikethroughs, and colors. |
| **`text-underline-offset`**| Length (e.g. `4px`, `0.2em`) | Distance between text baseline and underline decoration. |

### Modern Text Wrapping (`text-wrap: balance` & `text-wrap: pretty`)

Modern CSS introduces intelligent algorithmic line-breaking engines:

| Value | Ideal Target | Description |
| :--- | :--- | :--- |
| **`text-wrap: balance`** | Headlines (`<h1>`-`<h6>`) | Evenly distributes word count across lines for aesthetically balanced headlines. |
| **`text-wrap: pretty`** | Body Copy (`<p>`) | Eliminates single-word "orphans" on the last line of paragraphs. |

```css
/* Balances headline titles */
h1, h2, h3 {
  text-wrap: balance;
  line-height: 1.2;
}

/* Prevents awkward orphan words in body text */
p {
  text-wrap: pretty;
  line-height: 1.6;
}
```

---

## 3. White Space, Overflow & Word Breaks

| Property | Values | Description |
| :--- | :--- | :--- |
| **`white-space`** | `normal`, `nowrap`, `pre`, `pre-wrap`, `pre-line`, `break-spaces` | Controls whitespace collapsing and line wrapping. |
| **`overflow-wrap`** (formerly `word-wrap`) | `normal`, `break-word`, `anywhere` | Determines whether long unbreakable words wrap when overflowing the container. |
| **`word-break`** | `normal`, `break-all`, `keep-all` | Specifies whether line breaks appear anywhere within words. |
| **`hyphens`** | `none`, `manual`, `auto` | Controls automatic hyphenation at line breaks (requires `lang` on HTML). |

### Text Truncation with Ellipsis Pattern

```css
/* Single-line truncation */
.truncate-single {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  max-width: 100%;
}

/* Multi-line clamp (modern standards-compliant) */
.truncate-multiline {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3; /* Show maximum 3 lines */
  overflow: hidden;
}
```

---

## 4. Multi-Column Layout

Formats text into newspaper-style editorial columns.

| Property | Values | Description |
| :--- | :--- | :--- |
| **`columns`** | `<width> <count>` | Shorthand for `column-width` and `column-count`. |
| **`column-count`** | Integer | Ideal number of columns. |
| **`column-width`** | Length (e.g. `250px`) | Minimum column width (columns auto-adjust responsively). |
| **`column-gap`** | Length | Gutter between columns. |
| **`column-rule`** | `<width> <style> <color>` | Border dividing adjacent columns. |
| **`column-span`** | `none`, `all` | Allows an element (like an `<h2>`) to span across all columns. |

```css
.article-body {
  columns: 300px 3; /* Spans up to 3 columns, each at least 300px wide */
  column-gap: 2rem;
  column-rule: 1px solid #e2e8f0;
}

.article-body h2 {
  column-span: all;
  margin-block: 1.5rem;
}
```
