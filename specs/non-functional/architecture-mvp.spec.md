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
- **Framework**: React 18+
- **Language**: TypeScript 5+
- **Build Tool**: Vite
- **Styling**: CSS3 with CSS Modules or styled-components
- **Module System**: ES Modules

### 2.2 Development Tools
- **Version Control**: Git (GitHub)
- **Code Editor**: VS Code recommended (with TypeScript support)
- **Browser Testing**: Modern evergreen browsers (Chrome, Firefox, Edge, Safari)
- **Testing Framework**: Vitest (for unit/integration tests)
- **Testing Library**: React Testing Library
- **Linting**: ESLint (TypeScript/React rules)
- **Type Checking**: TypeScript compiler
- **Formatting**: Prettier

### 2.3 Permitted Dependencies
- **React Router**: For client-side routing (if needed)
- **Utility Libraries**: Small focused libraries (e.g., date-fns, zod for validation)
- **UI Libraries**: Headless UI libraries allowed (e.g., Radix UI)
- **Chart Libraries**: For data visualization (e.g., recharts, chart.js)

### 2.4 Forbidden Dependencies
- **No Backend**: No Node.js server, no API calls to custom backends
- **No Heavy Frameworks**: No Next.js, Remix, Gatsby (Vite + React only)
- **No CSS Frameworks**: No Bootstrap, Tailwind, Material UI (custom CSS only)
- **No State Management Libraries**: No Redux, MobX, Zustand (use React Context/hooks)

**Principle**: Keep dependencies minimal and justify each addition.

---

## 3. Architectural Patterns and Constraints

### 3.1 File Structure

All MVP applications must follow this directory structure:

```
/mvp-app-name/
├── index.html              # Vite entry point
├── README.md               # Application documentation
├── package.json            # Dependencies and scripts
├── tsconfig.json           # TypeScript configuration
├── vite.config.ts          # Vite configuration
├── src/
│   ├── main.tsx            # Application entry point
│   ├── App.tsx             # Root component
│   ├── components/         # React components
│   │   ├── ComponentName/
│   │   │   ├── ComponentName.tsx
│   │   │   ├── ComponentName.module.css
│   │   │   ├── ComponentName.test.tsx
│   │   │   └── index.ts    # Re-export
│   ├── services/           # Business logic layer
│   │   ├── calculatorService.ts
│   │   ├── calculatorService.test.ts
│   │   └── validationService.ts
│   ├── models/             # TypeScript types and interfaces
│   │   ├── TaxModel.ts
│   │   └── index.ts
│   ├── hooks/              # Custom React hooks
│   │   ├── useCalculator.ts
│   │   └── useLocalStorage.ts
│   ├── context/            # React Context providers
│   │   └── AppContext.tsx
│   ├── utils/              # Shared utilities
│   │   ├── formatUtils.ts
│   │   └── formatUtils.test.ts
│   ├── styles/             # Global styles
│   │   ├── variables.css   # CSS custom properties
│   │   ├── reset.css       # CSS reset/normalize
│   │   └── global.css      # Global styles
│   └── assets/             # Static assets
│       ├── images/
│       └── fonts/
└── public/                 # Public static files
    └── favicon.ico
```

### 3.2 Separation of Concerns

#### **Principle**: One Concern Per File
Every file should have a single, clear responsibility:

- **HTML files**: Structure and semantic markup only
- **CSS files**: Presentation and styling only
- **JavaScript files**: Behavior, logic, and interactivity only

#### **Layered Architecture (Frontend Only)**

**1. Presentation Layer (React Components)**
- React functional components with TypeScript
- CSS Modules for component-scoped styling
- Semantic JSX markup
- Props typing with TypeScript interfaces
- No business logic in components (delegate to hooks/services)

**2. Component Layer (React Components)**
- Functional components with hooks
- Component composition patterns
- Props validation via TypeScript
- Component-specific styles in CSS Modules
- Controlled/uncontrolled component patterns
- Components consume services via hooks

**3. Hook Layer (React State Logic)**
- Custom hooks for reusable logic
- State management with useState/useReducer
- Side effects with useEffect
- Context consumption with useContext
- Hooks delegate to services for business logic

**4. Service Layer (Business Logic)**
- Pure TypeScript functions/classes
- Framework-agnostic business logic
- Testable, pure functions where possible
- No React dependencies
- No DOM manipulation

**5. Model Layer (TypeScript Types)**
- Type definitions and interfaces
- Enums and constants
- Type guards and validators
- Zod schemas for runtime validation (optional)
- Immutable data patterns

**6. Utility Layer (Cross-Cutting Concerns)**
- Formatting helpers
- Date/time utilities
- Common validators
- Constants and enums

---

## 4. React Component Standards

### 4.1 Component Structure

Each component must follow this pattern:

**File: `src/components/TaxInput/TaxInput.tsx`**
```typescript
import { ChangeEvent } from 'react';
import styles from './TaxInput.module.css';

interface TaxInputProps {
  label?: string;
  value: number;
  onChange: (value: number) => void;
  error?: string;
  disabled?: boolean;
}

/**
 * TaxInput component for entering tax-related information
 */
export function TaxInput({
  label = 'Tax Amount',
  value,
  onChange,
  error,
  disabled = false,
}: TaxInputProps) {
  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    const numValue = parseFloat(e.target.value);
    if (!isNaN(numValue)) {
      onChange(numValue);
    }
  };

  return (
    <div className={styles.taxInput}>
      <label htmlFor="tax-input" className={styles.label}>
        {label}
      </label>
      <input
        id="tax-input"
        type="number"
        value={value}
        onChange={handleChange}
        disabled={disabled}
        className={styles.input}
        aria-invalid={!!error}
        aria-describedby={error ? 'tax-input-error' : undefined}
      />
      {error && (
        <span id="tax-input-error" className={styles.error} role="alert">
          {error}
        </span>
      )}
    </div>
  );
}
```

**File: `src/components/TaxInput/TaxInput.module.css`**
```css
.taxInput {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-sm);
}

.label {
  font-weight: var(--font-weight-medium);
  color: var(--color-text-primary);
}

.input {
  padding: var(--spacing-sm);
  border: 1px solid var(--color-border);
  border-radius: var(--border-radius);
}

.input:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.error {
  color: var(--color-error);
  font-size: var(--font-size-sm);
}
```

**File: `src/components/TaxInput/index.ts`**
```typescript
export { TaxInput } from './TaxInput';
export type { TaxInputProps } from './TaxInput';
```

### 4.2 Component Design Principles

**Composition Over Inheritance**
- Build complex components from simpler ones
- Use children prop for content projection
- Prefer composition to prop drilling

**Single Responsibility**
- Each component does one thing well
- Break down complex UIs into smaller components
- Component names reflect their purpose (PascalCase)

**TypeScript First**
- All components fully typed
- Props defined with interfaces
- Use Pick, Omit, extends for prop composition
- Avoid `any` types

**Communication**
- Parent → Child: Props (typed)
- Child → Parent: Callback props
- Sibling ↔ Sibling: Via shared parent state or Context
- Global state: React Context (avoid prop drilling)

### 4.3 Component Naming Conventions
- Use PascalCase for component names: `TaxInput`, `ResultDisplay`
- One component per file
- File name matches component name: `TaxInput.tsx`
- Folder name matches component name: `TaxInput/`
- Re-export from `index.ts` for clean imports

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

**Use ES Modules with TypeScript:**
```typescript
// Importing
import { calculateTax } from './services/calculatorService';
import { TaxModel } from './models/TaxModel';
import type { TaxCalculation } from './models';

// Exporting
export function calculateTax(income: number): number { }
export class TaxModel { }
export type { TaxCalculation };
```

**Module Loading:**
- Vite handles bundling and code splitting
- Use named exports for better tree-shaking
- Import React components without .tsx extension
- Use path aliases in tsconfig.json for cleaner imports

### 6.2 Service Layer Pattern

Services contain business logic and must be:
- **Pure functions** where possible (deterministic, no side effects)
- **Testable** in isolation
- **Framework-agnostic** (no React dependencies)
- **Fully typed** with TypeScript

**Example: `src/services/calculatorService.ts`**
```typescript
import type { TaxCalculation } from '../models';

/**
 * Calculates PAYE tax based on income and tax brackets
 */
export function calculatePAYE(income: number): TaxCalculation {
  if (income < 0) {
    throw new Error('Income must be a non-negative number');
  }

  // Business logic here
  const tax = calculateTaxAmount(income);
  const netIncome = income - tax;
  const effectiveRate = income > 0 ? (tax / income) * 100 : 0;

  return {
    income,
    tax,
    netIncome,
    effectiveRate,
    timestamp: new Date(),
  };
}

/**
 * Internal function to calculate tax amount
 */
function calculateTaxAmount(income: number): number {
  // Tax bracket logic
  return 0; // Placeholder
}
```

### 6.3 Model Layer Pattern

Models define TypeScript types and interfaces:

**Example: `src/models/TaxModel.ts`**
```typescript
/**
 * Represents a tax calculation result
 */
export interface TaxCalculation {
  income: number;
  tax: number;
  netIncome: number;
  effectiveRate: number;
  timestamp: Date;
}

/**
 * Type guard to validate TaxCalculation
 */
export function isTaxCalculation(obj: unknown): obj is TaxCalculation {
  if (typeof obj !== 'object' || obj === null) return false;
  
  const calc = obj as TaxCalculation;
  return (
    typeof calc.income === 'number' &&
    typeof calc.tax === 'number' &&
    typeof calc.netIncome === 'number' &&
    typeof calc.effectiveRate === 'number' &&
    calc.timestamp instanceof Date &&
    calc.income >= 0 &&
    calc.tax >= 0 &&
    calc.netIncome >= 0
  );
}

/**
 * Serializes TaxCalculation for storage
 */
export function serializeTaxCalculation(calc: TaxCalculation): string {
  return JSON.stringify({
    ...calc,
    timestamp: calc.timestamp.toISOString(),
  });
}

/**
 * Deserializes TaxCalculation from storage
 */
export function deserializeTaxCalculation(json: string): TaxCalculation {
  const obj = JSON.parse(json);
  return {
    ...obj,
    timestamp: new Date(obj.timestamp),
  };
}
```

### 6.4 State Management

For MVP applications, use React's built-in state management:

**Option 1: Component-Local State (useState)**
- Use for component-specific state
- Suitable for isolated components
```typescript
const [count, setCount] = useState(0);
```

**Option 2: Lifted State**
- Share state between components via props
- Lift state to common ancestor

**Option 3: React Context (for global state)**
- Use for app-wide state
- Avoid prop drilling
- Keep minimal and focused

**Example: `src/context/AppContext.tsx`**
```typescript
import { createContext, useContext, useState, ReactNode } from 'react';
import type { TaxCalculation } from '../models';

interface AppContextValue {
  calculation: TaxCalculation | null;
  setCalculation: (calc: TaxCalculation) => void;
}

const AppContext = createContext<AppContextValue | undefined>(undefined);

export function AppProvider({ children }: { children: ReactNode }) {
  const [calculation, setCalculation] = useState<TaxCalculation | null>(null);

  return (
    <AppContext.Provider value={{ calculation, setCalculation }}>
      {children}
    </AppContext.Provider>
  );
}

export function useAppContext() {
  const context = useContext(AppContext);
  if (!context) {
    throw new Error('useAppContext must be used within AppProvider');
  }
  return context;
}
```

**Custom Hooks for Reusable Logic:**
```typescript
// src/hooks/useCalculator.ts
import { useState } from 'react';
import { calculatePAYE } from '../services/calculatorService';
import type { TaxCalculation } from '../models';

export function useCalculator() {
  const [calculation, setCalculation] = useState<TaxCalculation | null>(null);
  const [error, setError] = useState<string | null>(null);
  const [isLoading, setIsLoading] = useState(false);

  const calculate = (income: number) => {
    setIsLoading(true);
    setError(null);
    
    try {
      const result = calculatePAYE(income);
      setCalculation(result);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Calculation failed');
    } finally {
      setIsLoading(false);
    }
  };

  return { calculation, error, isLoading, calculate };
}
```

### 6.5 Error Handling

**Service Layer Error Handling:**
```typescript
export function processData(data: unknown): ProcessedData {
  try {
    validateData(data);
    return transformData(data);
  } catch (error) {
    console.error('Error processing data:', error);
    throw new Error(
      `Failed to process data: ${error instanceof Error ? error.message : 'Unknown error'}`
    );
  }
}
```

**React Error Boundaries:**
```typescript
// src/components/ErrorBoundary/ErrorBoundary.tsx
import { Component, ReactNode } from 'react';

interface Props {
  children: ReactNode;
  fallback?: ReactNode;
}

interface State {
  hasError: boolean;
  error: Error | null;
}

export class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || <div>Something went wrong</div>;
    }
    return this.props.children;
  }
}
```

**User-Facing Errors:**
- Display in UI via state
- Never expose technical details to users
- Provide actionable error messages
- Use TypeScript to catch errors at compile time

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

**Vitest** (for unit/integration tests) + **React Testing Library** (for component tests)

**Service Test Example: `src/services/calculatorService.test.ts`**
```typescript
import { describe, it, expect } from 'vitest';
import { calculatePAYE } from './calculatorService';

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
    expect(() => calculatePAYE(-1000)).toThrow('Income must be a non-negative number');
  });
});
```

**Component Test Example: `src/components/TaxInput/TaxInput.test.tsx`**
```typescript
import { describe, it, expect, vi } from 'vitest';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { TaxInput } from './TaxInput';

describe('TaxInput', () => {
  it('should render with label', () => {
    const onChange = vi.fn();
    render(<TaxInput value={0} onChange={onChange} label="Income" />);
    
    expect(screen.getByLabelText('Income')).toBeInTheDocument();
  });

  it('should call onChange when value changes', async () => {
    const onChange = vi.fn();
    const user = userEvent.setup();
    
    render(<TaxInput value={0} onChange={onChange} />);
    const input = screen.getByRole('spinbutton');
    
    await user.type(input, '50000');
    expect(onChange).toHaveBeenCalled();
  });

  it('should display error message', () => {
    const onChange = vi.fn();
    render(<TaxInput value={0} onChange={onChange} error="Invalid amount" />);
    
    expect(screen.getByRole('alert')).toHaveTextContent('Invalid amount');
  });
});
```

### 7.3 Test Configuration

**File: `vitest.config.ts`**
```typescript
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './src/test/setup.ts',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html', 'json'],
      include: ['src/**/*.{ts,tsx}'],
      exclude: [
        '**/*.test.{ts,tsx}',
        '**/*.spec.{ts,tsx}',
        '**/node_modules/**',
        '**/dist/**',
        'src/main.tsx',
        'src/vite-env.d.ts',
      ],
    },
  },
});
```

**File: `src/test/setup.ts`**
```typescript
import '@testing-library/jest-dom';
import { cleanup } from '@testing-library/react';
import { afterEach } from 'vitest';

afterEach(() => {
  cleanup();
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
</head>
<body>
  <div id="root"></div>
  <script type="module" src="/src/main.tsx"></script>
</body>
</html>
```

**File: `src/main.tsx`**
```typescript
import React from 'react';
import ReactDOM from 'react-dom/client';
import { App } from './App';
import './styles/reset.css';
import './styles/variables.css';
import './styles/global.css';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

**File: `src/App.tsx`**
```typescript
import { AppProvider } from './context/AppContext';
import { Calculator } from './components/Calculator';
import styles from './App.module.css';

export function App() {
  return (
    <AppProvider>
      <div className={styles.app}>
        <header className={styles.header}>
          <h1>MVP Application</h1>
        </header>
        <main className={styles.main}>
          <Calculator />
        </main>
        <footer className={styles.footer}>
          <p>&copy; 2026 MVP App</p>
        </footer>
      </div>
    </AppProvider>
  );
}
```

**File: `src/vite-env.d.ts`**
```typescript
/// <reference types="vite/client" />
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
    "build": "tsc && vite build",
    "preview": "vite preview",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest --coverage",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "type-check": "tsc --noEmit",
    "format": "prettier --write \"src/**/*.{ts,tsx,css}\""
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "@typescript-eslint/eslint-plugin": "^6.0.0",
    "@typescript-eslint/parser": "^6.0.0",
    "@vitejs/plugin-react": "^4.2.0",
    "@vitest/coverage-v8": "^1.0.0",
    "@vitest/ui": "^1.0.0",
    "@testing-library/react": "^14.0.0",
    "@testing-library/jest-dom": "^6.0.0",
    "@testing-library/user-event": "^14.0.0",
    "eslint": "^8.0.0",
    "eslint-plugin-react": "^7.33.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.4.0",
    "jsdom": "^23.0.0",
    "prettier": "^3.0.0",
    "typescript": "^5.3.0",
    "vite": "^5.0.0",
    "vitest": "^1.0.0"
  }
}
```

**File: `tsconfig.json`**
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",

    /* Linting */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,

    /* Path aliases */
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

**File: `tsconfig.node.json`**
```json
{
  "compilerOptions": {
    "composite": true,
    "skipLibCheck": true,
    "module": "ESNext",
    "moduleResolution": "bundler",
    "allowSyntheticDefaultImports": true
  },
  "include": ["vite.config.ts"]
}
```

**File: `vite.config.ts`**
```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
  server: {
    port: 3000,
    open: true,
  },
});
```

### 10.3 Code Quality Tools

**ESLint Configuration: `.eslintrc.cjs`**
```javascript
module.exports = {
  root: true,
  env: { browser: true, es2020: true },
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react/recommended',
    'plugin:react/jsx-runtime',
    'plugin:react-hooks/recommended',
  ],
  ignorePatterns: ['dist', '.eslintrc.cjs'],
  parser: '@typescript-eslint/parser',
  plugins: ['react-refresh'],
  rules: {
    'react-refresh/only-export-components': [
      'warn',
      { allowConstantExport: true },
    ],
    '@typescript-eslint/no-unused-vars': 'error',
    '@typescript-eslint/no-explicit-any': 'error',
  },
  settings: {
    react: {
      version: 'detect',
    },
  },
};
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
2. Must use React with TypeScript
3. Must use Vite as build tool
4. All code must be in `/src` folder
5. Must use functional components with hooks (no class components)
6. Business logic must be testable (service layer pattern)
7. Must use CSS Modules or styled-components for styling
8. Must use CSS custom properties for theming
9. Must be deployable as static files
10. Must follow accessibility standards (WCAG 2.1 AA minimum)
11. TypeScript strict mode must be enabled
12. No `any` types allowed (use `unknown` and type guards instead)

Optimizations and enhancements are permitted within these boundaries.

---

## 18. Common Patterns and Recipes

### 18.1 Form Handling

```typescript
import { FormEvent, useState } from 'react';
import { validateForm } from '../utils/validation';
import type { FormData } from '../models';

function MyForm() {
  const [formData, setFormData] = useState<FormData>({ income: 0 });
  const [errors, setErrors] = useState<string[]>([]);

  const handleSubmit = (event: FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    
    const validationErrors = validateForm(formData);
    if (validationErrors.length > 0) {
      setErrors(validationErrors);
      return;
    }
    
    const result = processFormData(formData);
    onSubmit(result);
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* form fields */}
    </form>
  );
}
```

### 18.2 Loading States

```typescript
import { useState } from 'react';
import styles from './MyComponent.module.css';

function MyComponent() {
  const [isLoading, setIsLoading] = useState(false);

  const handleClick = async () => {
    setIsLoading(true);
    try {
      await performAction();
    } finally {
      setIsLoading(false);
    }
  };

  return (
    <button
      onClick={handleClick}
      disabled={isLoading}
      aria-busy={isLoading}
      className={styles.button}
    >
      {isLoading ? 'Loading...' : 'Submit'}
    </button>
  );
}
```

### 18.3 Error Display

```typescript
import styles from './ErrorMessage.module.css';

interface ErrorMessageProps {
  message: string;
}

export function ErrorMessage({ message }: ErrorMessageProps) {
  if (!message) return null;

  return (
    <div className={styles.error} role="alert">
      {message}
    </div>
  );
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
- [React Documentation](https://react.dev/)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [Vite Guide](https://vitejs.dev/guide/)
- [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/)
- [CSS Modules](https://github.com/css-modules/css-modules)
- [CSS Custom Properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)
- [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)

### Tools
- [Vite](https://vitejs.dev/)
- [Vitest](https://vitest.dev/)
- [React Testing Library](https://testing-library.com/)
- [ESLint](https://eslint.org/)
- [TypeScript](https://www.typescriptlang.org/)
- [Prettier](https://prettier.io/)

---

**Note**: This specification is designed for rapid MVP development. For production applications requiring backend services, authentication, data persistence, and scalability, refer to `architecture.spec.md`.
