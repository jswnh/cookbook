# DOM Creation & Manipulation

A comprehensive guide to creating, inserting, replacing, cloning, and removing DOM elements using modern DOM manipulation APIs and best performance practices.

---

## 1. Modern DOM Mutation Methods

The modern DOM specification provides direct, intuitive methods on elements that accept multiple nodes and plain text strings. They replace legacy methods like `appendChild` and `insertBefore`.

| Modern Method | Legacy Equivalent | Behavior | Accepts Strings? | Multiple Args? |
| :--- | :--- | :--- | :--- | :--- |
| `parent.append(...nodes)` | `parent.appendChild(node)` | Inserts at the **end** of children | Yes | Yes |
| `parent.prepend(...nodes)` | `parent.insertBefore(node, first)` | Inserts at the **beginning** of children | Yes | Yes |
| `el.before(...nodes)` | `parent.insertBefore(node, el)` | Inserts immediately **before** `el` | Yes | Yes |
| `el.after(...nodes)` | `parent.insertBefore(node, el.next)`| Inserts immediately **after** `el` | Yes | Yes |
| `el.replaceWith(...nodes)` | `parent.replaceChild(new, el)` | Replaces `el` with new nodes | Yes | Yes |
| `el.remove()` | `parent.removeChild(el)` | Removes `el` from its parent | N/A | N/A |

### Practical Examples

```javascript
const list = document.querySelector("ul.tasks");

// 1. append(): insert multiple items or text at the end
const itemA = document.createElement("li");
itemA.textContent = "Finish documentation";
const itemB = document.createElement("li");
itemB.textContent = "Run tests";

list.append(itemA, itemB, "Direct text appended");

// 2. prepend(): insert at the beginning
const priorityItem = document.createElement("li");
priorityItem.textContent = "Urgent hotfix";
list.prepend(priorityItem);

// 3. before() and after(): insert relative to target element
const divider = document.createElement("hr");
list.before(divider); // Insert hr right above <ul>

// 4. replaceWith(): swap an element directly
const updatedItem = document.createElement("li");
updatedItem.textContent = "Replaced urgent hotfix";
priorityItem.replaceWith(updatedItem);

// 5. remove(): self-removal without referencing parentNode
updatedItem.remove();
```

---

## 2. Creating Elements & Nodes

### `document.createElement(tagName, options)`
Creates an element node specified by tag name.

```javascript
const button = document.createElement("button");
button.type = "button";
button.className = "btn btn-primary";
button.textContent = "Save Changes";
button.setAttribute("aria-label", "Save current changes to disk");
```

### `document.createTextNode(text)`
Creates a text node. Typically, setting `element.textContent` is preferred and more concise.

```javascript
const textNode = document.createTextNode("Hello World");
```

### Cloning Nodes: `element.cloneNode(deep)`
Creates a copy of an element. Passing `true` creates a **deep clone** copying all child nodes and attributes; passing `false` copies only the tag and its attributes.

> [!NOTE]
> Event listeners added via `addEventListener` are **not** copied by `cloneNode()`.

```javascript
const templateCard = document.querySelector(".card-blueprint");

// Deep clone the card including all nested children
const cardCopy = templateCard.cloneNode(true);
cardCopy.classList.remove("card-blueprint");
cardCopy.querySelector(".card-title").textContent = "New Card Item";

document.querySelector(".card-grid").append(cardCopy);
```

---

## 3. Adjacent HTML Insertion (`insertAdjacentHTML`)

`insertAdjacentHTML()` parses a string of HTML and inserts the resulting nodes into the DOM tree at a specified position. It is significantly faster than `innerHTML` because it does not reparse or destroy existing child elements or listeners.

```
       <!-- beforebegin -->
<div class="target-element">
       <!-- afterbegin -->
  <p>Existing Child Content</p>
       <!-- beforeend -->
</div>
       <!-- afterend -->
```

| Position | Location Relative to Target Element |
| :--- | :--- |
| `'beforebegin'` | Before the element itself (as preceding sibling) |
| `'afterbegin'` | Just inside the element, before its first child |
| `'beforeend'` | Just inside the element, after its last child |
| `'afterend'` | After the element itself (as following sibling) |

### Example

```javascript
const container = document.querySelector(".notifications-panel");

// Add notification at top
container.insertAdjacentHTML(
  "afterbegin",
  `<div class="alert alert-info">
    <span>New version available!</span>
    <button type="button" class="btn-close" aria-label="Dismiss">&times;</button>
  </div>`
);

// Append another notification at bottom
container.insertAdjacentHTML(
  "beforeend",
  `<div class="alert alert-success"><span>System sync complete.</span></div>`
);
```

> [!TIP]
> Similar methods exist for element nodes and raw text:
> - `el.insertAdjacentElement(position, elementNode)`
> - `el.insertAdjacentText(position, string)`

---

## 4. Modifying Content: `textContent` vs `innerText` vs `innerHTML`

| Property | Parses HTML? | Triggers Reflow? | Respects CSS styling? | Security Risk (XSS)? |
| :--- | :--- | :--- | :--- | :--- |
| **`textContent`** | No | No | No (returns raw text in tree) | **Safe** (escapes raw text) |
| **`innerText`** | No | **Yes** | Yes (aware of `display: none`, formatting) | **Safe** |
| **`innerHTML`** | **Yes** | **Yes** | N/A | **High risk** with unsanitized user input |
| **`outerHTML`** | **Yes** | **Yes** | N/A | Replaces element itself with parsed HTML |

### Comparison Example

```html
<div id="demo">
  Hello <span style="display: none;">Secret</span>
  World!
</div>
```

```javascript
const demo = document.getElementById("demo");

console.log(demo.textContent);
// "Hello Secret World!" (Includes hidden text, no layout calculation)

console.log(demo.innerText);
// "Hello World!" (Excludes hidden text, triggers layout calculation)

demo.textContent = "<b>Bold? No, escaped text!</b>";
// Renders literally as text: <b>Bold? No, escaped text!</b>

demo.innerHTML = "<strong>Now it is bold HTML</strong>";
// Parses and renders bold HTML
```

---

## 5. Modern Safe HTML Injection (`setHTMLUnsafe` & Baseline 2024)

Standard web platforms now provide native methods for parsing HTML strings, including Declarative Shadow DOM support:

- `element.setHTMLUnsafe(htmlString)`: Injects HTML into an element while supporting declarative shadow roots.
- `Document.parseHTMLUnsafe(htmlString)`: Parses an HTML string into a detached `Document` fragment.

```javascript
const targetContainer = document.querySelector("#preview-area");

// Modern Baseline standard for injecting HTML strings
targetContainer.setHTMLUnsafe(`
  <div class="user-badge">
    <h4>Dynamic Header</h4>
    <p>Rendered safely with modern engine support.</p>
  </div>
`);
```

> [!CAUTION]
> Always sanitize untrusted user input before passing it to `setHTMLUnsafe()` or `innerHTML` using a dedicated sanitizer library (such as DOMPurify) to prevent Cross-Site Scripting (XSS) attacks.

---

## 6. High-Performance Batching: `DocumentFragment` & `<template>`

Repeatedly appending elements directly to the live document triggers multiple browser reflows and repaints. Use a `DocumentFragment` to batch additions offscreen in memory.

### Using `DocumentFragment`

A `DocumentFragment` is a lightweight, minimal document container that has no parent. When appended to the DOM, its child nodes are moved into the destination in a single reflow, leaving the fragment empty.

```javascript
const userList = document.querySelector("#user-list");
const users = [
  { id: 1, name: "Alice", role: "Developer" },
  { id: 2, name: "Bob", role: "Designer" },
  { id: 3, name: "Charlie", role: "Manager" }
];

// Create an in-memory fragment
const fragment = document.createDocumentFragment();

users.forEach((user) => {
  const li = document.createElement("li");
  li.className = "user-item";
  li.dataset.userId = user.id;
  li.textContent = `${user.name} - ${user.role}`;
  
  // Append to offscreen fragment (zero browser reflows)
  fragment.append(li);
});

// Single DOM operation: inserts all children at once
userList.append(fragment);
```

---

### Instantiating HTML `<template>` Elements

The `<template>` tag allows storing markup templates directly in HTML that remain inert until cloned at runtime.

```html
<!-- In your HTML -->
<template id="todo-row-template">
  <tr class="todo-row">
    <td class="task-col"></td>
    <td class="status-col"><span class="badge">Pending</span></td>
    <td>
      <button type="button" class="btn-delete">Delete</button>
    </td>
  </tr>
</template>

<table id="todo-table">
  <tbody></tbody>
</table>
```

```javascript
const template = document.getElementById("todo-row-template");
const tbody = document.querySelector("#todo-table tbody");

function addTodo(taskTitle) {
  // Clone the template content (DocumentFragment)
  const clone = template.content.cloneNode(true);

  // Fill in content
  clone.querySelector(".task-col").textContent = taskTitle;
  
  // Attach event listener directly to cloned node
  clone.querySelector(".btn-delete").addEventListener("click", (e) => {
    e.target.closest("tr").remove();
  });

  // Append to live DOM
  tbody.append(clone);
}

addTodo("Audit accessibility standards");
addTodo("Implement View Transitions API");
```
