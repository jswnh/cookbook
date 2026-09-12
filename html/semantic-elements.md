## 1. Overview of Semantic HTML

Semantic HTML tags convey meaning about the content they enclose to both browsers and assistive technologies (like screen readers), rather than just describing appearance.

> [!NOTE]
> Avoid replacing semantic elements with generic `<div>` or `<span>` tags styled with CSS classes. Semantic elements automatically establish landmark regions and accessible names.

---

## 2. Landmark & Sectioning Elements

| Tag | Semantic Role | Description |
| :--- | :--- | :--- |
| **`<header>`** | Banner / Section Head | Introductory content or navigation aids for the page or enclosing section. |
| **`<nav>`** | Navigation | Group of major navigation links. Should include an `aria-label` when multiple nav bars exist. |
| **`<main>`** | Main Content | The dominant, unique content of the `<body>`. There must be only one visible `<main>` element per document. |
| **`<article>`** | Article | Standalone, self-contained composition (e.g., blog post, product card, forum message). |
| **`<section>`** | Generic Section | A thematic grouping of content, typically introduced by a heading (`<h2>`-`<h6>`). |
| **`<aside>`** | Complementary | Content tangentially related to the content around it (e.g., sidebars, callouts, related links). |
| **`<footer>`** | Content Info / Section Foot | Footer containing metadata, author, copyright, or back-to-top links. |
| **`<search>`** | Search Landmark | Container for search inputs, forms, and filters. Standardized in modern HTML living standard. |
| **`<address>`** | Contact Info | Contact details for the author/owner of the enclosing `<article>` or document. |

### Complete Semantic Page Layout Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Modern Blog Post</title>
  </head>
  <body>
    <!-- Site Banner Header -->
    <header>
      <a href="/" class="brand-logo">DevCookbook</a>
      
      <!-- Primary Navigation -->
      <nav aria-label="Main Navigation">
        <ul>
          <li><a href="/">Home</a></li>
          <li><a href="/guides">Guides</a></li>
          <li><a href="/about">About</a></li>
        </ul>
      </nav>

      <!-- Search landmark (Modern HTML element) -->
      <search>
        <form action="/search" method="get">
          <label for="search-input">Search guides:</label>
          <input type="search" id="search-input" name="q" placeholder="Keywords...">
          <button type="submit">Search</button>
        </form>
      </search>
    </header>

    <!-- Dominant Page Content -->
    <main>
      <article>
        <header>
          <hgroup>
            <h1>Understanding Semantic HTML</h1>
            <p>A comprehensive architectural reference for modern web layouts</p>
          </hgroup>
          <p>Published on <time datetime="2026-09-12">September 12, 2026</time></p>
        </header>

        <section>
          <h2>Introduction</h2>
          <p>Semantic tags improve accessibility, SEO, and developer readability.</p>
        </section>

        <section>
          <h2>Core Principles</h2>
          <p>Use structural tags to outline the document flow naturally.</p>
        </section>

        <footer>
          <address>
            Written by <a href="mailto:author@example.com">Alex Turner</a>
          </address>
        </footer>
      </article>

      <!-- Tangentially Related Sidebar -->
      <aside aria-label="Related Topics">
        <h3>Related Articles</h3>
        <ul>
          <li><a href="/css-grid">CSS Grid Layout Guide</a></li>
          <li><a href="/accessibility">WCAG Accessibility Basics</a></li>
        </ul>
      </aside>
    </main>

    <!-- Global Footer -->
    <footer>
      <p>&copy; 2026 DevCookbook. All rights reserved.</p>
    </footer>
  </body>
</html>
```

---

## 3. Heading Elements & Heading Groups

- **`<h1>` to `<h6>`**: Heading levels 1 through 6. Headings represent ranks in a document outline.
- **`<hgroup>`**: Groups a heading with secondary content (like subheadings, alternative titles, or taglines enclosed in `<p>` or `<h2-h6>`).

```html
<hgroup>
  <h1>Design Systems at Scale</h1>
  <p>Building reusable component libraries for modern applications</p>
</hgroup>
```

> [!IMPORTANT]
> Never skip heading levels (e.g., jumping from `<h2>` directly to `<h4>`). Keep heading hierarchy sequential for screen reader navigation.

---

## 4. Content Grouping Elements

| Tag | Purpose | Example |
| :--- | :--- | :--- |
| **`<p>`** | Represents a paragraph of text. | `<p>Text content goes here.</p>` |
| **`<hr>`** | Represents a thematic break or scene shift between paragraphs. | `<hr>` |
| **`<pre>`** | Preserves whitespace and line breaks verbatim (often wraps `<code>`). | `<pre><code>const x = 10;</code></pre>` |
| **`<blockquote>`** | Represents an extended quotation from another source. Accepts `cite` URL. | `<blockquote cite="https://example.com"><p>Quote</p></blockquote>` |
| **`<figure>`** | Encapsulates self-contained media, charts, diagrams, or code blocks. | See example below |
| **`<figcaption>`** | Caption or title describing the contents of its parent `<figure>`. | `<figcaption>Figure 1. Architecture</figcaption>` |
| **`<div>`** | Generic non-semantic container block. Use only for styling or scripting hooks when no semantic tag fits. | `<div class="card-grid">...</div>` |

### Figure with Figcaption Example

```html
<figure>
  <img src="event-loop.svg" alt="Diagram showing the JavaScript call stack, web APIs, and task queue" width="600" height="350">
  <figcaption>Figure 1: High-level overview of the browser event loop concurrency model.</figcaption>
</figure>
```

---

## 5. Lists & Description Lists

### Ordered & Unordered Lists

```html
<!-- Unordered Bulleted List -->
<ul>
  <li>HTML Living Standard</li>
  <li>Modern CSS</li>
  <li>JavaScript ESNext</li>
</ul>

<!-- Ordered Numbered List with configuration attributes -->
<!-- type: "1" (default), "a", "A", "i", "I" -->
<!-- start: Starting index -->
<!-- reversed: Counts downward -->
<ol type="1" start="1" reversed>
  <li>Final verification</li>
  <li>Build artifacts</li>
  <li>Lint codebase</li>
</ol>
```

### Description Lists (`<dl>`, `<dt>`, `<dd>`)

Ideal for key-value pairs, glossaries, metadata lists, and settings.

```html
<dl>
  <dt>HyperText Markup Language (HTML)</dt>
  <dd>The standard markup language used to structure web documents.</dd>

  <dt>Cascading Style Sheets (CSS)</dt>
  <dd>A style sheet language used for describing the presentation of a document.</dd>
</dl>
```
