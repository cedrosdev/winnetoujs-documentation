# Constructos: Building Reusable Components

## Overview

Constructos are the building blocks of WinnetouJs applications. They are HTML components that get compiled into reusable JavaScript classes, allowing you to create modular, maintainable, and type-safe user interfaces.

When WinnetouJs is installed, it provides a WBR.js file which transpiles `.wcto.html` files into `.wcto.js` files. You write HTML components (called constructos) inside `.wcto.html` files, and WBR compiles them into reusable JS classes that can be imported into your JS/TS applications.

## Creating Your First Constructo

### File Structure

Constructos are created in files with the `.wcto.html` extension. The compiled `.wcto.js` files will be generated alongside the `.wcto.html` files automatically by WBR.

**Best Practices for File Organization:**

- Put all similar constructos in one single HTML file
- Group them in folders by functionality
- Use a consistent naming convention

**Recommended Project Structure:**

```
application/
├── src/
│   ├── app.ts
│   ├── common-constructos/
│   │   ├── common.wcto.html
│   │   ├── _common.scss
│   │   └── common.ts
│   ├── cards/
│   │   ├── cards.wcto.html
│   │   ├── _cards.scss
│   │   └── cards.ts
├── libs/
├── sass/
│   ├── main.scss
├── package.json
├── win.config.json
├── wbr.js
└── README.md
```

### Basic Constructo Syntax

Every constructo must be created inside `<winnetou>` tags:

```html
<winnetou>
  <div id="[[myDiv]]">{{content}}</div>
</winnetou>
```

**Important:** Do not duplicate the `<winnetou>` tag. Each constructo should have exactly one opening and closing `<winnetou>` tag.

❌ **Bad Pattern:**

```html
<winnetou></winnetou>
<!-- rest of code -->
</winnetou>
```

✅ **Good Pattern:**

```html
<winnetou>
  <!-- all content here -->
</winnetou>
```

### Adding Descriptions

You can add a `description` attribute to the `<winnetou>` tag to document what the constructo does:

```html
<winnetou description="A price card component for displaying product pricing">
  <div id="[[priceCard]]" class="card">
    <h3>{{productName}}</h3>
    <p class="price">{{price}}</p>
  </div>
</winnetou>
```

## Working with IDs

### ID Syntax

IDs in constructos must be enclosed in double square brackets: `[[id]]`

```html
<winnetou>
  <div id="[[myFirstDiv]]">Hello World!</div>
</winnetou>
```

### Constructo Class Names

When a constructo HTML file is compiled, the ID of the constructo becomes the class name prefixed with `$`:

```html
<!-- myComponents.wcto.html -->
<winnetou>
  <div id="[[myFirstDiv]]">{{content}}</div>
</winnetou>
```

```javascript
// app.js
import { $myFirstDiv } from "./myComponents.wcto";
new $myFirstDiv({ content: "Hello!" }).create("#app");
```

### Accessing IDs at Runtime

When constructos are placed in the HTML, WinnetouJs creates unique string IDs using the formula: `originalId-win-randomNumber`

The `create()` method returns an object containing all the IDs:

```javascript
const myConstructo = new $myFirstDiv({ content: "Test" }).create("#app");
console.log(myConstructo.ids.myFirstDiv); // "myFirstDiv-win-1"
```

### Custom Identifiers

You can override the random number with a custom identifier using the `identifier` option:

```javascript
let left = new $myDiv({}, { identifier: "leftDiv" }).create("#app");
console.log(left.ids.myDiv); // "myDiv-win-leftDiv"

let right = new $myDiv({}, { identifier: "rightDiv" }).create("#app");
console.log(right.ids.myDiv); // "myDiv-win-rightDiv"
```

This is particularly useful for DOM manipulations when you need predictable IDs.

### Multiple IDs in One Constructo

A constructo can have many IDs. The first ID becomes the class name, and all IDs are returned by the `create()` method:

```html
<winnetou description="A card with multiple interactive elements">
  <div id="[[mainCard]]" class="card">
    <h2 id="[[cardTitle]]">{{title}}</h2>
    <p id="[[cardText]]">{{text}}</p>
    <button id="[[cardButton]]">{{buttonText}}</button>
  </div>
</winnetou>
```

```javascript
import { $mainCard } from "./cards.wcto";

const card = new $mainCard({
  title: "Welcome",
  text: "This is a card component",
  buttonText: "Click Me",
}).create("#app");

// Access all IDs
const { mainCard, cardTitle, cardText, cardButton } = card.ids;

// Manipulate specific elements
document.getElementById(cardTitle).style.color = "blue";
document.getElementById(cardButton).addEventListener("click", () => {
  console.log("Button clicked!");
});
```

## Working with Props

Props allow you to pass dynamic data into your constructos, making them flexible and reusable.

### Basic Props

Create props using double curly brackets: `{{prop}}`

```html
<winnetou>
  <h1 id="[[title]]">{{text}}</h1>
</winnetou>
```

```javascript
import { $title } from "./components.wcto";
new $title({ text: "My Page Title" }).create("#app");
```

### Optional Props

Make a prop optional by adding a `?` token:

```html
<winnetou>
  <div id="[[userCard]]" class="{{className?}}">
    <h3>{{userName}}</h3>
    <p>{{bio?}}</p>
  </div>
</winnetou>
```

```javascript
// Valid - bio is optional
new $userCard({
  userName: "John Doe",
}).create("#app");

// Also valid - providing all props
new $userCard({
  userName: "John Doe",
  className: "featured",
  bio: "Software Developer",
}).create("#app");
```

### Typed Props

Add type annotations using the `:` token for TypeScript/JSDoc type checking:

```html
<winnetou>
  <div id="[[counter]]">
    <span>{{label:string}}</span>
    <strong>{{count:number}}</strong>
  </div>
</winnetou>
```

```javascript
new $counter({
  label: "Total Items:",
  count: 42,
}).create("#app");
```

### Props with Descriptions

Document your props using parentheses:

```html
<winnetou>
  <div
    id="[[colorBox]]"
    style="background-color: {{color(A valid CSS color value)}}">
    {{content(The text content to display inside the box)}}
  </div>
</winnetou>
```

### Combining All Prop Features

You can mix optional markers, types, and descriptions:

```html
<winnetou description="A customizable alert component">
  <div
    id="[[alert]]"
    class="alert {{type(The alert type)?:'success'|'warning'|'danger'}}">
    <h4>{{title(The alert title):string}}</h4>
    <p>{{message(The alert message):string}}</p>
    <span>{{timestamp(ISO timestamp)?:string}}</span>
  </div>
</winnetou>
```

```javascript
import { $alert } from "./components.wcto";

// Using with all props
new $alert({
  type: "danger",
  title: "Error",
  message: "Something went wrong!",
  timestamp: "2025-10-13T10:30:00Z",
}).create("#app");

// Using without optional props
new $alert({
  type: "success",
  title: "Success",
  message: "Operation completed!",
}).create("#app");
```

## Constructo Methods

Every constructo class has two main methods for rendering:

### 1. create() Method

The `create()` method attaches a constructo to the DOM. It requires a CSS selector string as a parameter.

**Basic Usage:**

```javascript
import { $title } from "./components.wcto";

new $title({ text: "Hello World" }).create("#app");
```

**With Options:**

The `create()` method accepts an options object as a second parameter:

```javascript
new $title({ text: "Simple title" }).create("#titles", {
  clear: true, // Clear the target element before inserting
  reverse: true, // Insert at the top instead of bottom
});
```

**Options Explained:**

- `clear: true` - Removes all existing content from the target element before inserting the constructo
- `reverse: true` - Inserts the constructo at the beginning of the target element (instead of appending)

**Example with Multiple Constructos:**

```javascript
import { $card } from "./cards.wcto";

// Clear the container and add first card
new $card({ title: "First Card" }).create("#container", { clear: true });

// Add more cards without clearing
new $card({ title: "Second Card" }).create("#container");
new $card({ title: "Third Card" }).create("#container");

// Add a card at the top
new $card({ title: "Priority Card" }).create("#container", { reverse: true });
```

### 2. constructoString() Method

The `constructoString()` method returns the HTML string of a constructo without attaching it to the DOM. This is essential for nesting constructos inside other constructos.

**Nesting Constructos:**

```html
<!-- components.wcto.html -->
<winnetou>
  <div id="[[outerDiv]]" class="outer">{{content}}</div>
</winnetou>

<winnetou>
  <div id="[[innerDiv]]" class="inner">{{text}}</div>
</winnetou>
```

```javascript
import { $outerDiv, $innerDiv } from "./components.wcto";

new $outerDiv({
  content: new $innerDiv({
    text: "I'm nested inside!",
  }).constructoString(),
}).create("#app");
```

**Building Lists:**

```javascript
import { $list, $listItem } from "./lists.wcto";

const items = ["Apple", "Banana", "Orange", "Grape"];

const listItems = items
  .map(fruit => new $listItem({ text: fruit }).constructoString())
  .join("");

new $list({ items: listItems }).create("#app");
```

> **⚠️ Important Note:** While `constructoString()` is useful for simple nesting and building lists, in complex systems with multiple nested components, it's better to create separated constructos and use **constructos chaining** (see below) to avoid confusion and code smell.

## Constructos Chaining

Constructos chaining is a powerful pattern for building complex UIs in a clean and maintainable way. Instead of nesting constructos with `constructoString()`, you create separate constructos and use their IDs to attach them to each other.

This approach ensures:

- ✅ Clean, readable code
- ✅ Reusable constructos
- ✅ Better separation of concerns
- ✅ Easier maintenance and testing

### Basic Chaining Pattern

```html
<!-- layout.wcto.html -->
<winnetou>
  <header id="[[header]]">I'm a header</header>
</winnetou>

<winnetou>
  <footer id="[[footer]]">I'm a footer</footer>
</winnetou>

<winnetou>
  <div id="[[page]]">I'm a page</div>
</winnetou>
```

```javascript
import { $header, $footer, $page } from "./layout.wcto";

// Create the main page container first
const pageId = new $page().create("#app").ids.page;

// Chain other constructos to the page
new $header().create(pageId);
new $footer().create(pageId);
```

### Advanced Chaining Example: Dashboard Layout

Let's build a complete dashboard using constructos chaining:

```html
<!-- dashboard.wcto.html -->
<winnetou description="Main dashboard container">
  <div id="[[dashboardContainer]]" class="dashboard">
    <div id="[[dashboardHeader]]" class="dashboard-header"></div>
    <div id="[[dashboardBody]]" class="dashboard-body">
      <aside id="[[dashboardSidebar]]" class="sidebar"></aside>
      <main id="[[dashboardContent]]" class="content"></main>
    </div>
  </div>
</winnetou>

<winnetou description="Dashboard header with navigation">
  <div id="[[topNav]]" class="top-nav">
    <h1>{{title:string}}</h1>
    <nav id="[[navLinks]]"></nav>
  </div>
</winnetou>

<winnetou description="Sidebar menu">
  <div id="[[sidebarMenu]]" class="menu">
    <h3>{{menuTitle:string}}</h3>
    <ul id="[[menuItems]]"></ul>
  </div>
</winnetou>

<winnetou description="Menu item">
  <li id="[[menuItem]]" class="menu-item" onclick="{{onClick}}">
    {{label:string}}
  </li>
</winnetou>

<winnetou description="Main content area">
  <div id="[[contentArea]]" class="content-area">
    <h2>{{pageTitle:string}}</h2>
    <div id="[[contentBody]]">{{content}}</div>
  </div>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import {
  $dashboardContainer,
  $topNav,
  $sidebarMenu,
  $menuItem,
  $contentArea,
} from "./dashboard.wcto";

// Build the dashboard using chaining
function buildDashboard() {
  // 1. Create main container
  const dashboard = new $dashboardContainer().create("#app");

  // 2. Chain header to container
  const header = new $topNav({ title: "My Dashboard" }).create(
    dashboard.ids.dashboardHeader
  );

  // 3. Chain sidebar to container
  const sidebar = new $sidebarMenu({ menuTitle: "Navigation" }).create(
    dashboard.ids.dashboardSidebar
  );

  // 4. Chain menu items to sidebar
  const menuData = [
    { label: "Home", route: "/home" },
    { label: "Profile", route: "/profile" },
    { label: "Settings", route: "/settings" },
    { label: "Logout", route: "/logout" },
  ];

  menuData.forEach(item => {
    new $menuItem({
      label: item.label,
      onClick: W.fx(() => {
        console.log(`Navigating to ${item.route}`);
        navigateTo(item.route);
      }),
    }).create(sidebar.ids.menuItems);
  });

  // 5. Chain main content to container
  const content = new $contentArea({
    pageTitle: "Welcome",
    content: "Dashboard content goes here",
  }).create(dashboard.ids.dashboardContent);

  return { dashboard, header, sidebar, content };
}

// Initialize the dashboard
const app = buildDashboard();

function navigateTo(route) {
  // Navigation logic
  console.log(`Route: ${route}`);
}
```

### Chaining with Dynamic Content

Constructos chaining works great for dynamic content updates:

```html
<!-- blog.wcto.html -->
<winnetou description="Blog post container">
  <div id="[[blogContainer]]" class="blog">
    <div id="[[postsArea]]" class="posts-area"></div>
  </div>
</winnetou>

<winnetou description="Individual blog post">
  <article id="[[blogPost]]" class="post">
    <h2 id="[[postTitle]]">{{title:string}}</h2>
    <p class="meta">By {{author:string}} on {{date:string}}</p>
    <div id="[[postContent]]">{{content:string}}</div>
    <div id="[[commentsSection]]"></div>
  </article>
</winnetou>

<winnetou description="Comment component">
  <div id="[[comment]]" class="comment">
    <strong>{{username:string}}</strong>
    <p>{{text:string}}</p>
    <small>{{timestamp:string}}</small>
  </div>
</winnetou>
```

```javascript
import { $blogContainer, $blogPost, $comment } from "./blog.wcto";

// Create blog with posts and comments using chaining
async function loadBlog() {
  // 1. Create container
  const blog = new $blogContainer().create("#app");

  // 2. Fetch and display posts
  const posts = await fetchPosts();

  posts.forEach(post => {
    // Create each post
    const postElement = new $blogPost({
      title: post.title,
      author: post.author,
      date: post.date,
      content: post.content,
    }).create(blog.ids.postsArea);

    // Chain comments to each post
    post.comments.forEach(commentData => {
      new $comment({
        username: commentData.username,
        text: commentData.text,
        timestamp: commentData.timestamp,
      }).create(postElement.ids.commentsSection);
    });
  });
}

async function fetchPosts() {
  // Simulated API call
  return [
    {
      title: "First Post",
      author: "John Doe",
      date: "2025-10-10",
      content: "This is the first post content...",
      comments: [
        {
          username: "Jane",
          text: "Great post!",
          timestamp: "2025-10-11 10:30",
        },
        {
          username: "Bob",
          text: "Very helpful!",
          timestamp: "2025-10-11 14:20",
        },
      ],
    },
    {
      title: "Second Post",
      author: "Jane Smith",
      date: "2025-10-12",
      content: "This is the second post content...",
      comments: [
        { username: "John", text: "Thanks!", timestamp: "2025-10-12 09:15" },
      ],
    },
  ];
}

loadBlog();
```

### When to Use Chaining vs constructoString()

**Use Constructos Chaining when:**

- ✅ Building complex layouts with multiple levels
- ✅ Creating reusable, independent components
- ✅ Need to manipulate individual components after creation
- ✅ Working with dynamic content that updates frequently
- ✅ Want clean, maintainable, and testable code

**Use constructoString() when:**

- ✅ Building simple lists or repeated elements
- ✅ Creating static content that won't change
- ✅ Nesting is shallow (one or two levels max)
- ✅ The nested content is simple and doesn't need separate manipulation

## Practical Examples

### Example 1: Simple Button Component

```html
<!-- buttons.wcto.html -->
<winnetou description="A reusable button component">
  <button
    id="[[customButton]]"
    class="btn {{variant:'primary'|'secondary'|'danger'}}"
    onclick="{{onclick}}">
    {{text(Button text):string}}
  </button>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $customButton } from "./buttons.wcto";

new $customButton({
  variant: "primary",
  text: "Save Changes",
  onclick: W.fx(() => {
    console.log("Save button clicked!");
  }),
}).create("#toolbar");
```

### Example 2: User Profile Card

```html
<!-- profiles.wcto.html -->
<winnetou description="User profile card with avatar and details">
  <div id="[[profileCard]]" class="profile-card">
    <img id="[[avatar]]" src="{{avatarUrl:string}}" alt="{{userName}} avatar" />
    <div id="[[profileInfo]]">
      <h3 id="[[userName]]">{{userName:string}}</h3>
      <p id="[[userBio]]">{{bio?:string}}</p>
      <span id="[[userRole]]" class="role-badge">
        {{role(User role)?:string}}
      </span>
    </div>
    <button id="[[contactButton]]" onclick="{{onContact}}">Contact</button>
  </div>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $profileCard } from "./profiles.wcto";

const profile = new $profileCard({
  avatarUrl: "/images/john-doe.jpg",
  userName: "John Doe",
  bio: "Full-stack developer passionate about web technologies",
  role: "Senior Developer",
  onContact: W.fx(() => {
    alert("Opening contact form...");
  }),
}).create("#team-members");

// Access specific elements for further manipulation
const { userName, userRole } = profile.ids;
document.getElementById(userName).style.fontWeight = "bold";
```

### Example 3: Dynamic Product Grid

```html
<!-- products.wcto.html -->
<winnetou description="Container for product items">
  <div id="[[productGrid]]" class="grid">{{products}}</div>
</winnetou>

<winnetou description="Individual product card">
  <div id="[[productCard]]" class="product-card">
    <img id="[[productImage]]" src="{{image:string}}" alt="{{name}}" />
    <h4 id="[[productName]]">{{name:string}}</h4>
    <p id="[[productPrice]]" class="price">{{price:string}}</p>
    <button id="[[addToCartBtn]]" onclick="{{onAddToCart}}">Add to Cart</button>
  </div>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $productGrid, $productCard } from "./products.wcto";

const productsData = [
  { id: 1, name: "Laptop", price: "$999", image: "/images/laptop.jpg" },
  { id: 2, name: "Mouse", price: "$29", image: "/images/mouse.jpg" },
  { id: 3, name: "Keyboard", price: "$79", image: "/images/keyboard.jpg" },
];

const productCards = productsData
  .map(product =>
    new $productCard({
      name: product.name,
      price: product.price,
      image: product.image,
      onAddToCart: W.fx(() => {
        console.log(`Adding ${product.name} to cart`);
        addToCart(product.id);
      }),
    }).constructoString()
  )
  .join("");

new $productGrid({ products: productCards }).create("#shop");

function addToCart(productId) {
  // Cart logic here
  console.log(`Product ${productId} added to cart`);
}
```

## Best Practices

### 1. Naming Conventions

- Use descriptive, camelCase names for constructo IDs
- Prefix component-specific IDs to avoid conflicts
- Group related constructos in the same file

```html
<!-- Good -->
<winnetou>
  <div id="[[userProfileCard]]">...</div>
</winnetou>

<!-- Avoid -->
<winnetou>
  <div id="[[card1]]">...</div>
</winnetou>
```

### 2. Prop Documentation

Always document your props with types and descriptions for better developer experience:

```html
<winnetou description="A well-documented component">
  <div id="[[documentedComponent]]">
    <h2>{{title(The main heading):string}}</h2>
    <p>{{description(Component description)?:string}}</p>
    <span>{{priority(Priority level from 1-5):number}}</span>
  </div>
</winnetou>
```

### 3. Use Constructos Chaining for Complex UIs

Break complex UIs into smaller, reusable constructos and chain them together:

```javascript
// ❌ Avoid - One massive nested constructo
new $complexPage({
  header: new $pageHeader({...}).constructoString(),
  sidebar: new $pageSidebar({...}).constructoString(),
  content: new $pageContent({...}).constructoString(),
  footer: new $pageFooter({...}).constructoString()
}).create("#app");

// ✅ Better - Use constructos chaining
const page = new $pageLayout().create("#app");
new $pageHeader({...}).create(page.ids.headerArea);
new $pageSidebar({...}).create(page.ids.sidebarArea);
new $pageContent({...}).create(page.ids.contentArea);
new $pageFooter({...}).create(page.ids.footerArea);
```

This approach ensures clean code, better reusability, and easier maintenance.

### 4. Consistent File Organization

Keep related files together:

```
components/
├── buttons/
│   ├── buttons.wcto.html
│   ├── _buttons.scss
│   └── buttons.ts
├── forms/
│   ├── forms.wcto.html
│   ├── _forms.scss
│   └── forms.ts
```

### 5. Use Identifiers for Testing

Use custom identifiers for elements you need to test or manipulate:

```javascript
const loginForm = new $loginForm(
  {
    username: "",
    password: "",
  },
  { identifier: "loginForm" }
).create("#auth");

// Easy to find in tests
const formId = loginForm.ids.loginForm; // "loginForm-win-loginForm"
```

## Summary

Constructos are the foundation of WinnetouJs applications. Key takeaways:

- ✅ Create constructos in `.wcto.html` files inside `<winnetou>` tags
- ✅ Use `[[id]]` syntax for component IDs
- ✅ Define props with `{{prop}}` and add types, descriptions, and optional markers
- ✅ Use `create()` to attach constructos to the DOM
- ✅ Use `constructoString()` for simple nesting and building lists
- ✅ Use **constructos chaining** for complex UIs and better maintainability
- ✅ Access generated IDs through the `ids` property
- ✅ Organize constructos by functionality in separate folders
- ✅ Document your constructos with descriptions and typed props

With these fundamentals, you're ready to build powerful, reusable components in WinnetouJs!
