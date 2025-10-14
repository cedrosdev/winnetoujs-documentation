# Constructos API

Constructos are compiled HTML components that become JavaScript classes with methods for rendering and manipulation.

## Import

```javascript
import { $constructoName } from "./path/to/file.wcto";
```

---

## Constructo Class

### Constructor

Creates a new constructo instance with props and options.

**Signature:**

```typescript
new $ConstructoName(
  props?: Record<string, any>,
  options?: ConstructoOptions
): Constructo
```

**Parameters:**

- `props` - Object containing constructo properties
- `options` - Optional configuration object
  - `identifier` - Custom identifier for the constructo instance

**Returns:** `Constructo` instance

**Usage:**

```javascript
// Basic usage
new $myDiv({ content: "Hello" });

// With custom identifier
new $myDiv({ content: "Hello" }, { identifier: "main" });
```

---

## Instance Methods

### .create()

Attaches the constructo to the DOM at the specified location.

**Signature:**

```typescript
.create(
  target: string,
  options?: CreateOptions
): ConstructoResult
```

**Parameters:**

- `target` - CSS selector string (e.g., `"#app"`, `".container"`)
- `options` - Optional configuration object
  - `clear?: boolean` - Clear target element before inserting (default: `false`)
  - `reverse?: boolean` - Insert at top of target element (default: `false`)

**Returns:** `ConstructoResult` object containing:

- `ids: Record<string, string>` - All constructo IDs with their generated values

**Usage:**

**Basic:**

```javascript
new $header({ title: "My App" }).create("#app");
```

**With clear option:**

```javascript
new $content({ text: "New content" }).create("#main", {
  clear: true,
});
```

**With reverse option:**

```javascript
new $notification({ message: "Alert!" }).create("#notifications", {
  reverse: true,
});
```

**Accessing IDs:**

```javascript
const result = new $card({
  title: "Product",
  price: "$99",
}).create("#products");

// Access generated IDs
console.log(result.ids.card); // "card-win-1"
console.log(result.ids.title); // "title-win-1"
console.log(result.ids.price); // "price-win-1"

// Use for DOM manipulation
document.getElementById(result.ids.title).style.color = "blue";
```

**With custom identifier:**

```javascript
const leftPanel = new $panel(
  { content: "Left" },
  { identifier: "left" }
).create("#app");

console.log(leftPanel.ids.panel); // "panel-win-left"
```

---

### .constructoString()

Returns the constructo as an HTML string without attaching it to the DOM.

**Signature:**

```typescript
.constructoString(): string
```

**Parameters:** None

**Returns:** `string` - HTML string representation of the constructo

**Usage:**

**Nesting constructos:**

```javascript
import { $card, $button } from "./components.wcto";

const buttonHTML = new $button({
  text: "Click me",
  onclick: W.fx(() => console.log("clicked")),
}).constructoString();

new $card({
  content: buttonHTML,
}).create("#app");
```

**Building complex templates:**

```javascript
import { $item } from "./list.wcto";

const items = ["Apple", "Banana", "Orange"];
const itemsHTML = items
  .map(item => new $item({ text: item }).constructoString())
  .join("");

new $list({
  items: itemsHTML,
}).create("#app");
```

**Server-side rendering:**

```javascript
import { ssr } from "winnetoujs/modules/ssr";
import { $html } from "./template.wcto";

const template = new $html({
  content: ssr(
    new $header({ title: "SSR App" }).constructoString(),
    new $main({ content: data }).constructoString(),
    new $footer().constructoString()
  ),
}).constructoString();

res.send(template);
```

---

## Constructo Props

### Basic Props

Props are defined in constructo HTML using double brackets `{{prop}}`.

```html
<winnetou>
  <div id="[[myDiv]]">{{content}}</div>
</winnetou>
```

```javascript
new $myDiv({ content: "Hello World" }).create("#app");
```

### Optional Props

Use `?` to mark props as optional.

```html
<winnetou>
  <div id="[[card]]" class="{{className?}}">{{text}}</div>
</winnetou>
```

```javascript
// className is optional
new $card({ text: "Title" }).create("#app");
new $card({ text: "Title", className: "highlighted" }).create("#app");
```

### Typed Props

Use `:type` to specify prop types.

```html
<winnetou>
  <h1 id="[[title]]">{{text:string}}</h1>
  <span>{{count:number}}</span>
</winnetou>
```

```javascript
new $title({
  text: "Welcome",
  count: 42,
}).create("#app");
```

### Props with Descriptions

Use `(description)` to document props.

```html
<winnetou>
  <div id="[[userCard]]">
    <h3>{{name(The user's full name):string}}</h3>
    <p>{{bio(User biography)?:string}}</p>
  </div>
</winnetou>
```

### Union Types

Use `|` to define allowed values.

```html
<winnetou>
  <button id="[[btn]]" class="btn-{{variant:'primary'|'secondary'|'danger'}}">
    {{text}}
  </button>
</winnetou>
```

```javascript
new $btn({
  variant: "primary",
  text: "Submit",
}).create("#app");
```

### Mutable Props

Connect props to mutables using `{ mutable: "key" }` syntax.

```javascript
import { W } from "winnetoujs";

W.setMutable("username", "John");

new $userDisplay({
  name: { mutable: "username" },
}).create("#app");

// Auto-updates the constructo
W.setMutable("username", "Jane");
```

---

## Constructo IDs

### ID Syntax

IDs in constructos use double square brackets `[[id]]`.

```html
<winnetou>
  <div id="[[mainCard]]">
    <h2 id="[[cardTitle]]">{{title}}</h2>
    <p id="[[cardText]]">{{text}}</p>
  </div>
</winnetou>
```

### Generated IDs

WinnetouJs generates unique IDs using the pattern: `originalId-win-randomNumber`

```javascript
const card = new $mainCard({
  title: "Welcome",
  text: "Hello",
}).create("#app");

console.log(card.ids.mainCard); // "mainCard-win-1"
console.log(card.ids.cardTitle); // "cardTitle-win-1"
console.log(card.ids.cardText); // "cardText-win-1"
```

### Custom Identifiers

Override the random number with a custom identifier:

```javascript
const leftCard = new $mainCard(
  { title: "Left", text: "Content" },
  { identifier: "left" }
).create("#app");

console.log(leftCard.ids.mainCard); // "mainCard-win-left"
```

### Class Name Convention

The first ID becomes the constructo's class name with a `$` prefix:

```html
<!-- First ID is "userProfile" -->
<winnetou>
  <div id="[[userProfile]]">
    <span id="[[userName]]">{{name}}</span>
  </div>
</winnetou>
```

```javascript
// Imported as $userProfile
import { $userProfile } from "./user.wcto";
```

---

## Best Practices

### Props Design

**Use descriptive prop names:**

```javascript
// ✅ Good
new $card({
  productName: "Widget",
  price: "$99",
  inStock: true,
});

// ❌ Avoid
new $card({
  n: "Widget",
  p: "$99",
  s: true,
});
```

**Define prop types and descriptions:**

```html
<!-- ✅ Good -->
<winnetou>
  <div id="[[productCard]]">
    <h3>{{name(Product name):string}}</h3>
    <p>{{price(Display price):string}}</p>
    <span>{{inStock(Availability status)?:boolean}}</span>
  </div>
</winnetou>
```

### ID Management

**Use meaningful ID names:**

```html
<!-- ✅ Good -->
<winnetou>
  <div id="[[navigationMenu]]">
    <button id="[[homeButton]]">Home</button>
    <button id="[[profileButton]]">Profile</button>
  </div>
</winnetou>

<!-- ❌ Avoid -->
<winnetou>
  <div id="[[div1]]">
    <button id="[[btn1]]">Home</button>
    <button id="[[btn2]]">Profile</button>
  </div>
</winnetou>
```

**Access IDs through the result object:**

```javascript
// ✅ Good
const menu = new $navigationMenu().create("#app");
document.getElementById(menu.ids.homeButton).focus();

// ❌ Avoid hardcoding IDs
document.getElementById("homeButton-win-1").focus();
```

### Constructo Composition

**Use chaining for complex layouts:**

```javascript
// ✅ Good - Clean and maintainable
const pageId = new $page().create("#app").ids.page;
new $header().create(pageId);
new $content().create(pageId);
new $footer().create(pageId);

// ❌ Avoid - Hard to maintain
new $page({
  content:
    new $header().constructoString() +
    new $content().constructoString() +
    new $footer().constructoString(),
}).create("#app");
```

**Use constructoString() for dynamic lists:**

```javascript
// ✅ Good - Efficient rendering
const items = data
  .map(item => new $listItem({ text: item.name }).constructoString())
  .join("");

new $list({ items }).create("#app");
```

---

## Common Patterns

### Dynamic Content Loading

```javascript
const loading = W.initMutable("true");

new $container({
  content: { mutable: loading },
}).create("#app");

async function loadContent() {
  const data = await fetchData();
  const html = data
    .map(item => new $item({ ...item }).constructoString())
    .join("");

  W.setMutableNotPersistent(loading, html);
}
```

### Conditional Rendering

```javascript
W.setMutable("userLoggedIn", "false");

new $navbar({
  userSection: { mutable: "userLoggedIn" },
}).create("#app");

// Show login button or user menu based on state
function updateUserSection() {
  const loggedIn = W.getMutable("userLoggedIn") === "true";
  const section = loggedIn
    ? new $userMenu({ name: "John" }).constructoString()
    : new $loginButton().constructoString();

  W.setMutable("userLoggedIn", section);
}
```

### List Management

```javascript
class TodoList {
  constructor() {
    this.containerId = new $todoContainer().create("#app").ids.todoContainer;
    this.render();
  }

  render() {
    const todos = this.getTodos();
    todos.forEach(todo => {
      new $todoItem({
        text: todo.text,
        onDelete: W.fx(id => this.delete(id), todo.id),
      }).create(`#${this.containerId}`);
    });
  }

  delete(id) {
    // Delete logic
    document.querySelector(`#${this.containerId}`).innerHTML = "";
    this.render();
  }
}
```
