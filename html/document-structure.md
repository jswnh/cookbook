## 1. Document Type Declaration & Root Element

Every modern HTML document begins with the DOCTYPE declaration followed by the root `<html>` element.

### Root Elements Reference

| Tag | Category | Description | Key Attributes | Self-Closing |
| :--- | :--- | :--- | :--- | :--- |
| **`<!DOCTYPE html>`** | Document Type | Tells the browser to render in standard HTML5 mode (prevents quirks mode). | N/A | Yes |
| **`<html>`** | Document Root | The top-level container for all other HTML elements on the page. | `lang`, `dir` | No |
| **`<head>`** | Document Metadata | Container for metadata, title, stylesheets, scripts, and resource hints (not rendered directly in the page body). | N/A | No |
| **`<body>`** | Document Body | Container for all visible content rendered on the webpage. | Global attributes | No |

#### Modern Document Boilerplate

```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="color-scheme" content="light dark">
    <title>Modern Web Document</title>
    <link rel="stylesheet" href="styles.css">
  </head>
  <body>
    <main>
      <h1>Hello, World!</h1>
    </main>
  </body>
</html>
```

> [!TIP]
> Always specify the `lang` attribute on the `<html>` tag (e.g., `lang="en"`, `lang="es"`). This is critical for screen readers, search engines, and browser translation tools.

---

## 2. Metadata Elements (`<meta>`)

The `<meta>` tag defines machine-readable information about the HTML document. It is self-closing (void element).

### Essential Meta Tags

| Purpose | Attribute & Value | Example |
| :--- | :--- | :--- |
| **Character Encoding** | `charset="UTF-8"` | `<meta charset="UTF-8">` |
| **Responsive Viewport** | `name="viewport"` | `<meta name="viewport" content="width=device-width, initial-scale=1.0">` |
| **SEO Description** | `name="description"` | `<meta name="description" content="Concise summary of page content.">` |
| **Robots Directives** | `name="robots"` | `<meta name="robots" content="index, follow">` |
| **Author** | `name="author"` | `<meta name="author" content="Jane Doe">` |
| **Theme Color (Browser UI)** | `name="theme-color"` | `<meta name="theme-color" content="#1a1a1a" media="(prefers-color-scheme: dark)">` |
| **Color Scheme Support** | `name="color-scheme"` | `<meta name="color-scheme" content="light dark">` |
| **Referrer Policy** | `name="referrer"` | `<meta name="referrer" content="strict-origin-when-cross-origin">` |

### Open Graph (Social Sharing) Meta Tags

```html
<!-- Open Graph Protocol (Facebook, LinkedIn, Discord, Slack) -->
<meta property="og:type" content="website">
<meta property="og:url" content="https://example.com/page">
<meta property="og:title" content="Modern Web Cookbook">
<meta property="og:description" content="Comprehensive reference for modern HTML & CSS.">
<meta property="og:image" content="https://example.com/assets/og-banner.jpg">
<meta property="og:image:alt" content="Cookbook cover banner">

<!-- Twitter Card Metadata -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Modern Web Cookbook">
<meta name="twitter:description" content="Comprehensive reference for modern HTML & CSS.">
<meta name="twitter:image" content="https://example.com/assets/og-banner.jpg">
```

---

## 3. Resource Links & Hints (`<link>`)

The `<link>` tag connects the current document to external resources like stylesheets, icons, and preloaded assets.

| Rel Value (`rel`) | Purpose | Example |
| :--- | :--- | :--- |
| **`stylesheet`** | Imports an external CSS stylesheet. | `<link rel="stylesheet" href="main.css">` |
| **`icon`** | Defines the browser tab favicon. | `<link rel="icon" type="image/svg+xml" href="/favicon.svg">` |
| **`apple-touch-icon`** | Icon for iOS home screen bookmarks. | `<link rel="apple-touch-icon" href="/apple-touch-icon.png">` |
| **`canonical`** | Specifies the preferred canonical URL for SEO deduplication. | `<link rel="canonical" href="https://example.com/original-page">` |
| **`manifest`** | Links a Web App Manifest file for PWA support. | `<link rel="manifest" href="/manifest.webmanifest">` |
| **`preconnect`** | Initiates early DNS lookup, TCP handshake, and TLS negotiation. | `<link rel="preconnect" href="https://fonts.googleapis.com">` |
| **`dns-prefetch`** | Resolves the domain IP address in advance. | `<link rel="dns-prefetch" href="https://api.example.com">` |
| **`preload`** | Prioritizes downloading critical assets needed immediately. | `<link rel="preload" href="/fonts/inter.woff2" as="font" type="font/woff2" crossorigin>` |
| **`modulepreload`** | Preloads and parses ES JavaScript modules ahead of execution. | `<link rel="modulepreload" href="/scripts/app.js">` |

#### High-Performance Web Font Loading Pattern

```html
<!-- Third-Party Font Loading (Google Fonts) with Preconnect -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap">

<!-- Self-Hosted Critical Font Preload (Prevents FOUT/FOIT) -->
<link rel="preload" href="/fonts/inter-variable.woff2" as="font" type="font/woff2" crossorigin>
```

---

## 4. Scripting & Styles (`<script>`, `<style>`, `<noscript>`)

### Scripts Reference

| Tag & Attribute | Behavior |
| :--- | :--- |
| **`<script src="app.js"></script>`** | Blocks HTML parsing until downloaded and executed. |
| **`<script src="app.js" defer></script>`** | Downloads in parallel without blocking parsing; executes in order once DOM is parsed. |
| **`<script src="app.js" async></script>`** | Downloads asynchronously in parallel; executes immediately as soon as ready (order not guaranteed). |
| **`<script type="module" src="app.js"></script>`** | Executes as an ES module in strict mode; automatically deferred by default. Supports ES `import`/`export`. |
| **`<script type="importmap">`** | Maps module specifiers to bare URLs for native ES module resolution. |
| **`<noscript>`** | Renders fallback markup if JavaScript is disabled in the user agent. |

#### Modern Import Map Example

```html
<script type="importmap">
{
  "imports": {
    "lodash": "https://cdn.jsdelivr.net/npm/lodash-es@4.17.21/lodash.js",
    "utils/": "/scripts/utils/"
  }
}
</script>

<script type="module">
  import { debounce } from "lodash";
  import { logger } from "utils/logger.js";
  
  window.addEventListener("resize", debounce(() => logger("Resized!"), 200));
</script>

<noscript>
  <p>JavaScript is required to use this application fully.</p>
</noscript>
```

---

## 5. Other Head Elements

- **`<title>`**: Sets the title of the document shown on browser tabs, history, and search engine results pages.
- **`<style>`**: Embedded CSS styles applied within the HTML document.
- **`<base>`**: Defines the base URL and target for all relative URLs in the document (e.g., `<base href="https://example.com/subdir/" target="_blank">`). Can appear only once per document.
