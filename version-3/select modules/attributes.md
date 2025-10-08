# WinnetouJS Attributes Module

The attributes module provides comprehensive functions for working with element attributes, form data, and input values in WinnetouJS applications. All functions support multiple selector types for flexible element targeting.

## Import

```javascript
import {
  getValue,
  setValue,
  setAttr,
  getAttr,
  isChecked,
  getFile,
  getFiles,
  disableElement,
  enableElement,
  setData,
  getData,
  removeData,
  clearForm,
  serializeForm,
  resetForm,
  insertTextAtCursor,
} from "winnetoujs/modules/select";
```

## Selector Types

All functions accept the following selector types:

- **String**: CSS selector (`"#myId"`, `".myClass"`, `"div"`, `"#id1, #id2"`)
- **Element**: Single DOM element
- **Element[]**: Array of DOM elements

## Value Management

### getValue(selector)

Gets the value of an input element.

**Parameters:**

- `selector` (Selector): The input element to get value from

**Returns:**

- `string`: The value of the input element

**Example:**

```javascript
import { getValue } from "winnetoujs/modules/select";

// Get value from input field
const username = getValue("#username");
console.log(username); // "john_doe"

// Get value using constructo ID
const input = new $myInput().create("#app");
const value = getValue(input.ids.myInput);
```

### setValue(selector, value)

Sets the value of input elements and triggers change events.

**Parameters:**

- `selector` (Selector): The input element(s) to set value for
- `value` (string): The value to set

**Example:**

```javascript
import { setValue } from "winnetoujs/modules/select";

// Set value and trigger change event
setValue("#email", "user@example.com");

// Set multiple input values
setValue(".form-input", "default value");

// Set value programmatically
const userEmail = await fetchUserEmail();
setValue("#email", userEmail);
```

**Behavior:**

- Sets the `value` property of input elements
- Automatically triggers `change` event
- Works with cross-browser event handling

## Attributes

### setAttr(selector, attr, value)

Sets an attribute on elements.

**Parameters:**

- `selector` (Selector): The element(s) to set attribute on
- `attr` (string): The attribute name
- `value` (string): The attribute value

**Example:**

```javascript
import { setAttr } from "winnetoujs/modules/select";

// Set data attribute
setAttr("#myElement", "data-id", "123");

// Set multiple attributes
setAttr(".buttons", "disabled", "true");
setAttr(".buttons", "aria-label", "Action button");

// Set custom attributes
setAttr("#chart", "data-config", JSON.stringify(chartConfig));
```

### getAttr(selector, attr)

Gets an attribute value from an element.

**Parameters:**

- `selector` (Selector): The element to get attribute from
- `attr` (string): The attribute name

**Returns:**

- `string | null`: The attribute value, or null if not found

**Example:**

```javascript
import { getAttr } from "winnetoujs/modules/select";

// Get data attribute
const userId = getAttr("#userCard", "data-user-id");

// Get aria attribute
const label = getAttr("#button", "aria-label");

// Check if attribute exists
if (getAttr("#element", "data-loaded") !== null) {
  // Attribute exists
}
```

## Form Elements

### isChecked(selector)

Checks if a checkbox or radio button is checked.

**Parameters:**

- `selector` (Selector): The checkbox/radio element to check

**Returns:**

- `boolean`: True if checked, false otherwise

**Example:**

```javascript
import { isChecked } from "winnetoujs/modules/select";

// Check if checkbox is selected
if (isChecked("#agreementCheckbox")) {
  submitForm();
}

// Check radio button selection
const isPremium = isChecked("#premiumPlan");
```

### disableElement(selector) / enableElement(selector)

Disables or enables form elements.

**Parameters:**

- `selector` (Selector): The element(s) to disable/enable

**Example:**

```javascript
import { disableElement, enableElement } from "winnetoujs/modules/select";

// Disable submit button during processing
disableElement("#submitBtn");

// Enable after processing
enableElement("#submitBtn");

// Conditional enabling
if (isFormValid()) {
  enableElement("#submitBtn");
} else {
  disableElement("#submitBtn");
}
```

## File Handling

### getFile(selector)

Gets the first selected file from a file input.

**Parameters:**

- `selector` (Selector): The file input element

**Returns:**

- `File | undefined`: The first selected file, or undefined

**Example:**

```javascript
import { getFile } from "winnetoujs/modules/select";

// Handle file upload
const file = getFile("#fileUpload");
if (file) {
  console.log(`Selected: ${file.name}, Size: ${file.size} bytes`);
  uploadFile(file);
}
```

### getFiles(selector)

Gets all selected files from a file input.

**Parameters:**

- `selector` (Selector): The file input element

**Returns:**

- `FileList | null`: All selected files, or null

**Example:**

```javascript
import { getFiles } from "winnetoujs/modules/select";

// Handle multiple file upload
const files = getFiles("#multiFileUpload");
if (files && files.length > 0) {
  Array.from(files).forEach(file => {
    console.log(`File: ${file.name}`);
    uploadFile(file);
  });
}
```

## Data Attributes

### setData(selector, key, value)

Sets a data attribute on elements.

**Parameters:**

- `selector` (Selector): The element(s) to set data attribute on
- `key` (string): The data attribute key (without 'data-' prefix)
- `value` (string): The data attribute value

**Example:**

```javascript
import { setData } from "winnetoujs/modules/select";

// Set data attributes
setData("#userCard", "userId", "123");
setData("#product", "price", "99.99");

// Set complex data
setData("#config", "settings", JSON.stringify(appSettings));
```

### getData(selector, key)

Gets a data attribute value from an element.

**Parameters:**

- `selector` (Selector): The element to get data attribute from
- `key` (string): The data attribute key (without 'data-' prefix)

**Returns:**

- `string | undefined`: The data attribute value, or undefined

**Example:**

```javascript
import { getData } from "winnetoujs/modules/select";

// Get data attributes
const userId = getData("#userCard", "userId");
const price = parseFloat(getData("#product", "price") || "0");

// Get complex data
const settings = JSON.parse(getData("#config", "settings") || "{}");
```

### removeData(selector, key)

Removes a data attribute from elements.

**Parameters:**

- `selector` (Selector): The element(s) to remove data attribute from
- `key` (string): The data attribute key to remove

**Example:**

```javascript
import { removeData } from "winnetoujs/modules/select";

// Clean up temporary data
removeData("#tempElement", "tempId");
removeData(".cached-items", "cacheTime");
```

## Form Operations

### clearForm(selector)

Clears all input values in a form.

**Parameters:**

- `selector` (Selector): The form element to clear

**Example:**

```javascript
import { clearForm } from "winnetoujs/modules/select";

// Clear entire form
clearForm("#contactForm");

// Clear after successful submission
onClick("#resetBtn", () => {
  clearForm("#userForm");
});
```

**Behavior:**

- Clears text inputs, textareas
- Unchecks checkboxes and radio buttons
- Resets select elements to no selection

### resetForm(selector)

Resets a form to its initial state.

**Parameters:**

- `selector` (Selector): The form element to reset

**Example:**

```javascript
import { resetForm } from "winnetoujs/modules/select";

// Reset to original values
resetForm("#editForm");

// Reset with user confirmation
onClick("#cancelBtn", () => {
  if (confirm("Discard changes?")) {
    resetForm("#userForm");
  }
});
```

### serializeForm(selector)

Serializes form data into a JavaScript object.

**Parameters:**

- `selector` (Selector): The form element to serialize

**Returns:**

- `Record<string, string | string[]>`: Object with form field names as keys

**Example:**

```javascript
import { serializeForm } from "winnetoujs/modules/select";

// Serialize form data
const formData = serializeForm("#contactForm");
console.log(formData);
// { name: "John", email: "john@example.com", interests: ["tech", "sports"] }

// Submit serialized data
onClick("#submitBtn", async () => {
  const data = serializeForm("#userForm");
  await submitUserData(data);
});
```

**Behavior:**

- Handles multiple values for same field name (arrays)
- Includes all form controls with names
- Returns empty object for non-form elements

## Text Manipulation

### insertTextAtCursor(selector, text)

Inserts text at the current cursor position in input/textarea elements.

**Parameters:**

- `selector` (Selector): The input/textarea element
- `text` (string): The text to insert

**Example:**

```javascript
import { insertTextAtCursor } from "winnetoujs/modules/select";

// Insert text at cursor
insertTextAtCursor("#messageInput", " @username");

// Insert template text
onClick("#templateBtn", () => {
  insertTextAtCursor("#emailBody", "Dear Customer,\n\n");
});

// Insert dynamic content
const insertUserMention = username => {
  insertTextAtCursor("#chatInput", `@${username} `);
};
```

**Behavior:**

- Preserves cursor position after insertion
- Works with both input and textarea elements
- Automatically focuses the element after insertion

## Usage with WinnetouJS

Integration with WinnetouJS constructos and reactive system:

```javascript
import { $userForm, $notification } from "./components.wcto";
import {
  setValue,
  getValue,
  serializeForm,
  clearForm,
} from "winnetoujs/modules/select";
import { W } from "winnetoujs";

// Create form with reactive behavior
const form = new $userForm({
  onSubmit: W.fx(async () => {
    const formData = serializeForm(form.ids.userForm);

    try {
      await submitUser(formData);
      new $notification({
        message: "User saved successfully!",
        type: "success",
      }).create("#notifications");
      clearForm(form.ids.userForm);
    } catch (error) {
      new $notification({
        message: "Error saving user",
        type: "error",
      }).create("#notifications");
    }
  }),
}).create("#app");

// Set default values
setValue(form.ids.country, "US");
setValue(form.ids.newsletter, "true");
```

## Performance Notes

- Value setters automatically trigger change events for reactive updates
- File operations work with modern File API
- Data attribute operations use efficient dataset API
- Form serialization uses FormData for optimal performance

## Browser Compatibility

All functions work in modern browsers supporting:

- ES6+ features
- File API (for file operations)
- Dataset API (for data attributes)
- Standard DOM events
