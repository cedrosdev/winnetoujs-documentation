# Router API

The Router module provides routing functionality for Single-Page Applications (SPAs).

## Import

```javascript
import { Router } from "winnetoujs/modules/router";
```

---

## Static Methods

### Router.navigate()

Navigates to a specified route.

**Signature:**

```typescript
Router.navigate(route: string): void
```

**Parameters:**

- `route` - The route path to navigate to (e.g., `"/home"`, `"/users/123"`)

**Returns:** `void`

**Usage:**

```javascript
Router.navigate("/home");
Router.navigate("/about");
Router.navigate("/users/profile");
```

**Features:**

- Updates browser history
- Triggers route handler function
- Calls `onGo` lifecycle hook
- Supports browser back/forward buttons

---

### Router.createRoutes()

Registers all routes and lifecycle hooks.

**Signature:**

```typescript
Router.createRoutes(
  routes: Record<string, Function>,
  hooks?: RouterHooks
): void
```

**Parameters:**

- `routes` - Object mapping route paths to handler functions
- `hooks` - Optional lifecycle hooks object
  - `onGo?: (route: string) => void` - Called after navigation
  - `onBack?: (route: string) => void` - Called after back navigation

**Returns:** `void`

**Usage:**

```javascript
const routes = {
  "/home": () => {
    console.log("Home route");
    loadHomePage();
  },
  "/about": () => {
    console.log("About route");
    loadAboutPage();
  },
  "/settings": () => {
    console.log("Settings route");
    loadSettingsPage();
  },
};

Router.createRoutes(routes, {
  onGo(route) {
    console.log("Navigated to:", route);
    updateActiveMenu(route);
  },
  onBack(route) {
    console.log("Went back to:", route);
    updateBreadcrumbs(route);
  },
});
```

---

## Router Class Pattern

The recommended way to organize routing is using a dedicated router class.

### Basic Router Implementation

```typescript
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

### Using the Router

```typescript
import { W } from "winnetoujs";
import { $navButton } from "./components.wcto";
import { MyRouter } from "./router";

// Initialize router
const router = new MyRouter();

// Navigate programmatically
router.methods.home.go();

// Navigate from constructo
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
```

---

## Route Methods Structure

Each route in the `methods` object has two functions:

### go()

Triggers navigation to the route.

**Signature:**

```typescript
go: () => void
```

**Usage:**

```javascript
public methods = {
  home: {
    go: () => Router.navigate("/home"),
    // ...
  }
};

// Call to navigate
router.methods.home.go();
```

---

### set()

Defines the route handler logic.

**Signature:**

```typescript
set: () => void
```

**Usage:**

```javascript
public methods = {
  home: {
    // ...
    set: () => {
      this.routes["/home"] = () => {
        // Route-specific logic
        loadHomePage();
        updateTitle("Home");
        trackPageView("/home");
      };
    },
  }
};
```

---

## Lifecycle Hooks

### onGo()

Called after navigating to a new route.

**Signature:**

```typescript
onGo: (route: string) => void
```

**Parameters:**

- `route` - The route that was navigated to

**Usage:**

```javascript
Router.createRoutes(routes, {
  onGo(route) {
    console.log("Navigated to:", route);

    // Update page title
    document.title = `App - ${route}`;

    // Track analytics
    analytics.pageView(route);

    // Update active menu
    updateActiveMenuItem(route);

    // Scroll to top
    window.scrollTo(0, 0);
  },
});
```

---

### onBack()

Called after using browser back button.

**Signature:**

```typescript
onBack: (route: string) => void
```

**Parameters:**

- `route` - The route that was navigated back to

**Usage:**

```javascript
Router.createRoutes(routes, {
  onBack(route) {
    console.log("Went back to:", route);

    // Restore scroll position
    restoreScrollPosition(route);

    // Update breadcrumbs
    updateBreadcrumbs(route);

    // Log navigation
    console.log("User navigated back to:", route);
  },
});
```

---

## Best Practices

### Route Organization

**Organize routes by feature:**

```javascript
export class AppRouter {
  public methods = {
    // Auth routes
    login: {
      go: () => Router.navigate("/login"),
      set: () => { this.routes["/login"] = () => loadLogin(); },
    },
    register: {
      go: () => Router.navigate("/register"),
      set: () => { this.routes["/register"] = () => loadRegister(); },
    },

    // User routes
    profile: {
      go: () => Router.navigate("/profile"),
      set: () => { this.routes["/profile"] = () => loadProfile(); },
    },
    settings: {
      go: () => Router.navigate("/settings"),
      set: () => { this.routes["/settings"] = () => loadSettings(); },
    },

    // Content routes
    home: {
      go: () => Router.navigate("/"),
      set: () => { this.routes["/"] = () => loadHome(); },
    },
    about: {
      go: () => Router.navigate("/about"),
      set: () => { this.routes["/about"] = () => loadAbout(); },
    },
  };
}
```

### Route Handlers

**Keep route handlers focused:**

```javascript
// ✅ Good - Clean and focused
set: () => {
  this.routes["/products"] = () => {
    this.loadProductsPage();
  };
};

// ❌ Avoid - Too much logic
set: () => {
  this.routes["/products"] = () => {
    // 100 lines of code...
  };
};
```

**Extract complex logic to methods:**

```javascript
export class AppRouter {
  private routes = {};

  public methods = {
    products: {
      go: () => Router.navigate("/products"),
      set: () => {
        this.routes["/products"] = () => this.loadProducts();
      },
    },
  };

  private loadProducts() {
    this.clearContent();
    this.showLoader();

    fetch("/api/products")
      .then(data => this.renderProducts(data))
      .finally(() => this.hideLoader());
  }

  private renderProducts(data) {
    // Rendering logic
  }
}
```

### Lifecycle Hooks

**Use hooks for global behavior:**

```javascript
Router.createRoutes(this.routes, {
  onGo(route) {
    // Global actions on navigation
    this.updateBreadcrumbs(route);
    this.trackAnalytics(route);
    this.closeModals();
    window.scrollTo(0, 0);
  },

  onBack(route) {
    // Global actions on back navigation
    this.restoreState(route);
    this.updateHistory(route);
  },
});
```

---

## Common Patterns

### Protected Routes

```javascript
export class AppRouter {
  private isAuthenticated() {
    return W.getMutable("userToken") !== null;
  }

  public methods = {
    dashboard: {
      go: () => Router.navigate("/dashboard"),
      set: () => {
        this.routes["/dashboard"] = () => {
          if (!this.isAuthenticated()) {
            this.methods.login.go();
            return;
          }
          loadDashboard();
        };
      },
    },

    login: {
      go: () => Router.navigate("/login"),
      set: () => {
        this.routes["/login"] = () => loadLogin();
      },
    },
  };
}
```

### Route Parameters

```javascript
export class AppRouter {
  public methods = {
    userProfile: {
      go: (userId) => Router.navigate(`/user/${userId}`),
      set: () => {
        this.routes["/user/:id"] = () => {
          const userId = this.getRouteParam("id");
          loadUserProfile(userId);
        };
      },
    },
  };

  private getRouteParam(param) {
    const path = window.location.pathname;
    // Parse parameter from path
    return path.split("/").pop();
  }
}

// Usage
router.methods.userProfile.go("123");
```

### Nested Navigation

```javascript
export class AppRouter {
  public methods = {
    products: {
      go: () => Router.navigate("/products"),
      set: () => {
        this.routes["/products"] = () => {
          this.loadProductsLayout();
          this.methods.productsList.go();
        };
      },
    },

    productsList: {
      go: () => Router.navigate("/products/list"),
      set: () => {
        this.routes["/products/list"] = () => {
          this.loadProductsList();
        };
      },
    },

    productDetail: {
      go: (id) => Router.navigate(`/products/${id}`),
      set: () => {
        this.routes["/products/:id"] = () => {
          const productId = this.getProductId();
          this.loadProductDetail(productId);
        };
      },
    },
  };
}
```

### Breadcrumbs Management

```javascript
Router.createRoutes(this.routes, {
  onGo(route) {
    const breadcrumbs = this.generateBreadcrumbs(route);
    this.updateBreadcrumbsUI(breadcrumbs);
  },
});

private generateBreadcrumbs(route) {
  const parts = route.split("/").filter(Boolean);
  return parts.map((part, index) => ({
    label: this.formatLabel(part),
    path: "/" + parts.slice(0, index + 1).join("/"),
  }));
}
```

### Page Transitions

```javascript
Router.createRoutes(this.routes, {
  onGo(route) {
    // Fade out
    document.querySelector("#app").classList.add("fade-out");

    setTimeout(() => {
      // Load content
      this.loadRoute(route);

      // Fade in
      document.querySelector("#app").classList.remove("fade-out");
      document.querySelector("#app").classList.add("fade-in");
    }, 300);
  },
});
```
