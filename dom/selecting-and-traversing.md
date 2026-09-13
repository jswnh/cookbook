# DOM Selecting & Traversing

A complete guide to finding, matching, and navigating elements and nodes within the Document Object Model using modern JavaScript.

---

## 1. Querying Elements

### Modern Query Methods (CSS Selectors)

| Method | Returns | Description |
| :--- | :--- | :--- |
| `document.querySelector(selector)` | `Element \| null` | Returns the **first** matching element, or `null` if none found. |
| `document.querySelectorAll(selector)` | `NodeList` (Static) | Returns a static `NodeList` containing **all** matching elements. |
| `element.querySelector(selector)` | `Element \| null` | Scopes search to descendants of `element`. |
| `element.querySelectorAll(selector)` | `NodeList` (Static) | Scopes search to descendants of `element`. |

```javascript
// Select by ID, class, attribute, or complex selector
const header = document.querySelector("#main-header");
const activeNavLinks = document.querySelectorAll("nav.primary a.active");
const submitBtn = document.querySelector("button[type='submit']:not(:disabled)");

// Scoped queries inside an existing element
const card = document.querySelector(".card");
const cardTitle = card.querySelector(".card-title");
const cardBadges = card.querySelectorAll(".badge");
```

---

### Legacy & Specific ID/Tag/Class Methods

| Method | Returns | Collection Type |
| :--- | :--- | :--- |
| `document.getElementById(id)` | `Element \| null` | Single Element (Fastest) |
| `document.getElementsByClassName(name)` | `HTMLCollection` | **Live** Collection |
| `document.getElementsByTagName(name)` | `HTMLCollection` | **Live** Collection |
| `document.getElementsByName(name)` | `NodeList` | **Live** NodeList (by `name` attribute) |

```javascript
// High performance direct ID lookup
const rootContainer = document.getElementById("app-root");

// Live HTMLCollection - automatically updates when DOM changes
const liveButtons = document.getElementsByClassName("btn-action");
```

---

## 2. NodeList vs HTMLCollection

Understanding the differences between collections is crucial to avoid performance traps and subtle bugs.

| Feature | `NodeList` (`querySelectorAll`) | `HTMLCollection` (`getElementsBy*`) |
| :--- | :--- | :--- |
| **Contains** | Elements, Text, Comments | Elements only |
| **Live / Static** | **Static snapshot** (does not reflect later DOM changes) | **Live** (automatically updates when DOM changes) |
| **`forEach` support** | Built-in (`list.forEach(...)`) | Not built-in |
| **Array conversion** | `Array.from(list)` or `[...list]` | `Array.from(coll)` or `[...coll]` |

### Converting to Array for Array Methods

```javascript
const itemsList = document.querySelectorAll(".menu-item");

// Native forEach on NodeList:
itemsList.forEach((item, index) => {
  item.dataset.index = index;
});

// Convert to true Array to use map, filter, reduce, find:
const itemsArray = Array.from(itemsList);
// Or spread syntax:
const items = [...itemsList];

const activeTitles = items
  .filter((item) => item.classList.contains("is-active"))
  .map((item) => item.textContent.trim());
```

---

## 3. Modern CSS Selectors in DOM Queries

Modern browsers support cutting-edge CSS pseudo-classes inside `querySelector` and `querySelectorAll`:

```javascript
// 1. :has() relational selector (find elements based on their children)
// Select cards that contain a featured badge:
const featuredCards = document.querySelectorAll(".card:has(.badge-featured)");

// Select forms that contain an invalid input:
const invalidForms = document.querySelectorAll("form:has(input:invalid)");

// 2. :is() and :where() for grouping selectors
const headings = document.querySelectorAll(":is(h1, h2, h3).section-title");

// 3. :scope (references the context element in scoped queries)
const container = document.querySelector(".container");
// Select direct children of container only:
const directChildren = container.querySelectorAll(":scope > .item");
```

---

## 4. Element Matching & Closest Ancestors

### `element.matches(selector)`
Tests whether an element matches a given CSS selector string. Returns a boolean.

```javascript
const button = document.querySelector("button");

if (button.matches(":disabled, [aria-disabled='true']")) {
  console.log("Button is disabled");
}
```

### `element.closest(selector)`
Traverses upwards from the element through its ancestors (including the element itself) until it finds a node that matches the selector. Returns `null` if no ancestor matches.

```javascript
document.addEventListener("click", (event) => {
  // Find closest button or link that was clicked (handles clicks on child icons/spans)
  const actionButton = event.target.closest("button[data-action]");

  if (actionButton) {
    const action = actionButton.dataset.action;
    console.log(`Executing action: ${action}`);
  }
});
```

### `element.contains(otherNode)`
Returns `true` if `otherNode` is a descendant of `element`, or the element itself.

```javascript
const dropdown = document.querySelector(".dropdown");

document.addEventListener("click", (event) => {
  // Detect clicks outside the dropdown
  if (!dropdown.contains(event.target)) {
    dropdown.classList.remove("is-open");
  }
});
```

---

## 5. Tree Navigation (Element Traversal vs Node Traversal)

The DOM tree contains both **Nodes** (which include text nodes, comments, and whitespace) and **Elements** (HTML tags). In almost all modern UI workflows, **Element Traversal** is preferred because it ignores blank text/whitespace nodes.

```
       [ Parent Element ]
           /        \
  previousElementSibling  nextElementSibling
         /            \
 [firstElementChild] ... [lastElementChild]
```

### Element Traversal (Ignores whitespace & text nodes)

| Property | Description |
| :--- | :--- |
| `el.parentElement` | Returns the parent `Element` (or `null`) |
| `el.children` | Returns a live `HTMLCollection` of child elements |
| `el.firstElementChild` | Returns the first child `Element` |
| `el.lastElementChild` | Returns the last child `Element` |
| `el.nextElementSibling` | Returns the next sibling `Element` |
| `el.previousElementSibling` | Returns the previous sibling `Element` |
| `el.childElementCount` | Number of child elements (`el.children.length`) |

```javascript
const currentItem = document.querySelector(".list-item.selected");

// Navigating siblings
const nextItem = currentItem.nextElementSibling;
const prevItem = currentItem.previousElementSibling;

// Navigating to parent and its children
const parentList = currentItem.parentElement;
const firstItem = parentList.firstElementChild;
const totalChildren = parentList.childElementCount;
```

---

### Node Traversal (Includes text nodes and comments)

| Property | Description |
| :--- | :--- |
| `node.parentNode` | Parent `Node` (can be `Document`) |
| `node.childNodes` | `NodeList` of all child nodes (elements, text, whitespace) |
| `node.firstChild` | First child `Node` (often a text whitespace node!) |
| `node.lastChild` | Last child `Node` |
| `node.nextSibling` | Next sibling `Node` |
| `node.previousSibling` | Previous sibling `Node` |

```javascript
// Warning: firstChild is often a text node containing newline/spaces
const container = document.getElementById("container");
console.log(container.firstChild.nodeType); // 3 (TEXT_NODE) if there is whitespace
console.log(container.firstElementChild.nodeType); // 1 (ELEMENT_NODE)
```

---

## 6. Node Types & Inspection

Every DOM node has a `nodeType`, `nodeName`, and `nodeValue`:

| Constant | Value | Description |
| :--- | :--- | :--- |
| `Node.ELEMENT_NODE` | `1` | An HTML or SVG element (`<div>`, `<p>`) |
| `Node.TEXT_NODE` | `3` | Raw text inside an element or whitespace |
| `Node.COMMENT_NODE` | `8` | Comment `<!-- note -->` |
| `Node.DOCUMENT_NODE` | `9` | The root `document` object |
| `Node.DOCUMENT_FRAGMENT_NODE` | `11` | Lightweight container (`DocumentFragment`) |

```javascript
function inspectNode(node) {
  switch (node.nodeType) {
    case Node.ELEMENT_NODE:
      console.log(`Element: <${node.tagName.toLowerCase()}>`);
      break;
    case Node.TEXT_NODE:
      console.log(`Text content: "${node.textContent.trim()}"`);
      break;
    case Node.COMMENT_NODE:
      console.log(`Comment: "${node.nodeValue}"`);
      break;
  }
}
```

---

## 7. Advanced DOM Traversal: `TreeWalker`

For filtering and traversing complex DOM subtrees (e.g. searching only for visible text nodes or specific custom elements), `document.createTreeWalker()` provides maximum control and performance.

```javascript
// Find all text nodes that contain non-whitespace text within an article
const article = document.querySelector("article");

const walker = document.createTreeWalker(
  article,
  NodeFilter.SHOW_TEXT,
  {
    acceptNode(node) {
      return node.textContent.trim().length > 0
        ? NodeFilter.FILTER_ACCEPT
        : NodeFilter.FILTER_REJECT;
    }
  }
);

let currentNode = walker.nextNode();
const textChunks = [];

while (currentNode) {
  textChunks.push(currentNode.textContent.trim());
  currentNode = walker.nextNode();
}

console.log("Collected text nodes:", textChunks);
```
