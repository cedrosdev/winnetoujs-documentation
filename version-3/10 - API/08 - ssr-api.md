# SSR API

The Server-Side Rendering (SSR) module provides functions for rendering WinnetouJs constructos on the server.

## Import

```javascript
import { ssr, escapeHTML, loadPartial } from "winnetoujs/modules/ssr";
```

---

## Functions

### ssr()

Concatenates multiple constructo strings into a single HTML string.

**Signature:**

```typescript
ssr(...content: (string | string[])[]): string
```

**Parameters:**

- `...content` - Variable number of strings or string arrays to concatenate
  - Accepts strings
  - Accepts arrays of strings (automatically flattened)
  - Accepts nested arrays (automatically flattened)

**Returns:** `string` - Concatenated HTML string

**Usage:**

**Basic concatenation:**

```javascript
import { ssr } from "winnetoujs/modules/ssr";
import { $header, $main, $footer } from "./components.wcto";

const headerHTML = new $header({ title: "SSR App" }).constructoString();
const mainHTML = new $main({ content: "Content" }).constructoString();
const footerHTML = new $footer().constructoString();

const fullPage = ssr(headerHTML, mainHTML, footerHTML);
```

**With arrays:**

```javascript
import { ssr } from "winnetoujs/modules/ssr";
import { $listItem } from "./components.wcto";

const items = ["Apple", "Banana", "Orange"];
const itemsHTML = items.map(item =>
  new $listItem({ text: item }).constructoString()
);

const page = ssr(
  new $header().constructoString(),
  itemsHTML, // Array is automatically flattened
  new $footer().constructoString()
);
```

**Features:**

- Flattens nested arrays automatically
- Efficient string concatenation
- Works with constructoString() output
- Perfect for server-side rendering

---

### escapeHTML()

Escapes HTML special characters to prevent XSS attacks.

**Signature:**

```typescript
escapeHTML(text: string): string
```

**Parameters:**

- `text` - The string to escape

**Returns:** `string` - Escaped HTML string

**Usage:**

```javascript
import { escapeHTML } from "winnetoujs/modules/ssr";

const userInput = '<script>alert("XSS")</script>';
const safe = escapeHTML(userInput);
// Result: "&lt;script&gt;alert(&quot;XSS&quot;)&lt;/script&gt;"

// Use in constructos
new $userContent({
  text: escapeHTML(userInput),
}).constructoString();
```

**Escaped characters:**

- `<` → `&lt;`
- `>` → `&gt;`
- `"` → `&quot;`
- `'` → `&#39;`
- `&` → `&amp;`

**Important:** Always escape user-generated content to prevent XSS vulnerabilities.

---

### loadPartial()

Loads a partial file content with caching support.

**Signature:**

```typescript
loadPartial(
  fileName: string,
  options?: { verbose?: boolean }
): string
```

**Parameters:**

- `fileName` - Path to the partial file to load
- `options` - Optional configuration object
  - `verbose?: boolean` - Log when partial is loaded from disk or cache (default: `false`)

**Returns:** `string` - File content

**Usage:**

**Basic usage:**

```javascript
import { loadPartial } from "winnetoujs/modules/ssr";

const content = loadPartial("./docs/terms.txt");
const privacy = loadPartial("./docs/privacy.txt");
```

**With verbose logging:**

```javascript
const content = loadPartial("./docs/doc1.txt", { verbose: true });
// Logs: "Loaded from disk: ./docs/doc1.txt" (first time)
// Logs: "Loaded from cache: ./docs/doc1.txt" (subsequent times)
```

**In SSR context:**

```javascript
import { ssr, loadPartial } from "winnetoujs/modules/ssr";
import { $html } from "./template.wcto";

const template = new $html({
  content: ssr(
    new $header().constructoString(),
    loadPartial("./content/about.html"),
    loadPartial("./content/features.html"),
    new $footer().constructoString()
  ),
}).constructoString();

res.send(template);
```

**Features:**

- Automatic caching (loads once, reuses content)
- Verbose logging for debugging
- Disable verbose in production for performance

**Important:** Disable verbose logging in production environments.

---

## Server Setup

### Node.js + Express

**Project structure:**

```
application/
├── server/
│   ├── index.js
│   └── winnetou-ssr/
│       ├── src/
│       │   ├── app.ts
│       │   └── components.wcto.html
│       ├── dist/
│       │   └── app.js
│       ├── win.config.json
│       ├── jsconfig.json
│       └── package.json
```

**win.config.json:**

```json
{
  "apps": ["./src/app.ts"],
  "outputDir": "./dist",
  "constructosSourceFolder": "./src"
}
```

**app.ts (bridge file):**

```typescript
import { ssr, escapeHTML, loadPartial } from "winnetoujs/modules/ssr";
import { $html, $header, $listItem } from "./components.wcto";

export { $html, $header, $listItem, ssr, escapeHTML, loadPartial };
```

**Compile with WBR:**

```bash
node wbr -b -n
# -b: bundle
# -n: node platform (for SSR)
```

**Important:** Use `--node` (`-n`) flag for SSR compilation. Do NOT use `--production` flag as it removes type information.

**server/index.js:**

```javascript
const express = require("express");
const {
  $html,
  $header,
  $listItem,
  ssr,
  escapeHTML,
  loadPartial,
} = require("./winnetou-ssr/dist/app.js");

const app = express();
const port = 3100;

app.get("/", (req, res) => {
  const header = new $header({
    title: "SSR Application",
  }).constructoString();

  const items = ["Item 1", "Item 2", "Item 3"];
  const list = items.map(item =>
    new $listItem({ text: escapeHTML(item) }).constructoString()
  );

  const template = new $html({
    content: ssr(header, list),
  }).constructoString();

  res.send(template);
});

app.listen(port, () => {
  console.log(`Server running on http://localhost:${port}`);
});
```

---

## Best Practices

### Security

**Always escape user input:**

```javascript
// ✅ Good - Escaped user content
const userName = escapeHTML(req.query.name);
new $welcome({
  name: userName,
}).constructoString();

// ❌ Dangerous - Unescaped user content
new $welcome({
  name: req.query.name, // XSS vulnerability!
}).constructoString();
```

### Performance

**Use loadPartial for static content:**

```javascript
// ✅ Good - Cached after first load
const terms = loadPartial("./content/terms.html");
const privacy = loadPartial("./content/privacy.html");

// ❌ Avoid - Re-reads file each time
const terms = fs.readFileSync("./content/terms.html", "utf-8");
```

**Disable verbose in production:**

```javascript
// ✅ Good - Verbose only in development
const isDev = process.env.NODE_ENV === "development";
const content = loadPartial("./docs/doc.txt", { verbose: isDev });

// ❌ Avoid - Verbose in production
const content = loadPartial("./docs/doc.txt", { verbose: true });
```

### Constructo Composition

**Use ssr() for clean composition:**

```javascript
// ✅ Good - Clean and maintainable
const page = ssr(header, navigation, main, sidebar, footer);

// ❌ Avoid - Manual concatenation
const page = header + navigation + main + sidebar + footer;
```

**Handle arrays properly:**

```javascript
// ✅ Good - ssr() flattens arrays
const items = data.map(item => renderItem(item));
const page = ssr(header, items, footer);

// ❌ Avoid - Manual join
const items = data.map(item => renderItem(item)).join("");
const page = header + items + footer;
```

---

## Common Patterns

### Dynamic List Rendering

```javascript
const { $html, $listItem, ssr, escapeHTML } = require("./dist/app.js");

app.get("/products", async (req, res) => {
  const products = await getProducts();

  const productList = products.map(product =>
    new $listItem({
      name: escapeHTML(product.name),
      price: escapeHTML(product.price),
    }).constructoString()
  );

  const template = new $html({
    content: ssr(
      new $header({ title: "Products" }).constructoString(),
      productList,
      new $footer().constructoString()
    ),
  }).constructoString();

  res.send(template);
});
```

### Conditional Rendering

```javascript
app.get("/dashboard", (req, res) => {
  const isLoggedIn = req.session.userId;

  const content = isLoggedIn
    ? ssr(
        new $userDashboard({
          name: escapeHTML(req.session.userName),
        }).constructoString()
      )
    : ssr(new $loginPrompt().constructoString());

  const template = new $html({
    content: ssr(
      new $header({ title: "Dashboard" }).constructoString(),
      content,
      new $footer().constructoString()
    ),
  }).constructoString();

  res.send(template);
});
```

### Using Partials

```javascript
app.get("/about", (req, res) => {
  const template = new $html({
    content: ssr(
      new $header({ title: "About Us" }).constructoString(),
      loadPartial("./content/about.html"),
      loadPartial("./content/team.html"),
      new $footer().constructoString()
    ),
  }).constructoString();

  res.send(template);
});
```

### Error Pages

```javascript
app.use((err, req, res, next) => {
  console.error(err.stack);

  const errorPage = new $html({
    content: ssr(
      new $header({ title: "Error" }).constructoString(),
      new $errorMessage({
        code: "500",
        message: escapeHTML("Internal Server Error"),
      }).constructoString(),
      new $footer().constructoString()
    ),
  }).constructoString();

  res.status(500).send(errorPage);
});

app.use((req, res) => {
  const notFoundPage = new $html({
    content: ssr(
      new $header({ title: "404" }).constructoString(),
      new $notFound({
        message: "Page not found",
      }).constructoString(),
      new $footer().constructoString()
    ),
  }).constructoString();

  res.status(404).send(notFoundPage);
});
```

### SEO-Optimized Pages

```javascript
app.get("/blog/:slug", async (req, res) => {
  const post = await getBlogPost(req.params.slug);

  if (!post) {
    return res.status(404).send("Post not found");
  }

  const template = new $html({
    title: escapeHTML(post.title),
    description: escapeHTML(post.excerpt),
    content: ssr(
      new $header({ title: escapeHTML(post.title) }).constructoString(),
      new $article({
        title: escapeHTML(post.title),
        content: escapeHTML(post.content),
        author: escapeHTML(post.author),
        date: post.date,
      }).constructoString(),
      new $footer().constructoString()
    ),
  }).constructoString();

  res.send(template);
});
```

### Data Fetching and Rendering

```javascript
app.get("/users", async (req, res) => {
  try {
    const users = await fetchUsers();

    const userCards = users.map(user =>
      new $userCard({
        name: escapeHTML(user.name),
        email: escapeHTML(user.email),
        avatar: escapeHTML(user.avatar),
      }).constructoString()
    );

    const template = new $html({
      content: ssr(
        new $header({ title: "Users" }).constructoString(),
        new $userGrid({
          users: ssr(...userCards),
        }).constructoString(),
        new $footer().constructoString()
      ),
    }).constructoString();

    res.send(template);
  } catch (error) {
    res.status(500).send("Error loading users");
  }
});
```

### Template Caching

```javascript
const cache = new Map();

function renderCachedPage(key, renderFn) {
  if (cache.has(key)) {
    return cache.get(key);
  }

  const html = renderFn();
  cache.set(key, html);
  return html;
}

app.get("/static", (req, res) => {
  const html = renderCachedPage("static-page", () => {
    return new $html({
      content: ssr(
        new $header({ title: "Static Page" }).constructoString(),
        loadPartial("./content/static.html"),
        new $footer().constructoString()
      ),
    }).constructoString();
  });

  res.send(html);
});
```

---

## Important Notes

### SSR Limitations

**No DOM access:**

```javascript
// ❌ Will fail in SSR - no DOM
new $component({
  onclick: W.fx(() => {
    document.getElementById("app").style.color = "red";
  }),
}).constructoString();

// ✅ SSR-safe - no DOM interaction
new $component({
  text: "Static content",
}).constructoString();
```

**Use constructoString(), not create():**

```javascript
// ❌ Will fail - no DOM to attach to
new $header({ title: "SSR" }).create("#app");

// ✅ Correct for SSR
new $header({ title: "SSR" }).constructoString();
```

### WBR Compilation

**Always use --node flag for SSR:**

```bash
# ✅ Correct for SSR
node wbr -b -n

# ❌ Wrong for SSR
node wbr -b -p  # Production flag removes types
```

**Can combine with watch and verbose:**

```bash
# Development SSR build
node wbr -b -n -w -v
```
