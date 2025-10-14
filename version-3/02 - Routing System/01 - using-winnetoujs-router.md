# WinnetouJs Router: Building Single-Page Applications

## Overview

WinnetouJs provides its own powerful and lightweight routing system that enables you to build Single-Page Applications (SPAs) without external dependencies. The router manages navigation, browser history, and route-specific logic with a clean, type-safe API.

**Key Features:**

- 🚀 Zero external dependencies
- 📍 Browser history support (back/forward buttons)
- 🎯 Clean, organized route structure
- 🔄 Lifecycle hooks (onGo, onBack)
- 🎨 Easy integration with constructos
- 💪 TypeScript-friendly
- ⚡ Lightweight and fast

## Getting Started

### Installation

The router is included in WinnetouJs. Import it from the modules:

```javascript
import { Router } from "winnetoujs/modules/router";
```

### Basic Concept

The WinnetouJs Router follows a class-based pattern where you:

1. Create a router class that defines your routes
2. Each route has a `go()` method for navigation and a `set()` method for route logic
3. Use `Router.navigate()` to change routes
4. Use `Router.createRoutes()` to register all routes with lifecycle hooks

## Creating Your Router

### Recommended File Structure

Create a dedicated `router.ts` (or `router.js`) file to organize your routing logic:

```
src/
├── app.ts
├── router.ts          ← Your router class here
├── pages/
│   ├── home.wcto.html
│   ├── about.wcto.html
│   └── settings.wcto.html
└── components/
```

### Basic Router Setup

Here's a basic router implementation:

```typescript
// router.ts
import { Router } from "winnetoujs/modules/router";

export class MyRouter {
  constructor() {
    this.createRoutes();
  }

  private routes = {};

  public methods = {
    home: {
      go: () => Router.navigate("/home"),
      set: () => {
        this.routes["/home"] = () => {
          console.log("Home route called");
          // Your route logic here
        };
      },
    },
    about: {
      go: () => Router.navigate("/about"),
      set: () => {
        this.routes["/about"] = () => {
          console.log("About route called");
          // Your route logic here
        };
      },
    },
    settings: {
      go: () => Router.navigate("/settings"),
      set: () => {
        this.routes["/settings"] = () => {
          console.log("Settings route called");
          // Your route logic here
        };
      },
    },
  };

  private createRoutes() {
    // Register all routes
    Object.keys(this.methods).forEach(key => {
      this.methods[key].set();
    });

    // Create the router with lifecycle hooks
    Router.createRoutes(this.routes, {
      onGo(route) {
        console.log("Navigating to:", route);
      },
      onBack(route) {
        console.log("Going back to:", route);
      },
    });
  }
}
```

### Using the Router in Your App

```typescript
// app.ts
import { W } from "winnetoujs";
import { $navButton } from "./components.wcto";
import { MyRouter } from "./router";

// Initialize the router
const router = new MyRouter();

// Create navigation buttons
new $navButton({
  text: "Home",
  onclick: W.fx(() => {
    router.methods.home.go();
  }),
}).create("#nav");

new $navButton({
  text: "About",
  onclick: W.fx(() => {
    router.methods.about.go();
  }),
}).create("#nav");

new $navButton({
  text: "Settings",
  onclick: W.fx(() => {
    router.methods.settings.go();
  }),
}).create("#nav");
```

## Router Structure Explained

### The `methods` Object

Each route in the `methods` object has two functions:

#### `go()` - Navigate to the Route

```javascript
home: {
  go: () => Router.navigate("/home"),
  // ...
}
```

- Called when you want to navigate to this route
- Uses `Router.navigate()` to trigger the route change
- Updates browser history

#### `set()` - Define Route Logic

```javascript
home: {
  // ...
  set: () => {
    this.routes["/home"] = () => {
      // Your route-specific logic
      loadHomePage();
      updateTitle("Home");
    };
  };
}
```

- Defines what happens when the route is activated
- Contains the route's logic (loading pages, updating UI, etc.)
- Called during router initialization

### Lifecycle Hooks

The router provides two lifecycle hooks:

#### `onGo(route: string)`

Called **after** navigating to a new route:

```javascript
Router.createRoutes(this.routes, {
  onGo(route) {
    console.log("Navigated to:", route);
    // Perfect for:
    // - Analytics tracking
    // - Loading animations
    // - Scroll to top
    // - Update active nav state
  },
});
```

#### `onBack(route: string)`

Called **after** using browser back/forward buttons:

```javascript
Router.createRoutes(this.routes, {
  onBack(route) {
    console.log("Went back to:", route);
    // Perfect for:
    // - Restoring previous state
    // - Analytics tracking
    // - Cleanup operations
  },
});
```

Both hooks receive the route path as a parameter.

## Real-World Examples

### Example 1: Complete SPA with Multiple Pages

```html
<!-- pages.wcto.html -->
<winnetou description="Home page">
  <div id="[[homePage]]" class="page">
    <h1>Welcome Home</h1>
    <p>This is the home page</p>
  </div>
</winnetou>

<winnetou description="About page">
  <div id="[[aboutPage]]" class="page">
    <h1>About Us</h1>
    <p>Learn more about our application</p>
  </div>
</winnetou>

<winnetou description="Contact page">
  <div id="[[contactPage]]" class="page">
    <h1>Contact</h1>
    <form id="[[contactForm]]">
      <input type="email" placeholder="Your email" />
      <textarea placeholder="Your message"></textarea>
      <button type="submit">Send</button>
    </form>
  </div>
</winnetou>

<winnetou description="Navigation menu">
  <nav id="[[mainNav]]" class="navigation">
    <a id="[[homeLink]]" onclick="{{onHome}}">Home</a>
    <a id="[[aboutLink]]" onclick="{{onAbout}}">About</a>
    <a id="[[contactLink]]" onclick="{{onContact}}">Contact</a>
  </nav>
</winnetou>
```

```typescript
// router.ts
import { Router } from "winnetoujs/modules/router";
import { $homePage, $aboutPage, $contactPage } from "./pages.wcto";

export class AppRouter {
  constructor() {
    this.createRoutes();
  }

  private routes = {};
  private currentPage = null;

  public methods = {
    home: {
      go: () => Router.navigate("/"),
      set: () => {
        this.routes["/"] = () => {
          this.loadPage(() => {
            new $homePage().create("#content", { clear: true });
          });
        };
      },
    },
    about: {
      go: () => Router.navigate("/about"),
      set: () => {
        this.routes["/about"] = () => {
          this.loadPage(() => {
            new $aboutPage().create("#content", { clear: true });
          });
        };
      },
    },
    contact: {
      go: () => Router.navigate("/contact"),
      set: () => {
        this.routes["/contact"] = () => {
          this.loadPage(() => {
            new $contactPage().create("#content", { clear: true });
          });
        };
      },
    },
  };

  private loadPage(pageLoader: Function) {
    // Clear previous page
    const content = document.getElementById("content");
    if (content) {
      content.innerHTML = "";
    }

    // Load new page
    pageLoader();

    // Scroll to top
    window.scrollTo(0, 0);
  }

  private createRoutes() {
    Object.keys(this.methods).forEach(key => {
      this.methods[key].set();
    });

    Router.createRoutes(this.routes, {
      onGo(route) {
        console.log("Navigation to:", route);
        updateActiveNavLink(route);
        trackPageView(route);
      },
      onBack(route) {
        console.log("Back button pressed, route:", route);
        updateActiveNavLink(route);
      },
    });
  }
}

function updateActiveNavLink(route: string) {
  // Remove active class from all links
  document.querySelectorAll("nav a").forEach(link => {
    link.classList.remove("active");
  });

  // Add active class to current link
  const linkMap = {
    "/": "homeLink",
    "/about": "aboutLink",
    "/contact": "contactLink",
  };

  const activeId = linkMap[route];
  if (activeId) {
    const activeLinks = document.querySelectorAll(`[id*="${activeId}"]`);
    activeLinks.forEach(link => link.classList.add("active"));
  }
}

function trackPageView(route: string) {
  // Analytics tracking
  console.log("Page view:", route);
}
```

```typescript
// app.ts
import { W } from "winnetoujs";
import { $mainNav } from "./pages.wcto";
import { AppRouter } from "./router";

const router = new AppRouter();

// Create navigation
new $mainNav({
  onHome: W.fx(() => router.methods.home.go()),
  onAbout: W.fx(() => router.methods.about.go()),
  onContact: W.fx(() => router.methods.contact.go()),
}).create("#app");

// Load initial route
router.methods.home.go();
```

### Example 2: Router with Authentication

```typescript
// router.ts
import { Router } from "winnetoujs/modules/router";
import { W } from "winnetoujs";
import { $loginPage, $dashboardPage, $profilePage } from "./pages.wcto";

export class AuthRouter {
  constructor() {
    this.createRoutes();
  }

  private routes = {};

  public methods = {
    login: {
      go: () => Router.navigate("/login"),
      set: () => {
        this.routes["/login"] = () => {
          new $loginPage({
            onSubmit: W.fx(() => this.handleLogin()),
          }).create("#content", { clear: true });
        };
      },
    },
    dashboard: {
      go: () => {
        if (this.isAuthenticated()) {
          Router.navigate("/dashboard");
        } else {
          Router.navigate("/login");
        }
      },
      set: () => {
        this.routes["/dashboard"] = () => {
          if (!this.isAuthenticated()) {
            this.methods.login.go();
            return;
          }

          new $dashboardPage({
            username: W.getMutable("username"),
          }).create("#content", { clear: true });
        };
      },
    },
    profile: {
      go: () => {
        if (this.isAuthenticated()) {
          Router.navigate("/profile");
        } else {
          Router.navigate("/login");
        }
      },
      set: () => {
        this.routes["/profile"] = () => {
          if (!this.isAuthenticated()) {
            this.methods.login.go();
            return;
          }

          new $profilePage({
            user: this.getCurrentUser(),
          }).create("#content", { clear: true });
        };
      },
    },
    logout: {
      go: () => {
        this.handleLogout();
        Router.navigate("/login");
      },
      set: () => {
        // Logout doesn't need a route handler
        this.routes["/logout"] = () => {
          this.handleLogout();
          this.methods.login.go();
        };
      },
    },
  };

  private isAuthenticated(): boolean {
    return W.getMutable("isLoggedIn") === "true";
  }

  private getCurrentUser() {
    return {
      username: W.getMutable("username"),
      email: W.getMutable("email"),
    };
  }

  private handleLogin() {
    // Simulate login
    W.setMutable("isLoggedIn", "true");
    W.setMutable("username", "John Doe");
    W.setMutable("email", "john@example.com");

    this.methods.dashboard.go();
  }

  private handleLogout() {
    W.setMutable("isLoggedIn", "false");
    W.setMutable("username", "");
    W.setMutable("email", "");
  }

  private createRoutes() {
    Object.keys(this.methods).forEach(key => {
      this.methods[key].set();
    });

    Router.createRoutes(this.routes, {
      onGo(route) {
        console.log("Navigated to:", route);
        document.title = `App - ${route}`;
      },
      onBack(route) {
        console.log("Back to:", route);
      },
    });
  }
}
```

### Example 3: Router with Dynamic Content Loading

```typescript
// router.ts
import { Router } from "winnetoujs/modules/router";
import { $blogPost, $blogList, $loadingSpinner } from "./blog.wcto";

export class BlogRouter {
  constructor() {
    this.createRoutes();
  }

  private routes = {};

  public methods = {
    blogList: {
      go: () => Router.navigate("/blog"),
      set: () => {
        this.routes["/blog"] = async () => {
          this.showLoading();

          try {
            const posts = await this.fetchBlogPosts();
            new $blogList({ posts }).create("#content", { clear: true });
          } catch (error) {
            console.error("Error loading blog posts:", error);
            this.showError("Failed to load blog posts");
          }
        };
      },
    },
    blogPost: {
      go: (postId: string) => Router.navigate(`/blog/${postId}`),
      set: () => {
        // Handle dynamic routes with pattern matching
        this.routes["/blog/:id"] = async (postId: string) => {
          this.showLoading();

          try {
            const post = await this.fetchBlogPost(postId);
            new $blogPost({
              title: post.title,
              content: post.content,
              author: post.author,
              date: post.date,
            }).create("#content", { clear: true });
          } catch (error) {
            console.error("Error loading blog post:", error);
            this.showError("Failed to load blog post");
          }
        };
      },
    },
  };

  private showLoading() {
    new $loadingSpinner().create("#content", { clear: true });
  }

  private showError(message: string) {
    const content = document.getElementById("content");
    if (content) {
      content.innerHTML = `<div class="error">${message}</div>`;
    }
  }

  private async fetchBlogPosts() {
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 500));
    return [
      { id: "1", title: "First Post", excerpt: "..." },
      { id: "2", title: "Second Post", excerpt: "..." },
    ];
  }

  private async fetchBlogPost(id: string) {
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 500));
    return {
      id,
      title: "Blog Post Title",
      content: "Blog post content...",
      author: "John Doe",
      date: "2025-10-13",
    };
  }

  private createRoutes() {
    Object.keys(this.methods).forEach(key => {
      this.methods[key].set();
    });

    Router.createRoutes(this.routes, {
      onGo(route) {
        console.log("Loading page:", route);
        window.scrollTo(0, 0);
      },
      onBack(route) {
        console.log("Navigating back to:", route);
      },
    });
  }
}
```

### Example 4: Router with Nested Navigation

```typescript
// router.ts
import { Router } from "winnetoujs/modules/router";
import {
  $settingsLayout,
  $accountSettings,
  $privacySettings,
  $notificationSettings,
} from "./settings.wcto";

export class SettingsRouter {
  constructor() {
    this.createRoutes();
  }

  private routes = {};
  private settingsLayout = null;

  public methods = {
    settings: {
      go: () => Router.navigate("/settings"),
      set: () => {
        this.routes["/settings"] = () => {
          // Create settings layout if not exists
          if (!this.settingsLayout) {
            this.settingsLayout = new $settingsLayout().create("#content", {
              clear: true,
            });
          }

          // Load default settings tab
          this.methods.account.go();
        };
      },
    },
    account: {
      go: () => Router.navigate("/settings/account"),
      set: () => {
        this.routes["/settings/account"] = () => {
          this.ensureSettingsLayout();
          new $accountSettings().create(
            `#${this.settingsLayout.ids.settingsContent}`,
            { clear: true }
          );
        };
      },
    },
    privacy: {
      go: () => Router.navigate("/settings/privacy"),
      set: () => {
        this.routes["/settings/privacy"] = () => {
          this.ensureSettingsLayout();
          new $privacySettings().create(
            `#${this.settingsLayout.ids.settingsContent}`,
            { clear: true }
          );
        };
      },
    },
    notifications: {
      go: () => Router.navigate("/settings/notifications"),
      set: () => {
        this.routes["/settings/notifications"] = () => {
          this.ensureSettingsLayout();
          new $notificationSettings().create(
            `#${this.settingsLayout.ids.settingsContent}`,
            { clear: true }
          );
        };
      },
    },
  };

  private ensureSettingsLayout() {
    if (!this.settingsLayout) {
      this.settingsLayout = new $settingsLayout().create("#content", {
        clear: true,
      });
    }
  }

  private createRoutes() {
    Object.keys(this.methods).forEach(key => {
      this.methods[key].set();
    });

    Router.createRoutes(this.routes, {
      onGo(route) {
        console.log("Settings navigation:", route);
        updateSettingsTabs(route);
      },
      onBack(route) {
        console.log("Settings back:", route);
        updateSettingsTabs(route);
      },
    });
  }
}

function updateSettingsTabs(route: string) {
  // Update active tab based on route
  document.querySelectorAll(".settings-tab").forEach(tab => {
    tab.classList.remove("active");
  });

  const tabMap = {
    "/settings/account": "account-tab",
    "/settings/privacy": "privacy-tab",
    "/settings/notifications": "notifications-tab",
  };

  const activeTab = tabMap[route];
  if (activeTab) {
    const tab = document.getElementById(activeTab);
    if (tab) tab.classList.add("active");
  }
}
```

## Advanced Patterns

### Pattern 1: Route Guards

Protect routes with authentication checks:

```typescript
private checkAuth(callback: Function) {
  if (this.isAuthenticated()) {
    callback();
  } else {
    console.log("Access denied, redirecting to login");
    this.methods.login.go();
  }
}

// Use in route set()
dashboard: {
  go: () => Router.navigate("/dashboard"),
  set: () => {
    this.routes["/dashboard"] = () => {
      this.checkAuth(() => {
        loadDashboard();
      });
    };
  }
}
```

### Pattern 2: Route Parameters

Pass parameters to routes:

```typescript
profile: {
  go: (userId: string) => {
    Router.navigate(`/profile/${userId}`);
  },
  set: () => {
    this.routes["/profile/:userId"] = () => {
      const userId = this.getRouteParam("userId");
      loadUserProfile(userId);
    };
  }
}

private getRouteParam(param: string): string {
  const path = window.location.pathname;
  // Parse path to extract parameter
  // Implementation depends on your needs
  return path.split("/").pop() || "";
}
```

### Pattern 3: Lazy Loading

Load page constructos only when needed:

```typescript
about: {
  go: () => Router.navigate("/about"),
  set: () => {
    this.routes["/about"] = async () => {
      // Lazy load the about page module
      const { $aboutPage } = await import("./pages/about.wcto");
      new $aboutPage().create("#content", { clear: true });
    };
  }
}
```

### Pattern 4: Breadcrumb Navigation

Track navigation history:

```typescript
private breadcrumbs: string[] = [];

private addBreadcrumb(route: string) {
  this.breadcrumbs.push(route);
  this.updateBreadcrumbUI();
}

private updateBreadcrumbUI() {
  const breadcrumbEl = document.getElementById("breadcrumb");
  if (breadcrumbEl) {
    breadcrumbEl.innerHTML = this.breadcrumbs
      .map(route => `<span>${route}</span>`)
      .join(" > ");
  }
}

// In createRoutes lifecycle
Router.createRoutes(this.routes, {
  onGo(route) {
    this.addBreadcrumb(route);
  }
});
```

## Best Practices

### 1. Organize Routes by Feature

Group related routes together:

```typescript
public methods = {
  // User routes
  login: { /* ... */ },
  register: { /* ... */ },
  profile: { /* ... */ },

  // Admin routes
  adminDashboard: { /* ... */ },
  adminUsers: { /* ... */ },

  // Public routes
  home: { /* ... */ },
  about: { /* ... */ },
  contact: { /* ... */ },
};
```

### 2. Use Descriptive Route Names

```typescript
// ✅ Good - Clear and descriptive
userProfile: {
  go: () => Router.navigate("/user/profile"),
  // ...
}

// ❌ Avoid - Unclear abbreviations
up: {
  go: () => Router.navigate("/user/profile"),
  // ...
}
```

### 3. Centralize Route Logic

Keep route logic in the router, not in components:

```typescript
// ✅ Good - Logic in router
dashboard: {
  set: () => {
    this.routes["/dashboard"] = () => {
      if (!this.isAuthenticated()) {
        this.methods.login.go();
        return;
      }
      loadDashboard();
    };
  };
}

// ❌ Avoid - Logic scattered in components
// Component checking auth and redirecting manually
```

### 4. Handle Loading States

Show loading indicators during async operations:

```typescript
private async loadPageWithLoading(loader: Function) {
  this.showLoading();
  try {
    await loader();
  } catch (error) {
    this.showError(error.message);
  } finally {
    this.hideLoading();
  }
}
```

### 5. Clean Up on Route Change

Clear previous page data and event listeners:

```typescript
private cleanupCurrentPage() {
  // Clear mutables
  const tempKeys = ["searchQuery", "pageData", "formData"];
  tempKeys.forEach(key => {
    W.setMutableNotPersistent(key, "");
  });

  // Clear content
  const content = document.getElementById("content");
  if (content) {
    content.innerHTML = "";
  }
}
```

### 6. Use TypeScript for Type Safety

```typescript
interface RouteMethod {
  go: (...args: any[]) => void;
  set: () => void;
}

interface RouteMethods {
  [key: string]: RouteMethod;
}

public methods: RouteMethods = {
  // Your routes with full type safety
};
```

## Combining Router with Other Features

### Router + Mutables

```typescript
// Update page title with mutable
W.setMutable("pageTitle", "Home");

home: {
  set: () => {
    this.routes["/"] = () => {
      W.setMutable("pageTitle", "Home");
      loadHomePage();
    };
  };
}
```

### Router + Constructos Chaining

```typescript
dashboard: {
  set: () => {
    this.routes["/dashboard"] = () => {
      const layout = new $dashboardLayout().create("#content", { clear: true });
      new $dashboardHeader().create(`#${layout.ids.header}`);
      new $dashboardSidebar().create(`#${layout.ids.sidebar}`);
      new $dashboardContent().create(`#${layout.ids.content}`);
    };
  };
}
```

### Router + WinnetouFx

```typescript
// Navigation with event handlers
new $navLink({
  text: "Dashboard",
  onClick: W.fx(() => {
    router.methods.dashboard.go();
  }),
}).create("#nav");
```

## Troubleshooting

### Issue: Route Not Triggering

**Problem:** Clicking navigation doesn't trigger the route.

**Solution:** Ensure you're calling the `go()` method:

```typescript
// ✅ Correct
router.methods.home.go();

// ❌ Wrong
router.methods.home; // Missing .go()
```

### Issue: Back Button Not Working

**Problem:** Browser back button doesn't work as expected.

**Solution:** The router handles browser history automatically. Ensure `Router.createRoutes()` is called:

```typescript
private createRoutes() {
  Object.keys(this.methods).forEach(key => {
    this.methods[key].set();
  });

  // This is essential for back button support
  Router.createRoutes(this.routes, {
    onGo(route) { /* ... */ },
    onBack(route) { /* ... */ }
  });
}
```

### Issue: Route Not Found

**Problem:** Navigation results in no page loading.

**Solution:** Check that the route is registered in both `go()` and `set()`:

```typescript
myRoute: {
  go: () => Router.navigate("/my-route"),  // ✅ Must match
  set: () => {
    this.routes["/my-route"] = () => {     // ✅ Must match
      // Route logic
    };
  }
}
```

### Issue: Multiple Route Instances

**Problem:** Multiple router instances causing conflicts.

**Solution:** Create only one router instance and export it:

```typescript
// router.ts
export const router = new AppRouter();

// app.ts
import { router } from "./router";
router.methods.home.go();
```

## Performance Tips

### 1. Lazy Load Routes

Only load page modules when needed:

```typescript
about: {
  set: () => {
    this.routes["/about"] = async () => {
      const { $aboutPage } = await import("./pages/about.wcto");
      new $aboutPage().create("#content", { clear: true });
    };
  };
}
```

### 2. Cache Page Data

Avoid re-fetching data on every navigation:

```typescript
private pageCache = new Map();

private async loadPageData(pageId: string) {
  if (this.pageCache.has(pageId)) {
    return this.pageCache.get(pageId);
  }

  const data = await fetchData(pageId);
  this.pageCache.set(pageId, data);
  return data;
}
```

### 3. Optimize DOM Updates

Use `{ clear: true }` to efficiently replace content:

```typescript
// ✅ Efficient
new $page().create("#content", { clear: true });

// ❌ Less efficient
document.getElementById("content").innerHTML = "";
new $page().create("#content");
```

## Summary

The WinnetouJs Router provides everything you need for SPAs:

- ✅ Create a router class with `methods` object containing routes
- ✅ Each route has `go()` for navigation and `set()` for logic
- ✅ Use `Router.navigate()` to change routes
- ✅ Use `Router.createRoutes()` to register routes with hooks
- ✅ `onGo` hook called after forward navigation
- ✅ `onBack` hook called after back/forward buttons
- ✅ Organize routes in a dedicated `router.ts` file
- ✅ Combine with constructos, mutables, and WinnetouFx
- ✅ Implement route guards, lazy loading, and caching
- ✅ Browser history support built-in

With the WinnetouJs Router, you can build powerful Single-Page Applications with clean, maintainable routing!
