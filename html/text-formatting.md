## 1. Hyperlinks (`<a>`)

The anchor element creates hyperlinks to web pages, files, email addresses, phone numbers, and page fragments.

### Anchor Attributes

| Attribute | Accepted Values | Purpose | Example |
| :--- | :--- | :--- | :--- |
| **`href`** | URL, path, `#id`, `mailto:`, `tel:` | Target destination URL or fragment identifier. | `<a href="#top">Back to top</a>` |
| **`target`** | `_self` (default), `_blank`, `_parent`, `_top` | Context in which to open the linked document. | `<a href="https://example.com" target="_blank">` |
| **`rel`** | `noopener`, `noreferrer`, `nofollow`, `external` | Relationship between the source and target document. | `<a href="..." rel="noopener noreferrer">` |
| **`download`** | Optional filename string or boolean | Prompts browser to download the target resource rather than navigating to it. | `<a href="report.pdf" download="Q4-Report.pdf">` |

#### Hyperlink Protocols & Security

```html
<!-- External Link with Modern Security Best Practices -->
<a href="https://example.com" target="_blank" rel="noopener noreferrer">
  External Website (opens in new tab)
</a>

<!-- Telephone and Email Links -->
<a href="mailto:support@example.com?subject=Question">Email Support</a>
<a href="tel:+18005550199">Call Support</a>

<!-- Smooth Jump to Page Section (ID Fragment) -->
<a href="#section-pricing">Jump to Pricing</a>
```

> [!TIP]
> Whenever using `target="_blank"`, modern browsers automatically imply `rel="noopener"` to prevent reverse tabnabbing security vulnerabilities. Explicitly including `rel="noopener noreferrer"` remains best practice.

---

## 2. Text Semantics & Emphasis

| Tag | Semantic Meaning | Visual Rendering | When to Use |
| :--- | :--- | :--- | :--- |
| **`<strong>`** | Strong importance, seriousness, or urgency. | Bold | Warnings, crucial warnings, key terms. |
| **`<em>`** | Stress emphasis (changes the sentence meaning when spoken). | Italic | Words that require spoken emphasis. |
| **`<b>`** | Stylistic offset without semantic importance. | Bold | Product names in review, keywords in summary. |
| **`<i>`** | Alternate voice, mood, technical terms, foreign words. | Italic | Scientific taxonomy names, book thoughts. |
| **`<small>`** | Side comments, legal disclaimers, copyrights, caveats. | Smaller font | Terms of service, copyright notices. |
| **`<mark>`** | Highlighted text for reference or search match relevance. | Yellow background | Search term matches in a query result. |
| **`<s>`** | Content that is no longer accurate, valid, or relevant. | Strikethrough | Expired pricing, outdated task items. |

### Editorial Revisions (`<ins>` & `<del>`)

```html
<p>
  The subscription fee is 
  <del datetime="2026-01-01T00:00Z">$29/month</del> 
  <ins datetime="2026-01-01T00:00Z">$19/month</ins>.
</p>
```

---

## 3. Computer Code & Technical Text

| Tag | Meaning | Example |
| :--- | :--- | :--- |
| **`<code>`** | Inline computer code fragment. | `Use <code>git status</code> to inspect git changes.` |
| **`<kbd>`** | User keyboard input or hotkey shortcut. | `Press <kbd>Ctrl</kbd> + <kbd>C</kbd> to copy.` |
| **`<samp>`** | Sample output from a computer program or script. | `<samp>Error 404: Not Found</samp>` |
| **`<var>`** | Mathematical variable or programming argument. | `The circumference is $2\pi \times <var>r</var>$.` |

---

## 4. Quotations, Citations & Abbreviations

### Inline Quotes and Citations

- **`<q>`**: Short inline quotation. Browsers automatically insert language-appropriate quotation marks around it.
- **`<cite>`**: Identifies the creative work title (e.g., book, paper, song, movie, artwork).
- **`<abbr>`**: Defines an abbreviation or acronym. The `title` attribute provides the expanded description.

```html
<p>
  As noted in <cite>The Pragmatic Programmer</cite>, 
  <q cite="https://pragprog.com">Don't Live with Broken Windows.</q>
</p>

<p>
  We build accessible interfaces following <abbr title="Web Content Accessibility Guidelines">WCAG</abbr> standards.
</p>
```

---

## 5. Machine-Readable Data & Time (`<time>`, `<data>`)

### The `<time>` Element

The `<time>` element represents a specific period in time or a date. Always provide an ISO 8601 machine-readable string in the `datetime` attribute.

```html
<!-- Specific date -->
<time datetime="2026-09-12">September 12, 2026</time>

<!-- Date and time with timezone offset -->
<time datetime="2026-09-12T14:30:00+02:00">2:30 PM CEST</time>

<!-- Duration -->
<time datetime="PT2H30M">2 hours and 30 minutes</time>
```

### The `<data>` Element

Links human-readable text with machine-readable tokens or identifiers.

```html
<ul>
  <li><data value="SKU-8921">Wireless Mechanical Keyboard</data></li>
  <li><data value="SKU-4402">Ultra-wide Monitor 34"</data></li>
</ul>
```

---

## 6. Inline Utilities (`<span>`, `<br>`, `<wbr>`, `<sub>`, `<sup>`, `<bdi>`)

- **`<span>`**: Generic non-semantic inline container for applying CSS styles or JavaScript behavior.
- **`<br>`**: Explicit line break. Use only when a break is significant (e.g., in postal addresses or poetry), never for spacing paragraphs.
- **`<wbr>`**: Word Break Opportunity. Suggests where a browser may optionally break a long string of text if needed.
- **`<sub>` & `<sup>`**: Subscript ($H_2O$: `H<sub>2</sub>O`) and superscript ($E = mc^2$: `E = mc<sup>2</sup>`).
- **`<bdi>`**: Bi-directional Isolation. Isolates a span of text that might be formatted in a different direction (e.g., Arabic/Hebrew usernames in an English list).
- **`<bdo>`**: Bi-directional Override. Overrides current text direction (`<bdo dir="rtl">` or `<bdo dir="ltr">`).
