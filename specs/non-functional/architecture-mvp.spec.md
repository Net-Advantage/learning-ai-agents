# MVP Architecture Specification

**Version**: 1.0  
**Last Updated**: February 16, 2026

This specification defines the mandated technology stack, architectural patterns, and technical constraints for MVP (Minimum Viable Product) applications in this repository. MVPs are designed for rapid prototyping and validation with no backend infrastructure. All architectural decisions for MVPs must align with these standards.

---

## 1. Purpose and Scope

### 1.1 When to Use MVP Architecture
This architecture is appropriate for:
- Proof of concept applications
- User interface prototypes
- Client-side only applications
- Rapid validation of user experience
- Demonstration and presentation purposes

### 1.2 When to Migrate to Full Architecture
Migrate to the full architecture (see `architecture.spec.md`) when:
- Backend data persistence is required
- Multi-user collaboration is needed
- Authentication and authorization are necessary
- Business logic requires server-side processing
- Scalability beyond single-user is required

---

## 2. Mandated Technology Stack

All MVP systems must use the following technology stack. These choices are fixed for consistency and simplicity.

### 2.1 Core Technologies
- **Markup**: HTML5 (semantic HTML)
- **Styling**: CSS3 (modern CSS with custom properties)
- **Scripting**: Vanilla JavaScript (ES2022+)
- **Component Model**: Web Components (Custom Elements v1)
- **Module System**: ES Modules (native browser imports)

### 2.2 Development Tools
- **Version Control**: Git (GitHub)
- **Code Editor**: Any modern editor (VS Code recommended)
- **Browser Testing**: Modern evergreen browsers (Chrome, Firefox, Edge, Safari)
- **Testing Framework**: Vitest or Jest (for business logic)
- **Linting**: ESLint (JavaScript), Stylelint (CSS)
- **Formatting**: Prettier

### 2.3 Forbidden Dependencies
- **No Frameworks**: No React, Vue, Angular, Svelte, etc.
- **No Backend**: No Node.js server, no API calls to custom backends
- **No Build Tools Required**: Optional bundlers allowed (Vite, Rollup) but not mandatory
- **No CSS Frameworks**: No Bootstrap, Tailwind, Material UI (custom CSS only)

**Exception**: Small utility libraries are permitted for specific needs (e.g., date formatting, charts) but must be justified and minimal.

---

## 3. Architectural Patterns and Constraints

### 3.1 File Structure

All MVP applications must follow this directory structure:

```
/mvp-app-name/
├── index.html              # Main entry point
├── README.md               # Application documentation
├── src/
│   ├── components/         # Web Components
│   │   ├── component-name/
│   │   │   ├── component-name.js
│   │   │   ├── component-name.css
│   │   │   └── README.md   # Component usage docs
│   ├── services/           # Business logic layer
│   │   ├── calculator-service.js
│   │   └── validation-service.js
│   ├── models/             # Data models and types
│   │   └── tax-model.js
│   ├── utils/              # Shared utilities
│   │   └── format-utils.js
│   ├── styles/             # Global styles
│   │   ├── variables.css   # CSS custom properties
│   │   ├── reset.css       # CSS reset/normalize
│   │   ├── typography.css  # Typography system
│   │   └── utilities.css   # Utility classes
│   └── app.js              # Application initialization
├── tests/                  # Unit tests
│   ├── services/
│   │   └── calculator-service.test.js
│   └── utils/
│       └── format-utils.test.js
└── assets/                 # Static assets
    ├── images/
    └── fonts/
```

### 3.2 Separation of Concerns

#### **Principle**: One Concern Per File
Every file should have a single, clear responsibility:

- **HTML files**: Structure and semantic markup only
- **CSS files**: Presentation and styling only
- **JavaScript files**: Behavior, logic, and interactivity only

#### **Layered Architecture (Frontend Only)**

**1. Presentation Layer (HTML + CSS)**
- Semantic HTML5 elements
- Web Components for composition
- CSS modules scoped to components
- No inline styles (except dynamic styles via JS)
- No inline scripts

**2. Component Layer (Web Components)**
- Custom Elements for reusable UI components
- Shadow DOM for encapsulation (where appropriate)
- Template elements for markup patterns
- Component-specific styles in separate CSS files
- Components consume services, never contain business logic

**3. Service Layer (Business Logic)**
- Pure JavaScript modules
- Testable, pure functions where possible
- Business rules and calculations
- Data validation
- No DOM manipulation
- No direct component coupling

**4. Model Layer (Data Structures)**
- Plain JavaScript objects/classes
- Data validation schemas
- Type definitions (JSDoc for typing)
- Immutable data patterns preferred

**5. Utility Layer (Cross-Cutting Concerns)**
- Formatting helpers
- Date/time utilities
- Common validators
- Constants and enums

---

## 4. Web Components Standards

### 4.1 Component Structure

Each component must follow this pattern:

**File: `src/components/tax-input/tax-input.js`**
```javascript
/**
 * TaxInput component for entering tax-related information
 */
export class TaxInputComponent extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
  }

  connectedCallback() {
    this.render();
    this.attachEventListeners();
  }

  render() {
    this.shadowRoot.innerHTML = `
      <link rel="stylesheet" href="/src/components/tax-input/tax-input.css">
      <div class="tax-input">
        <label for="input">
          <slot name="label">Tax Amount</slot>
        </label>
        <input type="number" id="input" />
      </div>
    `;
  }

  attachEventListeners() {
    // Event listeners here
  }

  disconnectedCallback() {
    // Cleanup here
  }
}

customElements.define('tax-input', TaxInputComponent);
```

**File: `src/components/tax-input/tax-input.css`**
```css
.tax-input {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-sm);
}

label {
  font-weight: var(--font-weight-medium);
  color: var(--color-text-primary);
}

input {
  padding: var(--spacing-sm);
  border: 1px solid var(--color-border);
  border-radius: var(--border-radius);
}
```

### 4.2 Component Design Principles

**Composition Over Inheritance**
- Build complex components from simple ones
- Use slots for content projection
- Avoid deep component inheritance

**Single Responsibility**
- Each component does one thing well
- Break down complex UIs into smaller components
- Component names reflect their purpose

**Encapsulation**
- Use Shadow DOM for style isolation when appropriate
- Expose minimal public API
- Internal state management within component

**Communication**
- Parent → Child: Attributes and properties
- Child → Parent: Custom events
- Sibling ↔ Sibling: Via shared parent or event bus (sparingly)

### 4.3 Custom Element Naming
- Use kebab-case (required by spec)
- Include app prefix to avoid conflicts: `mvp-button`, `tax-calculator`
- Descriptive names: `mvp-form-input`, `mvp-result-display`

---

## 5. CSS Architecture

### 5.1 CSS Organization

**Global Styles** (`src/styles/`)
- `variables.css`: CSS custom properties (design tokens)
- `reset.css`: Browser normalization
- `typography.css`: Font families, sizes, line heights
- `utilities.css`: Reusable utility classes

**Component Styles** (`src/components/*/`)
- One CSS file per component
- Scoped to component (via Shadow DOM or naming convention)
- Use global CSS variables for consistency

### 5.2 CSS Custom Properties (Design Tokens)

**File: `src/styles/variables.css`**
```css
:root {
  /* Colors */
  --color-primary: #0066cc;
  --color-secondary: #6c757d;
  --color-success: #28a745;
  --color-error: #dc3545;
  --color-warning: #ffc107;
  
  --color-text-primary: #212529;
  --color-text-secondary: #6c757d;
  --color-border: #dee2e6;
  --color-background: #ffffff;
  
  /* Spacing */
  --spacing-xs: 0.25rem;
  --spacing-sm: 0.5rem;
  --spacing-md: 1rem;
  --spacing-lg: 1.5rem;
  --spacing-xl: 2rem;
  
  /* Typography */
  --font-family-base: system-ui, -apple-system, "Segoe UI", sans-serif;
  --font-size-sm: 0.875rem;
  --font-size-base: 1rem;
  --font-size-lg: 1.25rem;
  --font-size-xl: 1.5rem;
  
  --font-weight-normal: 400;
  --font-weight-medium: 500;
  --font-weight-bold: 700;
  
  /* Layout */
  --border-radius: 0.25rem;
  --box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  
  /* Transitions */
  --transition-fast: 150ms ease-in-out;
  --transition-base: 250ms ease-in-out;
}
```

### 5.3 CSS Naming Conventions

**Component Classes**: Use BEM (Block Element Modifier)
```css
.tax-calculator { }
.tax-calculator__input { }
.tax-calculator__input--error { }
```

**Utility Classes**: Prefix with `u-`
```css
.u-text-center { text-align: center; }
.u-mt-md { margin-top: var(--spacing-md); }
```

**State Classes**: Prefix with `is-` or `has-`
```css
.is-active { }
.has-error { }
```

### 5.4 Responsive Design
- Mobile-first approach
- Use relative units (rem, em, %)
- Breakpoints defined in variables
- Media queries in component CSS files

---

## 6. JavaScript Architecture

### 6.1 Module System

**Use ES Modules exclusively:**
```javascript
// Importing
import { calculateTax } from './services/tax-service.js';
import { TaxModel } from './models/tax-model.js';

// Exporting
export function calculateTax(income) { }
export class TaxModel { }
```

**Module Loading in HTML:**
```html
<script type="module" src="/src/app.js"></script>
```

### 6.2 Service Layer Pattern

Services contain business logic and must be:
- **Pure functions** where possible (deterministic, no side effects)
- **Testable** in isolation
- **Framework-agnostic** (no DOM dependencies)
- **Well-documented** with JSDoc

**Example: `src/services/tax-calculator-service.js`**
```javascript
/**
 * Calculates PAYE tax based on income and tax brackets
 * @param {number} income - Annual income amount
 * @returns {{tax: number, netIncome: number, effectiveRate: number}}
 */
export function calculatePAYE(income) {
  if (typeof income !== 'number' || income < 0) {
    throw new Error('Income must be a non-negative number');
  }

  // Business logic here
  const tax = calculateTaxAmount(income);
  const netIncome = income - tax;
  const effectiveRate = income > 0 ? (tax / income) * 100 : 0;

  return { tax, netIncome, effectiveRate };
}

/**
 * Internal function to calculate tax amount
 * @private
 */
function calculateTaxAmount(income) {
  // Tax bracket logic
  return 0; // Placeholder
}
```

### 6.3 Model Layer Pattern

Models define data structures and validation:

**Example: `src/models/tax-calculation.js`**
```javascript
/**
 * Represents a tax calculation result
 */
export class TaxCalculation {
  constructor(income, tax, netIncome, effectiveRate) {
    this.income = income;
    this.tax = tax;
    this.netIncome = netIncome;
    this.effectiveRate = effectiveRate;
    this.timestamp = new Date();
  }

  /**
   * Validates the tax calculation
   * @returns {boolean}
   */
  isValid() {
    return this.income >= 0 && 
           this.tax >= 0 && 
           this.netIncome >= 0;
  }

  /**
   * Converts to plain object for serialization
   * @returns {Object}
   */
  toJSON() {
    return {
      income: this.income,
      tax: this.tax,
      netIncome: this.netIncome,
      effectiveRate: this.effectiveRate,
      timestamp: this.timestamp.toISOString()
    };
  }
}
```

### 6.4 State Management

For MVP applications, use simple state management:

**Option 1: Component-Local State**
- State stored in component properties
- Suitable for isolated components

**Option 2: Shared State Object**
- Single state object for app-wide state
- Observable pattern for reactivity
- Kept minimal and simple

**Example: `src/services/state-service.js`**
```javascript
class AppState {
  constructor() {
    this.state = {};
    this.listeners = {};
  }

  get(key) {
    return this.state[key];
  }

  set(key, value) {
    this.state[key] = value;
    this.notify(key, value);
  }

  subscribe(key, callback) {
    if (!this.listeners[key]) {
      this.listeners[key] = [];
    }
    this.listeners[key].push(callback);
  }

  notify(key, value) {
    if (this.listeners[key]) {
      this.listeners[key].forEach(cb => cb(value));
    }
  }
}

export const appState = new AppState();
```

### 6.5 Error Handling

**Consistent error handling patterns:**
```javascript
export function processData(data) {
  try {
    validateData(data);
    return transformData(data);
  } catch (error) {
    console.error('Error processing data:', error);
    throw new Error(`Failed to process data: ${error.message}`);
  }
}
```

**User-facing errors:**
- Display in UI via custom events
- Never expose technical details to users
- Provide actionable error messages

---

## 7. Testing Strategy

### 7.1 What to Test

**Required Tests:**
- Business logic (service layer) - **100% coverage goal**
- Data models and validation
- Utility functions
- Complex calculations

**Optional Tests:**
- Component rendering (basic smoke tests)
- Integration between services
- User interactions (manual testing acceptable for MVP)

**Not Required for MVP:**
- End-to-end tests
- Visual regression tests
- Performance benchmarks

### 7.2 Testing Framework

**Vitest** (recommended) or **Jest**

**Example Test: `tests/services/tax-calculator-service.test.js`**
```javascript
import { describe, it, expect } from 'vitest';
import { calculatePAYE } from '../../src/services/tax-calculator-service.js';

describe('calculatePAYE', () => {
  it('should calculate tax correctly for basic income', () => {
    const result = calculatePAYE(50000);
    expect(result.tax).toBeGreaterThan(0);
    expect(result.netIncome).toBe(50000 - result.tax);
    expect(result.effectiveRate).toBeGreaterThan(0);
  });

  it('should handle zero income', () => {
    const result = calculatePAYE(0);
    expect(result.tax).toBe(0);
    expect(result.netIncome).toBe(0);
    expect(result.effectiveRate).toBe(0);
  });

  it('should throw error for negative income', () => {
    expect(() => calculatePAYE(-1000)).toThrow();
  });

  it('should throw error for non-numeric input', () => {
    expect(() => calculatePAYE('invalid')).toThrow();
  });
});
```

### 7.3 Test Configuration

**File: `vitest.config.js`**
```javascript
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    globals: true,
    environment: 'jsdom', // For DOM testing if needed
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html'],
      include: ['src/services/**', 'src/models/**', 'src/utils/**'],
      exclude: ['**/*.test.js', '**/node_modules/**']
    }
  }
});
```

### 7.4 Running Tests

```bash
# Run all tests
npm test

# Run tests in watch mode
npm test -- --watch

# Run with coverage
npm test -- --coverage
```

---

## 8. Data Persistence Strategy

Since MVPs have no backend, use client-side storage:

### 8.1 Browser Storage Options

**localStorage** (recommended for MVP)
- Persistent across sessions
- Synchronous API
- 5-10MB limit
- Store as JSON strings

**sessionStorage**
- Cleared when tab closes
- Same API as localStorage
- Use for temporary data

**IndexedDB**
- For larger datasets (if needed)
- Asynchronous API
- More complex but powerful

### 8.2 Storage Service Pattern

**File: `src/services/storage-service.js`**
```javascript
/**
 * Service for client-side data persistence
 */
export class StorageService {
  constructor(prefix = 'mvp') {
    this.prefix = prefix;
  }

  /**
   * Save data to localStorage
   * @param {string} key
   * @param {any} data
   */
  save(key, data) {
    try {
      const serialized = JSON.stringify(data);
      localStorage.setItem(`${this.prefix}_${key}`, serialized);
    } catch (error) {
      console.error('Failed to save data:', error);
      throw new Error('Storage quota exceeded or data not serializable');
    }
  }

  /**
   * Load data from localStorage
   * @param {string} key
   * @returns {any|null}
   */
  load(key) {
    try {
      const serialized = localStorage.getItem(`${this.prefix}_${key}`);
      return serialized ? JSON.parse(serialized) : null;
    } catch (error) {
      console.error('Failed to load data:', error);
      return null;
    }
  }

  /**
   * Remove data from localStorage
   * @param {string} key
   */
  remove(key) {
    localStorage.removeItem(`${this.prefix}_${key}`);
  }

  /**
   * Clear all app data
   */
  clear() {
    Object.keys(localStorage)
      .filter(key => key.startsWith(this.prefix))
      .forEach(key => localStorage.removeItem(key));
  }
}

export const storage = new StorageService();
```

---

## 9. Application Initialization

### 9.1 Main Entry Point

**File: `index.html`**
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MVP Application</title>
  
  <!-- Global Styles -->
  <link rel="stylesheet" href="/src/styles/reset.css">
  <link rel="stylesheet" href="/src/styles/variables.css">
  <link rel="stylesheet" href="/src/styles/typography.css">
  <link rel="stylesheet" href="/src/styles/utilities.css">
  
  <!-- App Initialization -->
  <script type="module" src="/src/app.js"></script>
</head>
<body>
  <div id="app">
    <!-- Application components loaded here -->
    <mvp-header></mvp-header>
    <main>
      <mvp-calculator></mvp-calculator>
    </main>
    <mvp-footer></mvp-footer>
  </div>
</body>
</html>
```

**File: `src/app.js`**
```javascript
/**
 * Application initialization and component registration
 */

// Import all components
import './components/mvp-header/mvp-header.js';
import './components/mvp-calculator/mvp-calculator.js';
import './components/mvp-footer/mvp-footer.js';

// Import services if needed for initialization
import { appState } from './services/state-service.js';

/**
 * Initialize application
 */
function init() {
  console.log('Application initialized');
  
  // Any startup logic here
  // Load saved state, set up global event listeners, etc.
}

// Initialize when DOM is ready
if (document.readyState === 'loading') {
  document.addEventListener('DOMContentLoaded', init);
} else {
  init();
}
```

---

## 10. Development Workflow

### 10.1 Local Development

**Option 1: Simple HTTP Server (No Build)**
```bash
# Using Python
python -m http.server 8000

# Using Node.js
npx serve .

# Using VS Code Live Server extension
# Right-click index.html → Open with Live Server
```

**Option 2: Vite Dev Server (With Hot Reload)**
```bash
npm install -D vite
npx vite
```

**Configuration: `vite.config.js`**
```javascript
export default {
  root: '.',
  server: {
    port: 3000,
    open: true
  }
};
```

### 10.2 Project Setup

**File: `package.json`**
```json
{
  "name": "mvp-app-name",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "test": "vitest",
    "test:coverage": "vitest --coverage",
    "lint:js": "eslint src/**/*.js",
    "lint:css": "stylelint src/**/*.css",
    "format": "prettier --write \"src/**/*.{js,css,html}\""
  },
  "devDependencies": {
    "vite": "^5.0.0",
    "vitest": "^1.0.0",
    "@vitest/ui": "^1.0.0",
    "eslint": "^8.0.0",
    "stylelint": "^16.0.0",
    "prettier": "^3.0.0"
  }
}
```

### 10.3 Code Quality Tools

**ESLint Configuration: `.eslintrc.json`**
```json
{
  "env": {
    "browser": true,
    "es2022": true
  },
  "extends": "eslint:recommended",
  "parserOptions": {
    "ecmaVersion": 2022,
    "sourceType": "module"
  },
  "rules": {
    "no-console": "warn",
    "no-unused-vars": "error"
  }
}
```

**Prettier Configuration: `.prettierrc.json`**
```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5"
}
```

---

## 11. Deployment Strategy

### 11.1 Static Hosting

MVPs are static sites and can be deployed to:
- **GitHub Pages** (recommended for prototypes)
- **Netlify**
- **Vercel**
- **Azure Static Web Apps**
- **Any static hosting service**

### 11.2 GitHub Pages Deployment

**File: `.github/workflows/deploy.yml`**
```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
      
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./
```

### 11.3 Build Optimization (Optional)

For production, optionally use Vite to bundle:
```bash
npx vite build
```

This creates optimized files in `dist/` directory.

---

## 12. Documentation Requirements

### 12.1 Application README

Each MVP must have a README.md with:
- Purpose and scope
- How to run locally
- How to run tests
- Component overview
- Deployment instructions

### 12.2 Component Documentation

Each component directory should have a README.md with:
- Component purpose
- Usage example
- Attributes/properties
- Events emitted
- Slots available

**Example: `src/components/mvp-button/README.md`**
```markdown
# MVP Button Component

Reusable button component with consistent styling.

## Usage

\`\`\`html
<mvp-button variant="primary" size="medium">
  Click Me
</mvp-button>
\`\`\`

## Attributes

- `variant`: `primary` | `secondary` | `danger` (default: `primary`)
- `size`: `small` | `medium` | `large` (default: `medium`)
- `disabled`: Boolean

## Events

- `mvp-button-click`: Fired when button is clicked

## Slots

- Default slot: Button text content
```

### 12.3 Inline Documentation

Use JSDoc for all JavaScript:
```javascript
/**
 * Calculates the total tax amount
 * @param {number} income - The annual income
 * @param {Array<TaxBracket>} brackets - Array of tax brackets
 * @returns {number} The calculated tax amount
 * @throws {Error} If income is negative
 */
export function calculateTax(income, brackets) {
  // Implementation
}
```

---

## 13. Accessibility Standards

### 13.1 Required Practices

**Semantic HTML**
- Use proper heading hierarchy (h1-h6)
- Use `<button>` for interactive elements
- Use `<label>` for form inputs
- Use `<nav>`, `<main>`, `<footer>`, etc.

**ARIA Attributes (when needed)**
```html
<button aria-label="Close dialog" aria-expanded="false">
  ×
</button>
```

**Keyboard Navigation**
- All interactive elements keyboard accessible
- Logical tab order
- Focus indicators visible
- Escape key closes modals/dialogs

**Color Contrast**
- WCAG AA minimum (4.5:1 for text)
- Don't rely on color alone to convey information

### 13.2 Testing Accessibility

Manual checks:
- Keyboard-only navigation test
- Screen reader test (NVDA, JAWS, VoiceOver)
- Browser DevTools accessibility audit

---

## 14. Performance Guidelines

### 14.1 Performance Targets (MVP)

- **First Contentful Paint**: < 1.5s
- **Time to Interactive**: < 3s
- **Lighthouse Score**: > 90

### 14.2 Optimization Techniques

**Minimal JavaScript**
- Load only what's needed
- Use native browser features
- Defer non-critical JS

**CSS Best Practices**
- Minimize render-blocking CSS
- Use CSS containment where appropriate
- Avoid complex selectors

**Asset Optimization**
- Compress images (WebP format preferred)
- Use SVG for icons
- Lazy load images below the fold

**Caching**
- Use service workers for offline support (optional)
- Set appropriate cache headers in production

---

## 15. Security Considerations

### 15.1 Client-Side Security

**Input Sanitization**
- Sanitize all user input before rendering
- Use `textContent` instead of `innerHTML` when possible
- Validate data types and ranges

**XSS Prevention**
```javascript
// Bad
element.innerHTML = userInput;

// Good
element.textContent = userInput;

// If HTML is needed, sanitize first
import DOMPurify from 'dompurify';
element.innerHTML = DOMPurify.sanitize(userInput);
```

**Data Privacy**
- Never store sensitive data in localStorage
- Clear storage appropriately
- No credentials or API keys in client code

**Content Security Policy**
```html
<meta http-equiv="Content-Security-Policy" 
      content="default-src 'self'; script-src 'self';">
```

---

## 16. Migration Path to Full Architecture

When an MVP needs to become a production application:

### 16.1 Migration Checklist

- [ ] Evaluate backend requirements
- [ ] Design API contracts
- [ ] Migrate business logic to backend (.NET API)
- [ ] Implement authentication/authorization
- [ ] Set up database (SQL Server)
- [ ] Convert frontend to Blazor (if needed) or keep as static frontend
- [ ] Implement CI/CD pipeline
- [ ] Set up monitoring and logging
- [ ] Security audit
- [ ] Performance optimization
- [ ] Documentation update

### 16.2 Reusable Assets

From MVP to full architecture, these can be reused:
- Business logic (services) - port to C#
- Data models - convert to C# DTOs/entities
- UI design and styles - adapt to Blazor components
- Test cases - rewrite for xUnit
- Requirements and specifications

---

## 17. Constraints and Non-Negotiables

The following are **fixed constraints** for MVP applications:

1. Must be client-side only (no custom backend)
2. Must use Web Components (no framework components)
3. Must use vanilla JavaScript (ES2022+, no TypeScript required for MVP)
4. Must separate concerns (HTML/CSS/JS in separate files)
5. Business logic must be testable (service layer pattern)
6. Must use CSS custom properties for theming
7. Must be deployable as static files
8. Must follow accessibility standards (WCAG 2.1 AA minimum)

Optimizations and enhancements are permitted within these boundaries.

---

## 18. Common Patterns and Recipes

### 18.1 Form Handling

```javascript
// In component
handleSubmit(event) {
  event.preventDefault();
  const formData = new FormData(event.target);
  const data = Object.fromEntries(formData);
  
  // Validate
  const errors = validateForm(data);
  if (errors.length > 0) {
    this.displayErrors(errors);
    return;
  }
  
  // Process via service
  const result = processFormData(data);
  
  // Emit event to parent
  this.dispatchEvent(new CustomEvent('form-submit', {
    detail: result,
    bubbles: true
  }));
}
```

### 18.2 Loading States

```javascript
class MyComponent extends HTMLElement {
  setLoading(isLoading) {
    this.setAttribute('aria-busy', isLoading);
    const button = this.shadowRoot.querySelector('button');
    button.disabled = isLoading;
    button.textContent = isLoading ? 'Loading...' : 'Submit';
  }
}
```

### 18.3 Error Display

```javascript
displayError(message) {
  const errorEl = this.shadowRoot.querySelector('.error-message');
  errorEl.textContent = message;
  errorEl.setAttribute('role', 'alert');
  errorEl.classList.add('is-visible');
}
```

---

## 19. Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | Feb 16, 2026 | System | Initial MVP architecture specification |

---

## 20. References and Resources

### Learning Resources
- [MDN Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
- [ES Modules Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules)
- [CSS Custom Properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)
- [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)

### Tools
- [Vitest](https://vitest.dev/)
- [Vite](https://vitejs.dev/)
- [ESLint](https://eslint.org/)
- [Prettier](https://prettier.io/)

---

**Note**: This specification is designed for rapid MVP development. For production applications requiring backend services, authentication, data persistence, and scalability, refer to `architecture.spec.md`.
