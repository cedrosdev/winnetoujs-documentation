# Native Translations: Internationalization in WinnetouJs

## Overview

WinnetouJs provides a powerful, built-in translation module for internationalizing your applications. With minimal setup, you can support multiple languages, allow users to switch between them dynamically, and persist their language preferences across sessions.

**Key Features:**

- 🌍 Built-in internationalization (i18n) support
- 🔄 Dynamic language switching without page reload
- 💾 Automatic language preference persistence in localStorage
- 🎯 Type-safe string references with IDE support
- 🔍 Go To Definition/Reference support (F12) for translation strings
- 📦 JSON-based translation files
- ⚡ Efficient loading and caching
- 🎨 Works seamlessly with constructos

## Why Use WinnetouJs Translations?

**Traditional Approach Problems:**
- JSON-based original language makes navigation difficult
- No IDE support for finding string usage
- Complex setup and configuration
- Multiple dependencies required

**WinnetouJs Solution:**
- JavaScript/TypeScript file for base language (IDE support)
- F12 to navigate between strings and usage
- Simple, streamlined API
- Zero additional dependencies
- Built into the framework

## Installation

The translations module is included with WinnetouJs. Simply import it:

```javascript
import { updateTranslations, changeLang } from "winnetoujs/modules/translations";
```

## Basic Setup

### 1. Create Your Base Language File

Create your original/default language as a JavaScript or TypeScript file (not JSON):

**`src/translations/en-us.js`**

```javascript
import { W } from "winnetoujs";

export default W.strings = {
  // App general
  appTitle: "My Awesome App",
  appDescription: "A powerful application built with WinnetouJs",
  
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
  welcomeMessage: "Welcome back!",
  successMessage: "Operation completed successfully",
  errorMessage: "An error occurred",
  
  // Buttons
  buttonText: "Change Language",
  buttonText2: "Original Language",
};
```

**Why JS/TS instead of JSON?**
- **F12 Navigation**: Jump directly to string definitions and find all usages
- **Reverse F12**: See where strings are used throughout your codebase
- **Better IDE Support**: Autocomplete, refactoring, and type checking
- **Reduced Computation**: One less file for WBR to process

### 2. Initialize Translations

In your main app file, initialize translations before starting your application:

**`src/app.ts`**

```javascript
import { W } from "winnetoujs";
import { updateTranslations } from "winnetoujs/modules/translations";
import strings from "./translations/en-us";

// Initialize translations
updateTranslations({
  stringsClass: strings,
  translationsPublicPath: "/translations",
}).then(() => {
  startApp();
});

async function startApp() {
  // Your app initialization code
  console.log(strings.appTitle); // "My Awesome App"
  
  new $header({
    title: strings.appTitle,
  }).create("#app");
}
```

**Parameters:**
- `stringsClass`: Your base language object (imported from your JS/TS file)
- `translationsPublicPath`: Public path where JSON translation files are served

**Important:** `updateTranslations()` returns a Promise, so use `.then()` or `await` before starting your app.

### 3. Create Translation Files

Create JSON files for each additional language in your public translations folder:

**Project Structure:**

```
application/
├── src/
│   ├── app.ts
│   └── translations/
│       ├── en-us.js          # Base language (JS/TS)
├── public/
│   └── translations/          # Translation JSON files
│       ├── es-es.json
│       ├── pt-br.json
│       ├── fr-fr.json
│       └── de-de.json
└── package.json
```

**`public/translations/pt-br.json`**

```json
{
  "appTitle": "Meu Aplicativo Incrível",
  "appDescription": "Um aplicativo poderoso criado com WinnetouJs",
  "home": "Início",
  "about": "Sobre",
  "contact": "Contato",
  "settings": "Configurações",
  "save": "Salvar",
  "cancel": "Cancelar",
  "delete": "Excluir",
  "edit": "Editar",
  "submit": "Enviar",
  "welcomeMessage": "Bem-vindo de volta!",
  "successMessage": "Operação concluída com sucesso",
  "errorMessage": "Ocorreu um erro",
  "buttonText": "Mudar Idioma",
  "buttonText2": "Idioma Original"
}
```

**`public/translations/es-es.json`**

```json
{
  "appTitle": "Mi Aplicación Increíble",
  "appDescription": "Una aplicación potente creada con WinnetouJs",
  "home": "Inicio",
  "about": "Acerca de",
  "contact": "Contacto",
  "settings": "Configuración",
  "save": "Guardar",
  "cancel": "Cancelar",
  "delete": "Eliminar",
  "edit": "Editar",
  "submit": "Enviar",
  "welcomeMessage": "¡Bienvenido de nuevo!",
  "successMessage": "Operación completada con éxito",
  "errorMessage": "Ocurrió un error",
  "buttonText": "Cambiar Idioma",
  "buttonText2": "Idioma Original"
}
```

**Important:** Do NOT create a JSON file for your base language (e.g., `en-us.json`). This ensures localStorage is cleared and strings are loaded from the JavaScript file itself, which is more efficient.

## Using Translations in Constructos

### Basic Usage

Simply reference strings from your imported strings object:

**`components/header.wcto.html`**

```html
<winnetou description="Application header">
  <header id="[[appHeader]]" class="header">
    <h1>{{title}}</h1>
    <nav>{{navigation}}</nav>
  </header>
</winnetou>
```

**`components/header.ts`**

```javascript
import { W } from "winnetoujs";
import { $appHeader } from "./header.wcto";
import strings from "../translations/en-us";

class Header {
  constructor() {
    this.render();
  }

  render() {
    new $appHeader({
      title: strings.appTitle,
      navigation: this.createNavigation(),
    }).create("#app");
  }

  createNavigation() {
    return `
      <a href="/home">${strings.home}</a>
      <a href="/about">${strings.about}</a>
      <a href="/contact">${strings.contact}</a>
    `;
  }
}

export default Header;
```

### Practical Example: Form with Translations

**`forms/contactForm.wcto.html`**

```html
<winnetou description="Contact form">
  <form id="[[contactForm]]" onsubmit="{{onsubmit}}">
    <div class="form-group">
      <label>{{nameLabel}}</label>
      <input type="text" name="name" placeholder="{{namePlaceholder}}" />
    </div>
    <div class="form-group">
      <label>{{emailLabel}}</label>
      <input type="email" name="email" placeholder="{{emailPlaceholder}}" />
    </div>
    <div class="form-group">
      <label>{{messageLabel}}</label>
      <textarea name="message" placeholder="{{messagePlaceholder}}"></textarea>
    </div>
    <button type="submit">{{submitButton}}</button>
    <button type="button" onclick="{{oncancel}}">{{cancelButton}}</button>
  </form>
</winnetou>
```

**`translations/en-us.js`**

```javascript
export default W.strings = {
  // ... other strings
  nameLabel: "Name",
  namePlaceholder: "Enter your name",
  emailLabel: "Email",
  emailPlaceholder: "Enter your email",
  messageLabel: "Message",
  messagePlaceholder: "Enter your message",
  submitButton: "Submit",
  cancelButton: "Cancel",
};
```

**`forms/contactForm.ts`**

```javascript
import { W } from "winnetoujs";
import { $contactForm } from "./contactForm.wcto";
import strings from "../translations/en-us";

class ContactForm {
  constructor() {
    this.render();
  }

  render() {
    new $contactForm({
      nameLabel: strings.nameLabel,
      namePlaceholder: strings.namePlaceholder,
      emailLabel: strings.emailLabel,
      emailPlaceholder: strings.emailPlaceholder,
      messageLabel: strings.messageLabel,
      messagePlaceholder: strings.messagePlaceholder,
      submitButton: strings.submitButton,
      cancelButton: strings.cancelButton,
      onsubmit: W.fx((e) => {
        e.preventDefault();
        this.handleSubmit(e);
      }, "this"),
      oncancel: W.fx(() => this.handleCancel()),
    }).create("#app");
  }

  handleSubmit(e) {
    console.log("Form submitted");
  }

  handleCancel() {
    console.log("Form cancelled");
  }
}

export default ContactForm;
```

## Changing Languages

### Using changeLang()

Use the `changeLang()` function to switch to a different language:

```javascript
import { changeLang } from "winnetoujs/modules/translations";

// Change to Portuguese (pt-br.json file)
changeLang("pt-br", true);

// Change to Spanish (es-es.json file)
changeLang("es-es", true);

// Change to French (fr-fr.json file)
changeLang("fr-fr", true);
```

**Parameters:**
- `languageCode` (string): The name of the JSON file (without `.json` extension)
- `reloadPage` (boolean): Whether to reload the page after language change

**How it works:**
1. Loads the specified JSON file from `translationsPublicPath`
2. Updates all strings in the `strings` object
3. Saves the language preference to localStorage
4. Optionally reloads the page to re-render with new strings

### Language Selector Component

**`components/languageSelector.wcto.html`**

```html
<winnetou description="Language selector dropdown">
  <div id="[[languageSelector]]" class="language-selector">
    <label for="lang-select">{{label}}</label>
    <select id="lang-select" onchange="{{onchange}}">
      {{options}}
    </select>
  </div>
</winnetou>

<winnetou description="Language option">
  <option id="[[langOption]]" value="{{value}}" selected="{{selected}}">
    {{label}}
  </option>
</winnetou>
```

**`components/languageSelector.ts`**

```javascript
import { W } from "winnetoujs";
import { changeLang } from "winnetoujs/modules/translations";
import { $languageSelector, $langOption } from "./languageSelector.wcto";
import strings from "../translations/en-us";

class LanguageSelector {
  constructor() {
    this.languages = [
      { code: "en-us", name: "English" },
      { code: "pt-br", name: "Português" },
      { code: "es-es", name: "Español" },
      { code: "fr-fr", name: "Français" },
      { code: "de-de", name: "Deutsch" },
    ];
    
    this.render();
  }

  getCurrentLanguage() {
    return localStorage.getItem("winnetou-language") || "en-us";
  }

  render() {
    const currentLang = this.getCurrentLanguage();

    // Create options HTML
    const optionsHTML = this.languages
      .map(lang => {
        return new $langOption({
          value: lang.code,
          label: lang.name,
          selected: lang.code === currentLang ? "selected" : "",
        }).constructoString();
      })
      .join("");

    new $languageSelector({
      label: strings.languageLabel || "Language:",
      options: optionsHTML,
      onchange: W.fx((self) => {
        const selectedLang = self.value;
        if (selectedLang !== currentLang) {
          changeLang(selectedLang, true);
        }
      }, "this"),
    }).create("#app");
  }
}

export default LanguageSelector;
```

### Language Toggle Button

**`components/langToggle.wcto.html`**

```html
<winnetou description="Language toggle button">
  <button id="[[langToggle]]" class="lang-toggle" onclick="{{onclick}}">
    <span class="flag-icon">{{flag}}</span>
    <span class="lang-text">{{text}}</span>
  </button>
</winnetou>
```

**`components/langToggle.ts`**

```javascript
import { W } from "winnetoujs";
import { changeLang } from "winnetoujs/modules/translations";
import { $langToggle } from "./langToggle.wcto";
import strings from "../translations/en-us";

class LanguageToggle {
  constructor() {
    this.languages = ["en-us", "pt-br", "es-es"];
    this.flags = {
      "en-us": "🇺🇸",
      "pt-br": "🇧🇷",
      "es-es": "🇪🇸",
    };
    this.render();
  }

  getCurrentLanguage() {
    return localStorage.getItem("winnetou-language") || "en-us";
  }

  getNextLanguage() {
    const current = this.getCurrentLanguage();
    const currentIndex = this.languages.indexOf(current);
    const nextIndex = (currentIndex + 1) % this.languages.length;
    return this.languages[nextIndex];
  }

  render() {
    const currentLang = this.getCurrentLanguage();

    new $langToggle({
      flag: this.flags[currentLang],
      text: currentLang.toUpperCase(),
      onclick: W.fx(() => {
        const nextLang = this.getNextLanguage();
        changeLang(nextLang, true);
      }),
    }).create("#header");
  }
}

export default LanguageToggle;
```

## Advanced Patterns

### Organizing Translation Strings

For large applications, organize strings by feature or module:

**`translations/en-us.js`**

```javascript
import { W } from "winnetoujs";

export default W.strings = {
  // Common
  common: {
    yes: "Yes",
    no: "No",
    ok: "OK",
    cancel: "Cancel",
    close: "Close",
  },

  // Navigation
  nav: {
    home: "Home",
    products: "Products",
    about: "About",
    contact: "Contact",
  },

  // Authentication
  auth: {
    login: "Login",
    logout: "Logout",
    register: "Register",
    forgotPassword: "Forgot Password?",
    email: "Email",
    password: "Password",
  },

  // Products
  products: {
    title: "Our Products",
    addToCart: "Add to Cart",
    viewDetails: "View Details",
    outOfStock: "Out of Stock",
    price: "Price",
  },

  // Errors
  errors: {
    generic: "An error occurred",
    network: "Network error",
    notFound: "Not found",
    unauthorized: "Unauthorized access",
  },
};
```

**Usage with nested structure:**

```javascript
import strings from "./translations/en-us";

// Access nested strings
console.log(strings.nav.home);           // "Home"
console.log(strings.auth.login);         // "Login"
console.log(strings.products.addToCart); // "Add to Cart"
console.log(strings.errors.generic);     // "An error occurred"
```

**Translation file with nested structure:**

**`public/translations/pt-br.json`**

```json
{
  "common": {
    "yes": "Sim",
    "no": "Não",
    "ok": "OK",
    "cancel": "Cancelar",
    "close": "Fechar"
  },
  "nav": {
    "home": "Início",
    "products": "Produtos",
    "about": "Sobre",
    "contact": "Contato"
  },
  "auth": {
    "login": "Entrar",
    "logout": "Sair",
    "register": "Registrar",
    "forgotPassword": "Esqueceu a senha?",
    "email": "E-mail",
    "password": "Senha"
  },
  "products": {
    "title": "Nossos Produtos",
    "addToCart": "Adicionar ao Carrinho",
    "viewDetails": "Ver Detalhes",
    "outOfStock": "Fora de Estoque",
    "price": "Preço"
  },
  "errors": {
    "generic": "Ocorreu um erro",
    "network": "Erro de rede",
    "notFound": "Não encontrado",
    "unauthorized": "Acesso não autorizado"
  }
}
```

### Dynamic String Interpolation

For strings that need dynamic values, use template literals:

**`translations/en-us.js`**

```javascript
export default W.strings = {
  // Static strings
  welcome: "Welcome",
  
  // Functions for dynamic strings
  greetUser: (name) => `Hello, ${name}!`,
  itemCount: (count) => `You have ${count} item${count !== 1 ? 's' : ''}`,
  timeRemaining: (minutes) => `${minutes} minute${minutes !== 1 ? 's' : ''} remaining`,
  priceDisplay: (price, currency) => `${currency}${price.toFixed(2)}`,
};
```

**Usage:**

```javascript
import strings from "./translations/en-us";

const userName = "John";
const itemCount = 5;
const timeLeft = 10;
const productPrice = 29.99;

new $greeting({
  text: strings.greetUser(userName),        // "Hello, John!"
}).create("#app");

new $cartSummary({
  items: strings.itemCount(itemCount),      // "You have 5 items"
  time: strings.timeRemaining(timeLeft),    // "10 minutes remaining"
  price: strings.priceDisplay(productPrice, "$"), // "$29.99"
}).create("#app");
```

### Pluralization Helper

Create a helper function for handling plurals:

**`utils/i18n.js`**

```javascript
export function plural(count, singular, plural) {
  return count === 1 ? singular : plural;
}

export function pluralize(count, word, pluralForm = null) {
  if (count === 1) return word;
  return pluralForm || `${word}s`;
}
```

**`translations/en-us.js`**

```javascript
import { pluralize } from "../utils/i18n";

export default W.strings = {
  items: (count) => `${count} ${pluralize(count, 'item')}`,
  people: (count) => `${count} ${pluralize(count, 'person', 'people')}`,
  child: (count) => `${count} ${pluralize(count, 'child', 'children')}`,
};
```

### Date and Number Formatting

Combine translations with Intl API for locale-specific formatting:

**`utils/formatters.js`**

```javascript
export function formatDate(date, locale = "en-US") {
  return new Intl.DateTimeFormat(locale, {
    year: "numeric",
    month: "long",
    day: "numeric",
  }).format(date);
}

export function formatCurrency(amount, currency = "USD", locale = "en-US") {
  return new Intl.NumberFormat(locale, {
    style: "currency",
    currency: currency,
  }).format(amount);
}

export function formatNumber(number, locale = "en-US") {
  return new Intl.NumberFormat(locale).format(number);
}
```

**Usage:**

```javascript
import { formatDate, formatCurrency } from "./utils/formatters";
import strings from "./translations/en-us";

const currentLang = localStorage.getItem("winnetou-language") || "en-us";
const locale = currentLang === "pt-br" ? "pt-BR" : "en-US";

const orderDate = new Date();
const totalAmount = 1234.56;

new $orderSummary({
  dateLabel: strings.orderDate,
  date: formatDate(orderDate, locale),
  totalLabel: strings.total,
  total: formatCurrency(totalAmount, "USD", locale),
}).create("#app");
```

## Without Page Reload

To change language without reloading the page, you need to manually re-render your components:

**`app.ts`**

```javascript
import { W } from "winnetoujs";
import { changeLang, updateTranslations } from "winnetoujs/modules/translations";
import strings from "./translations/en-us";

class App {
  constructor() {
    this.init();
  }

  async init() {
    await updateTranslations({
      stringsClass: strings,
      translationsPublicPath: "/translations",
    });
    
    this.render();
  }

  render() {
    // Clear and re-render app
    new $mainApp({
      title: strings.appTitle,
      description: strings.appDescription,
    }).create("#app", { clear: true });
  }

  async changeLanguage(langCode) {
    // Change language without reload
    await changeLang(langCode, false);
    
    // Re-render the entire app
    this.render();
  }
}

const app = new App();
```

**Language selector without reload:**

```javascript
new $languageSelector({
  onchange: W.fx((self) => {
    app.changeLanguage(self.value);
  }, "this"),
}).create("#header");
```

## Best Practices

### 1. Use Descriptive Key Names

✅ **Good:**

```javascript
export default W.strings = {
  loginButton: "Login",
  welcomeMessage: "Welcome to our app",
  errorInvalidEmail: "Invalid email address",
  successAccountCreated: "Account created successfully",
};
```

❌ **Avoid:**

```javascript
export default W.strings = {
  btn1: "Login",
  msg1: "Welcome to our app",
  err1: "Invalid email address",
  succ1: "Account created successfully",
};
```

### 2. Group Related Strings

Organize strings by feature or component:

```javascript
export default W.strings = {
  auth: {
    login: "Login",
    logout: "Logout",
    register: "Register",
  },
  products: {
    list: "Product List",
    details: "Product Details",
    addToCart: "Add to Cart",
  },
};
```

### 3. Keep Translation Files in Sync

Ensure all translation JSON files have the same keys as your base language:

**Tool to check missing keys:**

```javascript
function validateTranslations(baseStrings, translationJSON) {
  const missing = [];
  
  function checkKeys(base, translation, path = "") {
    for (const key in base) {
      const currentPath = path ? `${path}.${key}` : key;
      
      if (typeof base[key] === "object" && base[key] !== null) {
        if (!translation[key]) {
          missing.push(currentPath);
        } else {
          checkKeys(base[key], translation[key], currentPath);
        }
      } else {
        if (!(key in translation)) {
          missing.push(currentPath);
        }
      }
    }
  }
  
  checkKeys(baseStrings, translationJSON);
  return missing;
}
```

### 4. Avoid Hardcoded Strings

Always use translation strings, even if your app is single-language initially:

❌ **Avoid:**

```javascript
new $button({
  text: "Click me",
}).create("#app");
```

✅ **Good:**

```javascript
new $button({
  text: strings.clickButton,
}).create("#app");
```

### 5. Provide Context in Comments

Add comments to clarify string usage:

```javascript
export default W.strings = {
  // Button shown on login page
  loginButton: "Login",
  
  // Greeting message on dashboard
  welcomeMessage: "Welcome back!",
  
  // Error shown when email format is invalid
  errorInvalidEmail: "Please enter a valid email address",
};
```

### 6. Handle Missing Translations Gracefully

Add fallback logic for missing translations:

```javascript
function getTranslation(key, fallback = "") {
  const value = strings[key];
  if (value === undefined || value === "") {
    console.warn(`Missing translation for key: ${key}`);
    return fallback || key;
  }
  return value;
}
```

## Common Patterns

### Loading Indicator During Language Change

**`components/loadingOverlay.wcto.html`**

```html
<winnetou description="Loading overlay">
  <div id="[[loadingOverlay]]" class="loading-overlay">
    <div class="spinner"></div>
    <p>{{message}}</p>
  </div>
</winnetou>
```

**`app.ts`**

```javascript
async function changeLanguageWithLoading(langCode) {
  // Show loading indicator
  new $loadingOverlay({
    message: "Loading language...",
  }).create("body");

  try {
    await changeLang(langCode, false);
    
    // Re-render app
    renderApp();
  } catch (error) {
    console.error("Failed to load language:", error);
  } finally {
    // Remove loading indicator
    document.querySelector("#loadingOverlay-win-1")?.remove();
  }
}
```

### Language Detection from Browser

```javascript
function detectBrowserLanguage() {
  const browserLang = navigator.language || navigator.userLanguage;
  const langCode = browserLang.toLowerCase();
  
  // Map browser language codes to your supported languages
  const languageMap = {
    "en": "en-us",
    "en-us": "en-us",
    "en-gb": "en-us",
    "pt": "pt-br",
    "pt-br": "pt-br",
    "es": "es-es",
    "es-es": "es-es",
    "fr": "fr-fr",
    "de": "de-de",
  };
  
  return languageMap[langCode] || languageMap[langCode.split("-")[0]] || "en-us";
}

// Use on first app load
const savedLang = localStorage.getItem("winnetou-language");
if (!savedLang) {
  const detectedLang = detectBrowserLanguage();
  changeLang(detectedLang, false);
}
```

### RTL (Right-to-Left) Language Support

```javascript
const rtlLanguages = ["ar", "he", "fa", "ur"];

function isRTL(langCode) {
  return rtlLanguages.some(rtl => langCode.startsWith(rtl));
}

async function changeLanguageWithDirection(langCode) {
  await changeLang(langCode, false);
  
  // Update HTML direction
  document.documentElement.dir = isRTL(langCode) ? "rtl" : "ltr";
  document.documentElement.lang = langCode;
  
  // Re-render app
  renderApp();
}
```

## Troubleshooting

### Translations Not Loading

**Issue:** Translations don't load or strings don't change.

**Solutions:**

1. Verify `translationsPublicPath` is correct and accessible:
   ```javascript
   updateTranslations({
     stringsClass: strings,
     translationsPublicPath: "/translations", // Check this path
   });
   ```

2. Ensure JSON files are in the correct location:
   ```
   public/translations/pt-br.json  ✅
   src/translations/pt-br.json     ❌
   ```

3. Check browser console for 404 errors on JSON file requests

4. Verify JSON file syntax is valid (use JSON validator)

### Strings Not Updating After Language Change

**Issue:** Language changes but UI doesn't update.

**Solutions:**

1. Use `reloadPage: true` parameter:
   ```javascript
   changeLang("pt-br", true); // true = reload page
   ```

2. Or manually re-render components:
   ```javascript
   await changeLang("pt-br", false);
   renderAllComponents();
   ```

### Missing Translation Keys

**Issue:** Some strings show as undefined or keys instead of translated text.

**Solutions:**

1. Ensure all keys exist in translation JSON files:
   ```json
   {
     "home": "Início",
     "about": "Sobre"
   }
   ```

2. Check for typos in key names (case-sensitive):
   ```javascript
   strings.homePage  // ❌ Wrong
   strings.home      // ✅ Correct
   ```

3. Verify nested keys are properly structured:
   ```javascript
   strings.nav.home  // Requires nav: { home: "..." }
   ```

### Base Language Not Working

**Issue:** Even the default language doesn't show proper strings.

**Solutions:**

1. Ensure you're exporting `W.strings` correctly:
   ```javascript
   import { W } from "winnetoujs";
   export default W.strings = { ... };  // ✅
   ```

2. Verify import in app file:
   ```javascript
   import strings from "./translations/en-us";  // ✅
   ```

3. Check that `updateTranslations()` is called before using strings:
   ```javascript
   await updateTranslations({ stringsClass: strings, ... });
   // Now safe to use strings
   ```

## API Reference

### `updateTranslations(config)`

Initializes the translation system with your base language and configuration.

**Parameters:**
- `config` (Object):
  - `stringsClass` (Object): Your base language object
  - `translationsPublicPath` (String): Public path where JSON files are served

**Returns:** `Promise<void>`

**Usage:**
```javascript
await updateTranslations({
  stringsClass: strings,
  translationsPublicPath: "/translations",
});
```

### `changeLang(languageCode, reloadPage)`

Changes the application language.

**Parameters:**
- `languageCode` (String): Language code matching JSON filename (without `.json`)
- `reloadPage` (Boolean): Whether to reload the page after language change

**Returns:** `Promise<void>`

**Usage:**
```javascript
await changeLang("pt-br", true);  // Changes to Portuguese and reloads
await changeLang("es-es", false); // Changes to Spanish without reload
```

## Conclusion

WinnetouJs's built-in translation module provides a streamlined, efficient solution for internationalizing your applications. By using JavaScript/TypeScript for your base language and JSON for translations, you get the best of both worlds: IDE support and easy translation management.

**Key Takeaways:**

- Use JS/TS files for base language (not JSON) for IDE support and F12 navigation
- Call `updateTranslations()` before app startup
- Place translation JSON files in your public folder
- Use `changeLang()` to switch languages dynamically
- Organize strings by feature or module for large apps
- Keep all translation files in sync with base language keys
- Leverage browser's Intl API for locale-specific formatting
- Provide fallbacks for missing translations
- Test all languages thoroughly
- Consider RTL support for applicable languages

With these patterns and best practices, you can create truly international applications that provide excellent user experiences across different languages and cultures.
