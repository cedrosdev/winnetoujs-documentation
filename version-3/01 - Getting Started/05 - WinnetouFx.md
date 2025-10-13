# WinnetouFx: Event Handling in Constructos

## Overview

WinnetouFx, accessed through `W.fx()`, is WinnetouJs's powerful event handling system that allows you to attach JavaScript functions to constructo events like clicks, changes, submissions, and more. Unlike traditional event handlers, WinnetouFx provides a clean, type-safe way to pass functions and arguments to your constructos.

**Key Features:**

- 🎯 Clean event handler syntax
- 🔧 Pass custom arguments to handlers
- 📌 Access to the element itself with `"this"`
- ⚡ No default event object (explicit parameter passing)
- 🎨 Works with all standard DOM events

## Basic Usage

### Simple Event Handler

The most basic use of `W.fx()` is to pass a function to an event prop in your constructo:

```html
<!-- buttons.wcto.html -->
<winnetou description="A clickable button">
  <button id="[[myBtn]]" onclick="{{onclick}}">Click me</button>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $myBtn } from "./buttons.wcto";

new $myBtn({
  onclick: W.fx(() => {
    console.log("button pressed");
  }),
}).create("#app");
```

**Important:** WinnetouFx does not pass the default DOM event object. This is intentional for cleaner, more predictable function signatures.

### Syntax

```javascript
W.fx(handler: Function, ...args: any[])
```

- `handler` - The function to execute when the event fires
- `...args` - Optional arguments to pass to the handler

## Accessing the Element with "this"

To access the element that triggered the event, pass `"this"` as a parameter to `W.fx()`:

```html
<!-- inputs.wcto.html -->
<winnetou description="An input field">
  <input
    id="[[myInput]]"
    type="text"
    placeholder="{{placeholder}}"
    onchange="{{onchange}}" />
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $myInput } from "./inputs.wcto";

new $myInput({
  placeholder: "Type something...",
  onchange: W.fx(self => {
    console.log("Input value:", self.value);
    self.style.color = "blue";
  }, "this"),
}).create("#app");
```

The `"this"` keyword refers to the DOM element that triggered the event. In the handler function, it's passed as the first parameter (commonly named `self`, `element`, or `el`).

## Passing Multiple Arguments

WinnetouFx allows you to pass multiple arguments to your event handlers:

```html
<!-- buttons.wcto.html -->
<winnetou description="Button with multiple event args">
  <button id="[[actionBtn]]" onclick="{{onclick}}">{{text}}</button>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $actionBtn } from "./buttons.wcto";

new $actionBtn({
  text: "Update Elements",
  onclick: W.fx(
    (self, targetId, message) => {
      // self is the button element
      self.style.backgroundColor = "green";

      // Access other elements using passed IDs
      const targetElement = document.getElementById(targetId);
      targetElement.textContent = message;
    },
    "this", // First arg: the button element
    "result", // Second arg: target element ID
    "Success!" // Third arg: message to display
  ),
}).create("#app");
```

### Argument Order

When using `W.fx()`, arguments are passed in the order you specify:

```javascript
W.fx(handler, arg1, arg2, arg3, ...)
```

The handler receives them in the same order:

```javascript
(arg1, arg2, arg3, ...) => { /* handler code */ }
```

## Common Event Types

WinnetouFx works with all standard DOM events. Here are the most common:

### Click Events

```html
<winnetou>
  <button id="[[clickBtn]]" onclick="{{onClick}}">Click Me</button>
</winnetou>
```

```javascript
new $clickBtn({
  onClick: W.fx(() => {
    console.log("Clicked!");
  }),
}).create("#app");
```

### Change Events

```html
<winnetou>
  <select id="[[dropdown]]" onchange="{{onChange}}">
    <option value="1">Option 1</option>
    <option value="2">Option 2</option>
  </select>
</winnetou>
```

```javascript
new $dropdown({
  onChange: W.fx(select => {
    console.log("Selected:", select.value);
  }, "this"),
}).create("#app");
```

### Input Events

```html
<winnetou>
  <input
    id="[[searchBox]]"
    type="text"
    oninput="{{onInput}}"
    placeholder="Search..." />
</winnetou>
```

```javascript
new $searchBox({
  onInput: W.fx(input => {
    console.log("Current value:", input.value);
    performSearch(input.value);
  }, "this"),
}).create("#app");

function performSearch(query) {
  console.log(`Searching for: ${query}`);
}
```

### Submit Events

```html
<winnetou>
  <form id="[[loginForm]]" onsubmit="{{onSubmit}}">
    <input id="[[username]]" type="text" placeholder="Username" />
    <input id="[[password]]" type="password" placeholder="Password" />
    <button type="submit">Login</button>
  </form>
</winnetou>
```

```javascript
new $loginForm({
  onSubmit: W.fx(form => {
    // Prevent default form submission
    const username = form.querySelector('input[type="text"]').value;
    const password = form.querySelector('input[type="password"]').value;

    handleLogin(username, password);
    return false; // Prevent form submission
  }, "this"),
}).create("#app");

function handleLogin(username, password) {
  console.log(`Logging in: ${username}`);
}
```

### Mouse Events

```html
<winnetou>
  <div
    id="[[hoverBox]]"
    onmouseenter="{{onMouseEnter}}"
    onmouseleave="{{onMouseLeave}}">
    {{content}}
  </div>
</winnetou>
```

```javascript
new $hoverBox({
  content: "Hover over me!",
  onMouseEnter: W.fx(box => {
    box.style.backgroundColor = "lightblue";
  }, "this"),
  onMouseLeave: W.fx(box => {
    box.style.backgroundColor = "white";
  }, "this"),
}).create("#app");
```

## Real-World Examples

### Example 1: Interactive Counter

```html
<!-- counter.wcto.html -->
<winnetou description="Counter component">
  <div id="[[counter]]" class="counter">
    <button id="[[decrementBtn]]" onclick="{{onDecrement}}">-</button>
    <span id="[[countDisplay]]">{{count}}</span>
    <button id="[[incrementBtn]]" onclick="{{onIncrement}}">+</button>
    <button id="[[resetBtn]]" onclick="{{onReset}}">Reset</button>
  </div>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $counter } from "./counter.wcto";

let count = 0;

const counterComponent = new $counter({
  count: count.toString(),
  onIncrement: W.fx(
    (btn, displayId) => {
      count++;
      updateDisplay(displayId);
    },
    "this",
    "countDisplay"
  ),

  onDecrement: W.fx(
    (btn, displayId) => {
      count--;
      updateDisplay(displayId);
    },
    "this",
    "countDisplay"
  ),

  onReset: W.fx(
    (btn, displayId) => {
      count = 0;
      updateDisplay(displayId);
    },
    "this",
    "countDisplay"
  ),
}).create("#app");

function updateDisplay(displayId) {
  const display = document.getElementById(counterComponent.ids[displayId]);
  display.textContent = count.toString();
}
```

### Example 2: Todo List with Event Handlers

```html
<!-- todo.wcto.html -->
<winnetou description="Todo list container">
  <div id="[[todoContainer]]" class="todo-app">
    <input
      id="[[todoInput]]"
      type="text"
      placeholder="Add new todo..."
      onkeypress="{{onKeyPress}}" />
    <button id="[[addBtn]]" onclick="{{onAdd}}">Add</button>
    <ul id="[[todoList]]"></ul>
  </div>
</winnetou>

<winnetou description="Todo item">
  <li id="[[todoItem]]" class="todo-item">
    <span id="[[todoText]]">{{text}}</span>
    <button onclick="{{onDelete}}">Delete</button>
    <button onclick="{{onToggle}}">{{toggleText}}</button>
  </li>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $todoContainer, $todoItem } from "./todo.wcto";

let todos = [];

const todoApp = new $todoContainer({
  onKeyPress: W.fx(
    (input, listId) => {
      if (event.key === "Enter") {
        addTodo(input.value, listId);
        input.value = "";
      }
    },
    "this",
    "todoList"
  ),

  onAdd: W.fx(
    (btn, inputId, listId) => {
      const input = document.getElementById(todoApp.ids[inputId]);
      if (input.value.trim()) {
        addTodo(input.value, listId);
        input.value = "";
      }
    },
    "this",
    "todoInput",
    "todoList"
  ),
}).create("#app");

function addTodo(text, listId) {
  const todo = {
    id: Date.now(),
    text: text,
    completed: false,
  };

  todos.push(todo);
  renderTodo(todo, listId);
}

function renderTodo(todo, listId) {
  const listElement = document.getElementById(todoApp.ids[listId]);

  new $todoItem({
    text: todo.text,
    toggleText: todo.completed ? "Undo" : "Complete",

    onDelete: W.fx(btn => {
      todos = todos.filter(t => t.id !== todo.id);
      btn.closest("li").remove();
    }, "this"),

    onToggle: W.fx(
      (btn, textId) => {
        todo.completed = !todo.completed;
        btn.textContent = todo.completed ? "Undo" : "Complete";

        const textSpan = btn.parentElement.querySelector("span");
        textSpan.style.textDecoration = todo.completed
          ? "line-through"
          : "none";
      },
      "this",
      "todoText"
    ),
  }).create(`#${listElement.id}`);
}
```

### Example 3: Form Validation

```html
<!-- form.wcto.html -->
<winnetou description="Registration form">
  <form id="[[registrationForm]]" class="form">
    <div class="form-group">
      <input
        id="[[emailInput]]"
        type="email"
        placeholder="Email"
        oninput="{{onEmailInput}}"
        onblur="{{onEmailBlur}}" />
      <span id="[[emailError]]" class="error"></span>
    </div>

    <div class="form-group">
      <input
        id="[[passwordInput]]"
        type="password"
        placeholder="Password"
        oninput="{{onPasswordInput}}" />
      <span id="[[passwordError]]" class="error"></span>
    </div>

    <button id="[[submitBtn]]" type="button" onclick="{{onSubmit}}">
      Register
    </button>
  </form>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $registrationForm } from "./form.wcto";

const formState = {
  email: "",
  password: "",
  emailValid: false,
  passwordValid: false,
};

const form = new $registrationForm({
  onEmailInput: W.fx(
    (input, errorId) => {
      formState.email = input.value;
      validateEmail(input.value, errorId);
    },
    "this",
    "emailError"
  ),

  onEmailBlur: W.fx(
    (input, errorId) => {
      if (!input.value) {
        showError(errorId, "Email is required");
      }
    },
    "this",
    "emailError"
  ),

  onPasswordInput: W.fx(
    (input, errorId) => {
      formState.password = input.value;
      validatePassword(input.value, errorId);
    },
    "this",
    "passwordError"
  ),

  onSubmit: W.fx(
    (btn, emailInputId, passwordInputId) => {
      if (formState.emailValid && formState.passwordValid) {
        submitForm();
      } else {
        alert("Please fix the errors before submitting");
      }
    },
    "this",
    "emailInput",
    "passwordInput"
  ),
}).create("#app");

function validateEmail(email, errorId) {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;

  if (!email) {
    formState.emailValid = false;
    showError(errorId, "");
  } else if (!emailRegex.test(email)) {
    formState.emailValid = false;
    showError(errorId, "Invalid email format");
  } else {
    formState.emailValid = true;
    showError(errorId, "");
  }
}

function validatePassword(password, errorId) {
  if (!password) {
    formState.passwordValid = false;
    showError(errorId, "");
  } else if (password.length < 8) {
    formState.passwordValid = false;
    showError(errorId, "Password must be at least 8 characters");
  } else {
    formState.passwordValid = true;
    showError(errorId, "");
  }
}

function showError(errorId, message) {
  const errorElement = document.getElementById(form.ids[errorId]);
  errorElement.textContent = message;
}

function submitForm() {
  console.log("Form submitted:", formState);
  alert(`Registration successful for ${formState.email}`);
}
```

### Example 4: Image Gallery with Modal

```html
<!-- gallery.wcto.html -->
<winnetou description="Image gallery">
  <div id="[[gallery]]" class="gallery">
    <div id="[[imagesContainer]]" class="images-container"></div>
  </div>
</winnetou>

<winnetou description="Gallery image thumbnail">
  <div id="[[thumbnail]]" class="thumbnail" onclick="{{onClick}}">
    <img src="{{src}}" alt="{{alt}}" />
  </div>
</winnetou>

<winnetou description="Modal overlay">
  <div id="[[modal]]" class="modal" onclick="{{onClose}}">
    <div class="modal-content">
      <span id="[[closeBtn]]" class="close" onclick="{{onClose}}">&times;</span>
      <img id="[[modalImage]]" src="{{src}}" alt="{{alt}}" />
      <p id="[[caption]]">{{caption}}</p>
    </div>
  </div>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $gallery, $thumbnail, $modal } from "./gallery.wcto";

const images = [
  { src: "/images/photo1.jpg", alt: "Photo 1", caption: "Beautiful sunset" },
  { src: "/images/photo2.jpg", alt: "Photo 2", caption: "Mountain view" },
  { src: "/images/photo3.jpg", alt: "Photo 3", caption: "City lights" },
];

// Create gallery
const gallery = new $gallery().create("#app");

// Add thumbnails
images.forEach(image => {
  new $thumbnail({
    src: image.src,
    alt: image.alt,
    onClick: W.fx(
      (thumb, imageSrc, imageAlt, imageCaption) => {
        openModal(imageSrc, imageAlt, imageCaption);
      },
      "this",
      image.src,
      image.alt,
      image.caption
    ),
  }).create(`#${gallery.ids.imagesContainer}`);
});

let currentModal = null;

function openModal(src, alt, caption) {
  // Remove existing modal if present
  if (currentModal) {
    document.getElementById(currentModal.ids.modal).remove();
  }

  // Create new modal
  currentModal = new $modal({
    src: src,
    alt: alt,
    caption: caption,
    onClose: W.fx(element => {
      element.closest(".modal").remove();
      currentModal = null;
    }, "this"),
  }).create("body");

  // Show modal
  document.getElementById(currentModal.ids.modal).style.display = "flex";
}
```

### Example 5: Dynamic Search with Debouncing

```html
<!-- search.wcto.html -->
<winnetou description="Search component">
  <div id="[[searchComponent]]" class="search">
    <input
      id="[[searchInput]]"
      type="text"
      placeholder="Search..."
      oninput="{{onInput}}" />
    <div id="[[resultsContainer]]" class="results"></div>
  </div>
</winnetou>

<winnetou description="Search result item">
  <div id="[[resultItem]]" class="result-item" onclick="{{onClick}}">
    <h4>{{title}}</h4>
    <p>{{description}}</p>
  </div>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $searchComponent, $resultItem } from "./search.wcto";

let debounceTimer;
let searchResults = [];

const search = new $searchComponent({
  onInput: W.fx(
    (input, resultsId) => {
      const query = input.value.trim();

      // Clear previous timer
      clearTimeout(debounceTimer);

      // Debounce search by 300ms
      debounceTimer = setTimeout(() => {
        if (query.length > 2) {
          performSearch(query, resultsId);
        } else {
          clearResults(resultsId);
        }
      }, 300);
    },
    "this",
    "resultsContainer"
  ),
}).create("#app");

async function performSearch(query, resultsId) {
  console.log(`Searching for: ${query}`);

  // Simulate API call
  await new Promise(resolve => setTimeout(resolve, 500));

  // Mock results
  searchResults = [
    { id: 1, title: `Result for "${query}" #1`, description: "Description 1" },
    { id: 2, title: `Result for "${query}" #2`, description: "Description 2" },
    { id: 3, title: `Result for "${query}" #3`, description: "Description 3" },
  ];

  displayResults(resultsId);
}

function displayResults(resultsId) {
  const container = document.getElementById(search.ids[resultsId]);
  container.innerHTML = ""; // Clear previous results

  searchResults.forEach(result => {
    new $resultItem({
      title: result.title,
      description: result.description,
      onClick: W.fx(
        (item, resultData) => {
          console.log("Selected:", resultData);
          alert(`You selected: ${resultData.title}`);
        },
        "this",
        result
      ),
    }).create(`#${container.id}`);
  });
}

function clearResults(resultsId) {
  const container = document.getElementById(search.ids[resultsId]);
  container.innerHTML = "";
  searchResults = [];
}
```

## Combining WinnetouFx with Mutables

WinnetouFx works seamlessly with mutables for reactive state management:

```html
<!-- toggle.wcto.html -->
<winnetou description="Toggle button">
  <button id="[[toggleBtn]]" class="btn {{btnClass}}" onclick="{{onClick}}">
    {{text}}
  </button>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $toggleBtn } from "./toggle.wcto";

// Create mutables
W.setMutable("isActive", "false");
W.setMutable("btnClass", "btn-inactive");
W.setMutable("btnText", "Activate");

// Create button with mutables
new $toggleBtn({
  btnClass: { mutable: "btnClass" },
  text: { mutable: "btnText" },
  onClick: W.fx(() => {
    toggleState();
  }),
}).create("#app");

function toggleState() {
  const isActive = W.getMutable("isActive") === "true";

  if (isActive) {
    W.setMutable("isActive", "false");
    W.setMutable("btnClass", "btn-inactive");
    W.setMutable("btnText", "Activate");
  } else {
    W.setMutable("isActive", "true");
    W.setMutable("btnClass", "btn-active");
    W.setMutable("btnText", "Deactivate");
  }
}
```

## Best Practices

### 1. Always Use "this" to Access the Element

When you need to manipulate the element that triggered the event, pass `"this"`:

```javascript
// ✅ Good - Access the element
new $button({
  onClick: W.fx(btn => {
    btn.disabled = true;
    btn.textContent = "Loading...";
  }, "this"),
}).create("#app");

// ❌ Avoid - Can't access the element
new $button({
  onClick: W.fx(() => {
    // No access to the button element
  }),
}).create("#app");
```

### 2. Pass IDs Instead of Direct References

Pass element IDs as arguments rather than direct element references:

```javascript
// ✅ Good - Pass ID as string
new $button({
  onClick: W.fx(
    (btn, targetId) => {
      const target = document.getElementById(targetId);
      target.style.color = "red";
    },
    "this",
    "resultDiv"
  ),
}).create("#app");

// ❌ Avoid - Don't pass element references
const element = document.getElementById("resultDiv");
new $button({
  onClick: W.fx(
    (btn, targetElement) => {
      targetElement.style.color = "red";
    },
    "this",
    element
  ), // May cause issues
}).create("#app");
```

### 3. Use Descriptive Function Names

Keep your event handlers readable:

```javascript
// ✅ Good
function handleUserLogin(username, password) {
  // Login logic
}

new $loginButton({
  onClick: W.fx(() => handleUserLogin(user, pass)),
}).create("#app");

// ❌ Avoid
new $loginButton({
  onClick: W.fx(() => {
    // 50 lines of inline code...
  }),
}).create("#app");
```

### 4. Prevent Default Behavior When Needed

For forms and links, remember to prevent default behavior:

```javascript
new $form({
  onSubmit: W.fx(form => {
    handleFormSubmit(form);
    return false; // Prevents form submission
  }, "this"),
}).create("#app");
```

### 5. Clean Up Event Listeners

When removing constructos, event listeners are automatically cleaned up. However, for long-running timers or intervals, clean them up manually:

```javascript
let intervalId;

new $component({
  onClick: W.fx(() => {
    // Clear previous interval if exists
    if (intervalId) clearInterval(intervalId);

    intervalId = setInterval(() => {
      updateData();
    }, 1000);
  }),
}).create("#app");

// Clean up when needed
function cleanup() {
  if (intervalId) clearInterval(intervalId);
}
```

## Common Patterns

### Pattern 1: Element Manipulation

```javascript
new $button({
  onClick: W.fx(btn => {
    btn.classList.add("clicked");
    btn.disabled = true;

    setTimeout(() => {
      btn.classList.remove("clicked");
      btn.disabled = false;
    }, 2000);
  }, "this"),
}).create("#app");
```

### Pattern 2: Conditional Actions

```javascript
new $button({
  onClick: W.fx(
    (btn, isEnabled) => {
      if (isEnabled) {
        performAction();
      } else {
        alert("Action is disabled");
      }
    },
    "this",
    true
  ),
}).create("#app");
```

### Pattern 3: Chained Updates

```javascript
new $input({
  onInput: W.fx(
    (input, displayId, errorId) => {
      const value = input.value;

      // Update display
      document.getElementById(displayId).textContent = value;

      // Validate and show error
      const error = validate(value);
      document.getElementById(errorId).textContent = error;
    },
    "this",
    "display",
    "error"
  ),
}).create("#app");
```

## Troubleshooting

### Issue: "this" Returns Unexpected Element

**Problem:** The `"this"` parameter doesn't return the element you expect.

**Solution:** Ensure `"this"` is the first argument after the handler function:

```javascript
// ✅ Correct
W.fx((element) => { ... }, "this")

// ❌ Wrong
W.fx("this", (element) => { ... })
```

### Issue: Can't Access Constructo IDs

**Problem:** Need to access element IDs from the constructo instance.

**Solution:** Store the constructo instance and use its `ids` property:

```javascript
const myComponent = new $component({
  onClick: W.fx(
    (btn, componentInstance) => {
      const targetId = componentInstance.ids.targetElement;
      document.getElementById(targetId).textContent = "Updated!";
    },
    "this",
    myComponent
  ),
}).create("#app");
```

### Issue: Event Not Firing

**Problem:** Your event handler doesn't seem to work.

**Solution:** Check that:

1. The event prop name matches the HTML attribute (onclick, onchange, etc.)
2. You're using `W.fx()` to wrap your handler
3. The constructo is actually created in the DOM

```javascript
// ✅ Correct
new $button({
  onclick: W.fx(() => { ... })  // lowercase 'onclick'
}).create("#app");

// ❌ Wrong - case mismatch
new $button({
  onClick: W.fx(() => { ... })  // This won't work if HTML uses 'onclick'
}).create("#app");
```

## Performance Tips

### 1. Avoid Creating Functions in Loops

```javascript
// ❌ Avoid - Creates new function each iteration
items.forEach(item => {
  new $item({
    onClick: W.fx(() => handleClick(item.id)),
  }).create("#list");
});

// ✅ Better - Pass data as arguments
items.forEach(item => {
  new $item({
    onClick: W.fx((btn, itemId) => handleClick(itemId), "this", item.id),
  }).create("#list");
});
```

### 2. Debounce Frequent Events

For events like `oninput` or `onmousemove`, use debouncing:

```javascript
let timer;
new $input({
  onInput: W.fx(input => {
    clearTimeout(timer);
    timer = setTimeout(() => {
      expensiveOperation(input.value);
    }, 300);
  }, "this"),
}).create("#app");
```

### 3. Use Event Delegation for Large Lists

Instead of attaching handlers to many items, use a single handler on the container:

```javascript
// Create container with single handler
const list = new $list({
  onClick: W.fx((container, evt) => {
    const target = evt.target.closest(".item");
    if (target) {
      handleItemClick(target.dataset.id);
    }
  }, "this"),
}).create("#app");

// Add items without individual handlers
items.forEach(item => {
  // Items don't need individual onclick handlers
  new $listItem({
    text: item.name,
    dataId: item.id,
  }).create(`#${list.ids.listContainer}`);
});
```

## Summary

WinnetouFx provides a clean, powerful way to handle events in WinnetouJs:

- ✅ Use `W.fx()` to wrap event handler functions
- ✅ Pass `"this"` as a parameter to access the triggering element
- ✅ Pass additional arguments after the handler function
- ✅ WinnetouFx does not pass the default event object
- ✅ Works with all standard DOM events (onclick, onchange, oninput, etc.)
- ✅ Combine with mutables for reactive state management
- ✅ Store constructo instances to access their IDs in handlers
- ✅ Follow best practices for clean, maintainable code
- ✅ Use debouncing for frequent events
- ✅ Pass IDs rather than direct element references

With WinnetouFx, you have everything you need to create interactive, event-driven applications in WinnetouJs!
