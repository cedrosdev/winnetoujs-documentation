# Core API

The core WinnetouJs API provides essential methods for state management and event handling through the `W` object.

## Import

```javascript
import { W } from "winnetoujs";
```

---

## State Management

### W.setMutable()

Creates or updates a persistent mutable stored in localStorage.

**Signature:**

```typescript
W.setMutable(key: string, value: string): void
```

**Parameters:**

- `key` - Unique identifier for the mutable
- `value` - The value to store (string)

**Returns:** `void`

**Usage:**

```javascript
W.setMutable("username", "John Doe");
W.setMutable("theme", "dark");
```

**Features:**

- Stored in localStorage
- Survives page refreshes
- Automatically updates connected constructos
- Available across browser tabs

---

### W.getMutable()

Retrieves the current value of a mutable.

**Signature:**

```typescript
W.getMutable(key: string): string
```

**Parameters:**

- `key` - The mutable identifier

**Returns:** `string` - The mutable's current value

**Usage:**

```javascript
W.setMutable("username", "Jane");
const username = W.getMutable("username"); // "Jane"
```

---

### W.initMutable()

Creates a non-persistent mutable that exists only in memory.

**Signature:**

```typescript
W.initMutable(initialValue: string): string
```

**Parameters:**

- `initialValue` - The starting value for the mutable

**Returns:** `string` - A unique key to reference this mutable

**Usage:**

```javascript
const loadingState = W.initMutable("loading");
const searchQuery = W.initMutable("");
const errorMessage = W.initMutable(null);

// Later access it
console.log(W.getMutable(loadingState)); // "loading"
```

**Features:**

- Not stored in localStorage
- Exists only during current session
- Lightweight and fast
- Perfect for temporary UI states
- Automatically updates connected constructos

---

### W.setMutableNotPersistent()

Updates a non-persistent mutable value.

**Signature:**

```typescript
W.setMutableNotPersistent(key: string, value: string): void
```

**Parameters:**

- `key` - The mutable identifier (returned from `initMutable()`)
- `value` - The new value

**Returns:** `void`

**Usage:**

```javascript
const status = W.initMutable("idle");

W.setMutableNotPersistent(status, "loading");
W.setMutableNotPersistent(status, "success");
```

---

## Event Handling

### W.fx()

Creates an event handler function for constructos with optional arguments.

**Signature:**

```typescript
W.fx(handler: Function, ...args: any[]): string
```

**Parameters:**

- `handler` - The function to execute when the event fires
- `...args` - Optional arguments to pass to the handler
  - Use `"this"` as an argument to pass the element itself

**Returns:** `string` - Encoded function reference for constructo

**Usage:**

**Basic event handler:**

```javascript
new $myBtn({
  onclick: W.fx(() => {
    console.log("button pressed");
  }),
}).create("#app");
```

**Access element with "this":**

```javascript
new $myInput({
  onchange: W.fx(self => {
    console.log("Input value:", self.value);
    self.style.color = "blue";
  }, "this"),
}).create("#app");
```

**Multiple arguments:**

```javascript
new $actionBtn({
  text: "Update",
  onclick: W.fx(
    (self, targetId, message) => {
      self.style.backgroundColor = "green";
      document.getElementById(targetId).textContent = message;
    },
    "this", // First arg: the button element
    "result", // Second arg: target element ID
    "Success!" // Third arg: message
  ),
}).create("#app");
```

**Important Notes:**

- Does not pass the default DOM event object
- Arguments are passed in the order specified
- `"this"` refers to the DOM element that triggered the event

---

## W.strings

Object containing translation strings for the application.

**Signature:**

```typescript
W.strings: Record<string, string>
```

**Usage:**

```javascript
import { W } from "winnetoujs";

export default W.strings = {
  appTitle: "My App",
  welcomeMessage: "Welcome!",
  save: "Save",
  cancel: "Cancel",
};
```

**Features:**

- Used by the translations module
- Provides IDE navigation support (F12)
- Base language definition
- Type-safe string references

**See Also:**

- [Translations API](#) for language switching
- [Native Translations Documentation](#) for complete guide

---

## Best Practices

### Mutables

**Use persistent mutables for:**

- User preferences
- Authentication state
- App settings
- Data that should survive page refresh

**Use non-persistent mutables for:**

- Loading states
- Form validation errors
- Temporary UI states
- Modal visibility
- Search queries during a session

### Event Handlers

**Keep handlers simple:**

```javascript
// ✅ Good
W.fx(() => handleClick());

// ❌ Avoid complex logic
W.fx(() => {
  // 50 lines of code...
});
```

**Use "this" for element access:**

```javascript
// ✅ Good
W.fx(self => {
  self.classList.toggle("active");
}, "this");

// ❌ Avoid document queries
W.fx(() => {
  document.getElementById("myBtn").classList.toggle("active");
});
```

**Pass data as arguments:**

```javascript
// ✅ Good
W.fx((id, data) => update(id, data), userId, userData);

// ❌ Avoid closures when possible
W.fx(() => update(userId, userData));
```

---

## Common Patterns

### Loading State Pattern

```javascript
const loading = W.initMutable("false");

new $spinner({
  visible: { mutable: loading },
}).create("#app");

async function fetchData() {
  W.setMutableNotPersistent(loading, "true");
  const data = await api.getData();
  W.setMutableNotPersistent(loading, "false");
  return data;
}
```

### Form Validation Pattern

```javascript
const emailError = W.initMutable("");

new $inputField({
  error: { mutable: emailError },
  onblur: W.fx(input => {
    const isValid = validateEmail(input.value);
    W.setMutableNotPersistent(emailError, isValid ? "" : "Invalid email");
  }, "this"),
}).create("#form");
```

### Toggle Pattern

```javascript
W.setMutable("sidebarOpen", "false");

new $sidebar({
  class: { mutable: "sidebarOpen" },
}).create("#app");

new $toggleBtn({
  onclick: W.fx(() => {
    const current = W.getMutable("sidebarOpen");
    W.setMutable("sidebarOpen", current === "true" ? "false" : "true");
  }),
}).create("#header");
```
