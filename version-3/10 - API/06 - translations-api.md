# Translations API

The Translations module provides internationalization (i18n) support for WinnetouJs applications.

## Import

```javascript
import {
  updateTranslations,
  changeLang,
} from "winnetoujs/modules/translations";
```

---

## Functions

### updateTranslations()

Initializes the translation system with base language and configuration.

**Signature:**

```typescript
updateTranslations(config: TranslationConfig): Promise<void>
```

**Parameters:**

- `config` - Configuration object
  - `stringsClass: Record<string, string>` - Base language object (from JS/TS file)
  - `translationsPublicPath: string` - Public path where JSON translation files are served

**Returns:** `Promise<void>` - Resolves when translations are initialized

**Usage:**

**With .then():**

```javascript
import { updateTranslations } from "winnetoujs/modules/translations";
import strings from "./translations/en-us";

updateTranslations({
  stringsClass: strings,
  translationsPublicPath: "/translations",
}).then(() => {
  startApp();
});

function startApp() {
  console.log("App started with translations");
  // Your app initialization
}
```

**With await:**

```javascript
import { updateTranslations } from "winnetoujs/modules/translations";
import strings from "./translations/en-us";

await updateTranslations({
  stringsClass: strings,
  translationsPublicPath: "/translations",
});

function startApp() {
  console.log("App started with translations");
  // Your app initialization
}

startApp();
```

**Important:** Always use `.then()` or `await` before starting your app to ensure translations are loaded.

---

### changeLang()

Changes the current application language.

**Signature:**

```typescript
changeLang(
  languageCode: string,
  reload?: boolean
): void
```

**Parameters:**

- `languageCode` - The language code matching the JSON filename (e.g., `"pt-br"`, `"es-es"`)
- `reload` - Optional. If `true`, reloads the page after changing language (default: `false`)

**Returns:** `void`

**Usage:**

**With page reload:**

```javascript
import { changeLang } from "winnetoujs/modules/translations";

changeLang("pt-br", true); // Changes to Portuguese and reloads page
changeLang("es-es", true); // Changes to Spanish and reloads page
changeLang("fr-fr", true); // Changes to French and reloads page
```

**Without page reload:**

```javascript
changeLang("pt-br", false); // Changes to Portuguese without reload
changeLang("es-es"); // Changes to Spanish without reload (default)
```

**Features:**

- Loads corresponding JSON file from `translationsPublicPath`
- Updates `W.strings` object
- Saves language preference to localStorage
- Optionally reloads page for complete UI update

**Important:** To use `changeLang()`, you must have a corresponding JSON file (e.g., `pt-br.json`) in the `translationsPublicPath` folder.

---

## Base Language Setup

### Creating Base Language File

Create your original language as a JavaScript or TypeScript file (not JSON):

**`src/translations/en-us.js`**

```javascript
import { W } from "winnetoujs";

export default W.strings = {
  // App general
  appTitle: "My Application",
  appDescription: "A powerful application",

  // Navigation
  home: "Home",
  about: "About",
  contact: "Contact",
  settings: "Settings",

  // Actions
  save: "Save",
  cancel: "Cancel",
  delete: "Delete",
  edit: "Edit",
  submit: "Submit",

  // Messages
  welcomeMessage: "Welcome!",
  successMessage: "Success",
  errorMessage: "Error occurred",
};
```

**TypeScript version:**

```typescript
import { W } from "winnetoujs";

export default W.strings = {
  appTitle: "My Application",
  appDescription: "A powerful application",
  home: "Home",
  about: "About",
  // ... more strings
} as const;
```

**Benefits of JS/TS base language:**

- F12 navigation to string definitions
- Reverse F12 to find string usage
- IDE autocomplete support
- Type safety with TypeScript
- Better refactoring support

---

## Translation Files

### JSON Translation Files

Create JSON files for additional languages in the public translations folder:

**`public/translations/pt-br.json`**

```json
{
  "appTitle": "Minha Aplicação",
  "appDescription": "Uma aplicação poderosa",
  "home": "Início",
  "about": "Sobre",
  "contact": "Contato",
  "settings": "Configurações",
  "save": "Salvar",
  "cancel": "Cancelar",
  "delete": "Excluir",
  "edit": "Editar",
  "submit": "Enviar",
  "welcomeMessage": "Bem-vindo!",
  "successMessage": "Sucesso",
  "errorMessage": "Ocorreu um erro"
}
```

**`public/translations/es-es.json`**

```json
{
  "appTitle": "Mi Aplicación",
  "appDescription": "Una aplicación potente",
  "home": "Inicio",
  "about": "Acerca de",
  "contact": "Contacto",
  "settings": "Configuración",
  "save": "Guardar",
  "cancel": "Cancelar",
  "delete": "Eliminar",
  "edit": "Editar",
  "submit": "Enviar",
  "welcomeMessage": "¡Bienvenido!",
  "successMessage": "Éxito",
  "errorMessage": "Ocurrió un error"
}
```

**Important:** Do NOT create a JSON file for your base language (e.g., `en-us.json`). This ensures localStorage is cleared and strings load from the JS file for better efficiency.

---

## Using Translations in Constructos

### Basic Usage

Reference strings from the imported strings object:

```javascript
import { W } from "winnetoujs";
import { $header } from "./header.wcto";
import strings from "./translations/en-us";

new $header({
  title: strings.appTitle,
  subtitle: strings.appDescription,
}).create("#app");
```

### Dynamic Content

```javascript
import strings from "./translations/en-us";

function renderWelcome(username) {
  new $welcome({
    message: `${strings.welcomeMessage}, ${username}!`,
  }).create("#app");
}
```

### Form Labels

```javascript
import strings from "./translations/en-us";

new $loginForm({
  usernameLabel: strings.username,
  passwordLabel: strings.password,
  submitButton: strings.login,
  cancelButton: strings.cancel,
}).create("#app");
```

---

## Best Practices

### String Organization

**Group strings by feature:**

```javascript
export default W.strings = {
  // Navigation
  nav_home: "Home",
  nav_about: "About",
  nav_contact: "Contact",

  // Forms
  form_submit: "Submit",
  form_cancel: "Cancel",
  form_reset: "Reset",

  // Validation
  validation_required: "This field is required",
  validation_email: "Invalid email address",
  validation_minLength: "Minimum {0} characters",

  // Messages
  msg_success: "Operation successful",
  msg_error: "An error occurred",
  msg_loading: "Loading...",
};
```

### String Naming Conventions

**Use consistent prefixes:**

```javascript
export default W.strings = {
  // btn_ for buttons
  btn_save: "Save",
  btn_cancel: "Cancel",
  btn_delete: "Delete",

  // lbl_ for labels
  lbl_username: "Username",
  lbl_password: "Password",
  lbl_email: "Email",

  // msg_ for messages
  msg_welcome: "Welcome",
  msg_goodbye: "Goodbye",

  // err_ for errors
  err_notFound: "Not found",
  err_unauthorized: "Unauthorized",
};
```

### Translation File Structure

**Mirror the base language structure in JSON:**

```json
{
  "nav_home": "Início",
  "nav_about": "Sobre",
  "nav_contact": "Contato",

  "form_submit": "Enviar",
  "form_cancel": "Cancelar",
  "form_reset": "Redefinir",

  "validation_required": "Este campo é obrigatório",
  "validation_email": "Endereço de e-mail inválido",
  "validation_minLength": "Mínimo de {0} caracteres"
}
```

---

## Common Patterns

### Language Selector

```javascript
import { W } from "winnetoujs";
import { changeLang } from "winnetoujs/modules/translations";
import { $languageSelector } from "./components.wcto";
import strings from "./translations/en-us";

new $languageSelector({
  label: strings.selectLanguage,
  options: [
    { value: "en-us", label: "English" },
    { value: "pt-br", label: "Português" },
    { value: "es-es", label: "Español" },
    { value: "fr-fr", label: "Français" },
  ],
  onchange: W.fx(select => {
    const selectedLang = select.value;
    if (selectedLang !== "en-us") {
      changeLang(selectedLang, true);
    } else {
      changeLang("en-us", true);
    }
  }, "this"),
}).create("#header");
```

### Language Toggle Button

```javascript
import { changeLang } from "winnetoujs/modules/translations";
import { $langButton } from "./components.wcto";
import strings from "./translations/en-us";

const languages = ["en-us", "pt-br", "es-es"];
let currentIndex = 0;

new $langButton({
  text: strings.changeLanguage,
  onclick: W.fx(() => {
    currentIndex = (currentIndex + 1) % languages.length;
    const nextLang = languages[currentIndex];
    changeLang(nextLang, true);
  }),
}).create("#header");
```

### Persistent Language Preference

```javascript
import { W } from "winnetoujs";
import {
  updateTranslations,
  changeLang,
} from "winnetoujs/modules/translations";
import strings from "./translations/en-us";

// Initialize with saved language
async function initTranslations() {
  await updateTranslations({
    stringsClass: strings,
    translationsPublicPath: "/translations",
  });

  // Check for saved language preference
  const savedLang = W.getMutable("preferredLanguage");
  if (savedLang && savedLang !== "en-us") {
    changeLang(savedLang, false);
  }

  startApp();
}

// Save language preference when changing
function setLanguage(lang) {
  W.setMutable("preferredLanguage", lang);
  changeLang(lang, true);
}

initTranslations();
```

### String Interpolation

```javascript
import strings from "./translations/en-us";

// Base string with placeholder
export default W.strings = {
  welcomeUser: "Welcome, {0}!",
  itemsCount: "You have {0} items in your cart",
  dateFormat: "Today is {0}",
};

// Helper function for interpolation
function interpolate(str, ...args) {
  return str.replace(/{(\d+)}/g, (match, index) => {
    return typeof args[index] !== "undefined" ? args[index] : match;
  });
}

// Usage
const username = "John";
const message = interpolate(strings.welcomeUser, username);
// Result: "Welcome, John!"

const count = 5;
const cartMessage = interpolate(strings.itemsCount, count);
// Result: "You have 5 items in your cart"
```

### Pluralization Helper

```javascript
import strings from "./translations/en-us";

export default W.strings = {
  item_one: "1 item",
  item_other: "{0} items",
};

function pluralize(count, singular, plural) {
  return count === 1
    ? singular.replace("{0}", count)
    : plural.replace("{0}", count);
}

// Usage
const count = 5;
const text = pluralize(count, strings.item_one, strings.item_other);
// Result: "5 items"
```

### Date and Number Formatting

```javascript
import strings from "./translations/en-us";

export default W.strings = {
  locale: "en-US",
  currency: "USD",
};

function formatCurrency(amount) {
  return new Intl.NumberFormat(strings.locale, {
    style: "currency",
    currency: strings.currency,
  }).format(amount);
}

function formatDate(date) {
  return new Intl.DateTimeFormat(strings.locale, {
    year: "numeric",
    month: "long",
    day: "numeric",
  }).format(date);
}

// Usage
console.log(formatCurrency(99.99)); // "$99.99"
console.log(formatDate(new Date())); // "October 14, 2025"
```

### Re-rendering UI on Language Change

```javascript
import { changeLang } from "winnetoujs/modules/translations";
import strings from "./translations/en-us";

class App {
  constructor() {
    this.render();
  }

  render() {
    new $header({
      title: strings.appTitle,
    }).create("#app", { clear: true });

    new $navigation({
      home: strings.home,
      about: strings.about,
      contact: strings.contact,
    }).create("#app");
  }

  changeLanguage(lang) {
    changeLang(lang, false);

    // Wait a bit for strings to update
    setTimeout(() => {
      this.render();
    }, 100);
  }
}

const app = new App();
```
