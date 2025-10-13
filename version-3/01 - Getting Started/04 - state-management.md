# State Management with Mutables

## Overview

WinnetouJs provides a powerful and intuitive state management system called **Mutables**. Mutables are reactive variables that can automatically update your constructos when their values change, eliminating the need for complex state management libraries or manual DOM manipulation.

Think of mutables as smart variables that:

- 🔄 Automatically update the UI when changed
- 💾 Can persist data in localStorage
- 🎯 Connect directly to constructo props
- ⚡ Work seamlessly across your entire application

## Understanding Mutables

Mutables come in two flavors:

1. **Persistent Mutables** - Stored in localStorage and survive page refreshes
2. **Non-Persistent Mutables** - Exist only in memory during the current session

Both types automatically update any constructos that reference them when their values change.

## Creating Mutables

### Persistent Mutables with setMutable()

Use `W.setMutable()` to create mutables that persist in the browser's localStorage:

```javascript
import { W } from "winnetoujs";

// Create a persistent mutable
W.setMutable("username", "John Doe");
W.setMutable("theme", "dark");
W.setMutable("isLoggedIn", "true");
```

**Syntax:**

```javascript
W.setMutable(key: string, value: string)
```

- `key` - Unique identifier for the mutable
- `value` - The value to store (always as string)

**Features:**

- ✅ Stored in localStorage
- ✅ Survives page refreshes
- ✅ Available across browser tabs
- ✅ Automatically updates connected constructos

### Non-Persistent Mutables with initMutable()

Use `W.initMutable()` to create mutables that exist only in memory:

```javascript
import { W } from "winnetoujs";

// Create a non-persistent mutable
const loadingState = W.initMutable("loading");
const searchQuery = W.initMutable("");
const errorMessage = W.initMutable(null);
```

**Syntax:**

```javascript
const key = W.initMutable(initialValue: string)
```

- `initialValue` - The starting value for the mutable
- Returns a unique key that you use to reference this mutable

**Features:**

- ✅ Lightweight and fast
- ✅ Not stored in localStorage
- ✅ Perfect for temporary UI states
- ✅ Automatically updates connected constructos

### Non-Persistent Mutables with setMutableNotPersistent()

You can also update non-persistent mutables using `W.setMutableNotPersistent()`:

```javascript
import { W } from "winnetoujs";

const status = W.initMutable("idle");

// Later, update it
W.setMutableNotPersistent(status, "loading");
W.setMutableNotPersistent(status, "success");
```

**Syntax:**

```javascript
W.setMutableNotPersistent(key: string, value: string)
```

## Reading Mutables

Use `W.getMutable()` to retrieve the current value of any mutable:

```javascript
import { W } from "winnetoujs";

W.setMutable("username", "Jane");
console.log(W.getMutable("username")); // "Jane"

const count = W.initMutable("0");
console.log(W.getMutable(count)); // "0"
```

**Syntax:**

```javascript
W.getMutable(key: string): string
```

## Connecting Mutables to Constructos

The real power of mutables is their ability to automatically update constructos. Connect a mutable to a constructo prop using the `{ mutable: "key" }` syntax:

### Basic Example

```html
<!-- components.wcto.html -->
<winnetou>
  <h1 id="[[pageTitle]]">{{text}}</h1>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $pageTitle } from "./components.wcto";

// Create a mutable
W.setMutable("title", "Welcome to My App");

// Connect it to the constructo
new $pageTitle({
  text: { mutable: "title" },
}).create("#app");

// Update the mutable - constructo updates automatically!
setTimeout(() => {
  W.setMutable("title", "Dashboard");
}, 2000);
```

When you call `W.setMutable("title", "Dashboard")`, the constructo automatically re-renders with the new value. No manual DOM manipulation needed!

### User Profile Example

```html
<!-- profile.wcto.html -->
<winnetou description="User profile display">
  <div id="[[userProfile]]" class="profile">
    <img src="{{avatar}}" alt="User avatar" />
    <h2>{{name}}</h2>
    <p>{{bio}}</p>
    <span class="status {{statusClass}}">{{status}}</span>
  </div>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $userProfile } from "./profile.wcto";

// Initialize user data
W.setMutable("userName", "John Doe");
W.setMutable("userBio", "Loading...");
W.setMutable("userStatus", "offline");
W.setMutable("userAvatar", "/images/default-avatar.png");

// Create the profile component
new $userProfile({
  name: { mutable: "userName" },
  bio: { mutable: "userBio" },
  status: { mutable: "userStatus" },
  statusClass: { mutable: "userStatus" }, // Reuse same mutable
  avatar: { mutable: "userAvatar" },
}).create("#app");

// Simulate fetching user data
async function loadUserData() {
  const userData = await fetch("/api/user").then(r => r.json());

  // Update mutables - UI updates automatically!
  W.setMutable("userName", userData.name);
  W.setMutable("userBio", userData.bio);
  W.setMutable("userStatus", userData.online ? "online" : "offline");
  W.setMutable("userAvatar", userData.avatarUrl);
}

loadUserData();
```

## Real-World Examples

### Example 1: Loading States

```html
<!-- loader.wcto.html -->
<winnetou description="Loading indicator">
  <div id="[[loadingIndicator]]" class="loader">
    <div class="spinner"></div>
    <p>{{message}}</p>
  </div>
</winnetou>

<winnetou description="Data display">
  <div id="[[dataDisplay]]">
    <h3>{{title}}</h3>
    <p>{{content}}</p>
  </div>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $loadingIndicator, $dataDisplay } from "./loader.wcto";

// Create non-persistent mutables for UI state
const loadingMessage = W.initMutable("Loading data...");
const dataTitle = W.initMutable("");
const dataContent = W.initMutable("");

// Create UI components
new $loadingIndicator({
  message: { mutable: loadingMessage },
}).create("#app");

new $dataDisplay({
  title: { mutable: dataTitle },
  content: { mutable: dataContent },
}).create("#results");

// Fetch data with loading states
async function fetchData() {
  W.setMutableNotPersistent(loadingMessage, "Loading data...");

  try {
    const response = await fetch("/api/data");
    const data = await response.json();

    // Update mutables with fetched data
    W.setMutableNotPersistent(loadingMessage, "");
    W.setMutableNotPersistent(dataTitle, data.title);
    W.setMutableNotPersistent(dataContent, data.content);
  } catch (error) {
    W.setMutableNotPersistent(loadingMessage, "Error loading data");
  }
}

fetchData();
```

### Example 2: Shopping Cart Counter

```html
<!-- cart.wcto.html -->
<winnetou description="Shopping cart badge">
  <button id="[[cartButton]]" class="cart-btn" onclick="{{onClick}}">
    <span class="icon">🛒</span>
    <span id="[[cartBadge]]" class="badge">{{count}}</span>
  </button>
</winnetou>

<winnetou description="Product card">
  <div id="[[productCard]]" class="product">
    <h3>{{name}}</h3>
    <p class="price">{{price}}</p>
    <button onclick="{{onAddToCart}}">Add to Cart</button>
  </div>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $cartButton, $productCard } from "./cart.wcto";

// Initialize cart count
W.setMutable("cartCount", "0");

// Create cart button
new $cartButton({
  count: { mutable: "cartCount" },
  onClick: W.fx(() => {
    console.log("View cart");
    viewCart();
  }),
}).create("#header");

// Create product cards
const products = [
  { id: 1, name: "Laptop", price: "$999" },
  { id: 2, name: "Mouse", price: "$29" },
  { id: 3, name: "Keyboard", price: "$79" },
];

products.forEach(product => {
  new $productCard({
    name: product.name,
    price: product.price,
    onAddToCart: W.fx(() => {
      addToCart(product);
    }),
  }).create("#products");
});

// Add to cart function
function addToCart(product) {
  const currentCount = parseInt(W.getMutable("cartCount"));
  const newCount = currentCount + 1;

  // Update mutable - cart badge updates automatically!
  W.setMutable("cartCount", newCount.toString());

  console.log(`Added ${product.name} to cart`);
}

function viewCart() {
  alert(`You have ${W.getMutable("cartCount")} items in cart`);
}
```

### Example 3: Authentication State

```html
<!-- auth.wcto.html -->
<winnetou description="User menu for logged in users">
  <div id="[[userMenu]]" class="user-menu">
    <span>Welcome, {{username}}!</span>
    <button onclick="{{onLogout}}">Logout</button>
  </div>
</winnetou>

<winnetou description="Login form for guests">
  <div id="[[loginForm]]" class="login-form">
    <h2>Please Log In</h2>
    <input id="[[usernameInput]]" type="text" placeholder="Username" />
    <button onclick="{{onLogin}}">Login</button>
  </div>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $userMenu, $loginForm } from "./auth.wcto";

// Initialize auth state
W.setMutable("isLoggedIn", "false");
W.setMutable("currentUser", "");

// Render based on login state
function renderAuthUI() {
  const isLoggedIn = W.getMutable("isLoggedIn") === "true";

  if (isLoggedIn) {
    new $userMenu({
      username: { mutable: "currentUser" },
      onLogout: W.fx(() => logout()),
    }).create("#auth-container", { clear: true });
  } else {
    new $loginForm({
      onLogin: W.fx(self => {
        const input = document.getElementById(
          self.closest(".login-form").querySelector("input").id
        );
        login(input.value);
      }, "this"),
    }).create("#auth-container", { clear: true });
  }
}

function login(username) {
  // Update auth mutables
  W.setMutable("isLoggedIn", "true");
  W.setMutable("currentUser", username);

  // Re-render UI
  renderAuthUI();

  console.log(`Logged in as ${username}`);
}

function logout() {
  W.setMutable("isLoggedIn", "false");
  W.setMutable("currentUser", "");

  // Re-render UI
  renderAuthUI();

  console.log("Logged out");
}

// Initial render
renderAuthUI();
```

### Example 4: Real-Time Data Updates

```html
<!-- dashboard.wcto.html -->
<winnetou description="Live statistics dashboard">
  <div id="[[statsDashboard]]" class="dashboard">
    <div class="stat-card">
      <h3>Active Users</h3>
      <p class="stat-value">{{activeUsers}}</p>
    </div>
    <div class="stat-card">
      <h3>Total Sales</h3>
      <p class="stat-value">{{totalSales}}</p>
    </div>
    <div class="stat-card">
      <h3>Server Status</h3>
      <p class="stat-value {{statusClass}}">{{serverStatus}}</p>
    </div>
    <div class="stat-card">
      <h3>Last Updated</h3>
      <p class="stat-value">{{lastUpdate}}</p>
    </div>
  </div>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $statsDashboard } from "./dashboard.wcto";

// Initialize dashboard mutables
const activeUsers = W.initMutable("0");
const totalSales = W.initMutable("$0.00");
const serverStatus = W.initMutable("Checking...");
const lastUpdate = W.initMutable("Never");

// Create dashboard with mutables
new $statsDashboard({
  activeUsers: { mutable: activeUsers },
  totalSales: { mutable: totalSales },
  serverStatus: { mutable: serverStatus },
  statusClass: { mutable: serverStatus },
  lastUpdate: { mutable: lastUpdate },
}).create("#app");

// Simulate real-time updates
async function fetchDashboardData() {
  try {
    const response = await fetch("/api/dashboard/stats");
    const data = await response.json();

    // Update all mutables - UI updates automatically!
    W.setMutableNotPersistent(activeUsers, data.activeUsers.toString());
    W.setMutableNotPersistent(totalSales, `$${data.totalSales.toFixed(2)}`);
    W.setMutableNotPersistent(
      serverStatus,
      data.serverOnline ? "Online" : "Offline"
    );
    W.setMutableNotPersistent(lastUpdate, new Date().toLocaleTimeString());
  } catch (error) {
    W.setMutableNotPersistent(serverStatus, "Error");
    console.error("Failed to fetch dashboard data:", error);
  }
}

// Update every 5 seconds
setInterval(fetchDashboardData, 5000);

// Initial fetch
fetchDashboardData();
```

### Example 5: Form Validation with Mutables

```html
<!-- form.wcto.html -->
<winnetou description="Validated input field">
  <div id="[[validatedInput]]" class="form-group">
    <label>{{label}}</label>
    <input
      id="[[inputField]]"
      type="{{inputType}}"
      placeholder="{{placeholder}}"
      onchange="{{onChange}}" />
    <span id="[[errorMessage]]" class="error">{{error}}</span>
  </div>
</winnetou>

<winnetou description="Submit button">
  <button
    id="[[submitButton]]"
    class="btn {{buttonClass}}"
    onclick="{{onSubmit}}"
    disabled="{{isDisabled}}">
    {{text}}
  </button>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $validatedInput, $submitButton } from "./form.wcto";

// Form state mutables
const emailError = W.initMutable("");
const passwordError = W.initMutable("");
const formValid = W.initMutable("false");
const submitButtonClass = W.initMutable("btn-disabled");

// Email validation
new $validatedInput({
  label: "Email",
  inputType: "email",
  placeholder: "Enter your email",
  error: { mutable: emailError },
  onChange: W.fx(input => {
    const value = input.value;
    const isValid = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value);

    if (!isValid && value.length > 0) {
      W.setMutableNotPersistent(emailError, "Invalid email address");
    } else {
      W.setMutableNotPersistent(emailError, "");
    }

    checkFormValidity();
  }, "this"),
}).create("#form");

// Password validation
new $validatedInput({
  label: "Password",
  inputType: "password",
  placeholder: "Enter your password",
  error: { mutable: passwordError },
  onChange: W.fx(input => {
    const value = input.value;

    if (value.length < 8 && value.length > 0) {
      W.setMutableNotPersistent(
        passwordError,
        "Password must be at least 8 characters"
      );
    } else {
      W.setMutableNotPersistent(passwordError, "");
    }

    checkFormValidity();
  }, "this"),
}).create("#form");

// Submit button
new $submitButton({
  text: "Submit",
  buttonClass: { mutable: submitButtonClass },
  isDisabled: { mutable: formValid },
  onSubmit: W.fx(() => {
    if (W.getMutable(formValid) === "true") {
      submitForm();
    }
  }),
}).create("#form");

function checkFormValidity() {
  const hasEmailError = W.getMutable(emailError) !== "";
  const hasPasswordError = W.getMutable(passwordError) !== "";

  if (!hasEmailError && !hasPasswordError) {
    W.setMutableNotPersistent(formValid, "true");
    W.setMutableNotPersistent(submitButtonClass, "btn-primary");
  } else {
    W.setMutableNotPersistent(formValid, "false");
    W.setMutableNotPersistent(submitButtonClass, "btn-disabled");
  }
}

function submitForm() {
  console.log("Form submitted!");
}
```

## Best Practices

### 1. Choose the Right Type of Mutable

**Use Persistent Mutables (`setMutable`) when:**

- ✅ User preferences (theme, language, settings)
- ✅ Authentication tokens
- ✅ Shopping cart data
- ✅ User input that should survive refreshes

**Use Non-Persistent Mutables (`initMutable`) when:**

- ✅ Loading states
- ✅ Error messages
- ✅ Temporary UI states
- ✅ Form validation states
- ✅ Real-time data that refreshes on load

### 2. Use Descriptive Names

```javascript
// ❌ Avoid
W.setMutable("u", "John");
const x = W.initMutable("0");

// ✅ Better
W.setMutable("currentUser", "John");
const loadingProgress = W.initMutable("0");
```

### 3. Keep Mutable Keys as Constants

For large applications, centralize your mutable keys:

```javascript
// mutables.js
export const MUTABLES = {
  USER_NAME: "userName",
  USER_EMAIL: "userEmail",
  IS_LOGGED_IN: "isLoggedIn",
  THEME: "theme",
  LANGUAGE: "language",
};
```

```javascript
// app.js
import { W } from "winnetoujs";
import { MUTABLES } from "./mutables";

W.setMutable(MUTABLES.USER_NAME, "John Doe");
const username = W.getMutable(MUTABLES.USER_NAME);
```

### 4. Initialize Mutables Early

Initialize your mutables at application startup:

```javascript
// app.js
import { W } from "winnetoujs";

// Initialize all mutables
function initializeApp() {
  // Set defaults if not already set
  if (!W.getMutable("theme")) {
    W.setMutable("theme", "light");
  }

  if (!W.getMutable("language")) {
    W.setMutable("language", "en-US");
  }

  // Start the app
  renderApp();
}

initializeApp();
```

### 5. Combine Mutables with Constructos Chaining

For complex UIs, use mutables with constructos chaining:

```javascript
import { W } from "winnetoujs";
import { $dashboard, $sidebar, $content } from "./layout.wcto";

// State mutables
const currentPage = W.initMutable("home");
const sidebarOpen = W.initMutable("true");

// Build dashboard
const dashboard = new $dashboard().create("#app");

const sidebar = new $sidebar({
  isOpen: { mutable: sidebarOpen },
}).create(dashboard.ids.sidebarArea);

const content = new $content({
  page: { mutable: currentPage },
}).create(dashboard.ids.contentArea);

// Navigation updates the mutable
function navigateTo(page) {
  W.setMutableNotPersistent(currentPage, page);
}
```

### 6. Clean Up Non-Persistent Mutables

While non-persistent mutables don't use localStorage, it's good practice to reset them when no longer needed:

```javascript
const modalState = W.initMutable("closed");

function openModal() {
  W.setMutableNotPersistent(modalState, "open");
}

function closeModal() {
  W.setMutableNotPersistent(modalState, "closed");
}
```

## Common Patterns

### Pattern 1: Toggle States

```javascript
import { W } from "winnetoujs";

W.setMutable("darkMode", "false");

function toggleDarkMode() {
  const current = W.getMutable("darkMode") === "true";
  W.setMutable("darkMode", (!current).toString());
}
```

### Pattern 2: Counter

```javascript
import { W } from "winnetoujs";

W.setMutable("count", "0");

function increment() {
  const current = parseInt(W.getMutable("count"));
  W.setMutable("count", (current + 1).toString());
}

function decrement() {
  const current = parseInt(W.getMutable("count"));
  W.setMutable("count", (current - 1).toString());
}
```

### Pattern 3: Multi-Step Forms

```javascript
import { W } from "winnetoujs";

const formStep = W.initMutable("1");

function nextStep() {
  const current = parseInt(W.getMutable(formStep));
  W.setMutableNotPersistent(formStep, (current + 1).toString());
}

function previousStep() {
  const current = parseInt(W.getMutable(formStep));
  if (current > 1) {
    W.setMutableNotPersistent(formStep, (current - 1).toString());
  }
}
```

## Troubleshooting

### Issue: Mutable Not Updating UI

**Problem:** You update a mutable but the constructo doesn't update.

**Solution:** Ensure you're connecting the mutable correctly:

```javascript
// ❌ Wrong - passing the value directly
new $component({
  text: W.getMutable("title"), // This won't update!
}).create("#app");

// ✅ Correct - connecting the mutable
new $component({
  text: { mutable: "title" }, // This will update automatically!
}).create("#app");
```

### Issue: Mutable Not Persisting

**Problem:** Data doesn't survive page refreshes.

**Solution:** Use `setMutable()` instead of `initMutable()` or `setMutableNotPersistent()`:

```javascript
// ❌ Wrong - not persistent
const theme = W.initMutable("dark");
W.setMutableNotPersistent(theme, "light");

// ✅ Correct - persistent
W.setMutable("theme", "dark");
W.setMutable("theme", "light"); // Survives refresh!
```

### Issue: Multiple Constructos Not Syncing

**Problem:** Multiple constructos using the same mutable aren't syncing.

**Solution:** Ensure all constructos reference the same mutable key:

```javascript
// All these will sync automatically
new $header({ title: { mutable: "pageTitle" } }).create("#header");
new $breadcrumb({ title: { mutable: "pageTitle" } }).create("#nav");
new $content({ title: { mutable: "pageTitle" } }).create("#main");

// Update once, all update
W.setMutable("pageTitle", "New Title"); // All three update!
```

## Performance Considerations

### Mutables are Efficient

- Mutables only update constructos that reference them
- Updates are batched and optimized
- No unnecessary re-renders

### Best Practices for Performance

1. **Use non-persistent mutables for temporary data** - They're faster since they don't write to localStorage
2. **Don't overuse persistent mutables** - localStorage has size limits
3. **Group related updates** - Update multiple mutables in sequence before the next render cycle

```javascript
// Update multiple mutables
W.setMutable("userName", "John");
W.setMutable("userEmail", "john@example.com");
W.setMutable("userRole", "admin");
// All connected constructos update together
```

## Summary

Mutables provide a simple yet powerful way to manage state in WinnetouJs:

- ✅ Use `W.setMutable()` for persistent data that survives refreshes
- ✅ Use `W.initMutable()` for temporary UI state
- ✅ Connect mutables to constructos with `{ mutable: "key" }`
- ✅ Update mutables with `setMutable()` or `setMutableNotPersistent()`
- ✅ Read values with `W.getMutable()`
- ✅ Mutables automatically update all connected constructos
- ✅ Choose the right type of mutable for your use case
- ✅ Follow naming conventions and best practices

With mutables, you get reactive state management without the complexity of external libraries!
