# AGENTS.md — Rules for Building Modern Static Sites with Next.js 15

**Authoritative, non-negotiable rules and
expectations** for generating **beautiful, performant, responsive, static,
production-ready web applications** using **Next.js 15**, **React 19**, and
**TypeScript 5**.

The primary goals are:

- Visually appealing flawless **UI/UX**
- Smooth **backend integration** (FastAPI or Google Sheets)
- **Static export** for free hosting (Vercel, Netlify, GitHub Pages)

**Adherence to the following guidelines is mandatory.**

---

## PRIMARY DIRECTIVES (MANDATORY)

### 1. Type Safety First

- `strict: true` in `tsconfig.json`.
- **NEVER** use `any`.
- Validate all external data (API, forms, env vars) with **Zod**.
- Derive types using `z.infer<typeof schema>` to keep validation and types in
  sync.

### 2. Test Everything

- Minimum **80% coverage** using **Vitest** + **React Testing Library**.
- Tests live in `__tests__` directories within feature slices.
- All components, hooks, utils → unit + integration tests

### 3. Keep It Small & Focused

- Functions ≤ 50 lines, Components ≤ 200 lines, Files ≤ 500 lines
- Apply **SOLID** + **Clean Code Architecture**
- Reuse before reinventing

### 4. UI / UX Excellence

- Design must be **modern**, **responsive**, and **mobile-first**.
- All states (loading, error, empty, success) must be handled gracefully.
- **Framer Motion** for subtle animations.
- **shadcn/ui**, **Magic UI**, **Aceternity UI** for design consistency.
- Prioritize clarity, elegance, and user delight.

### 5. Forbidden Practices

Never use:

- `@ts-ignore`, `dangerouslySetInnerHTML`
- Legacy React patterns/deprecated APIs (class components,
  `componentWillMount`, etc.)
- Committing code that fails `npm run validate`.

---

## AI AGENT WORKFLOW

### Core Philosophy

- **KISS (Keep It Simple, Stupid)**: Prioritize straightforward solutions.
- **YAGNI (You Aren't Gonna Need It)**: Implement only what is required now.
- **DRY (Don't Repeat Yourself)**: Build modular components/patterns and
  reuse them.
- **Fail Fast**: Validate inputs at the boundaries and throw errors immediately.

### Standard Operating Procedure

1. **Analyze Context**: Before writing code, search the existing codebase for
   similar patterns, components, or utilities. Do not reinvent the wheel.
2. **Test-Driven Development (TDD)**: Write tests _before_ implementation. This
   clarifies requirements and ensures correctness.
3. **Incremental Changes**: Break down complex tasks into small, logical, and
   testable units.
4. **Clarify Ambiguities**: If a request is ambiguous, ask for clarification
   before proceeding.

### Tool Usage

- **Search**: **ALWAYS** use `rg` (ripgrep) for code searches. It is faster and
  more effective than `grep` or `find`.
- **Context7 Library Docs**:
  1.  Check the [`CORE LIBRARIES & STATE MANAGEMENT`](#core-libraries--state-management)
      section for a direct Context7 ID.
  2.  If not found, use `resolve-library-id` to find the correct ID.
  3.  Use `get-library-docs` to fetch **only the specific topics/sections**
      needed for the task. Do not fetch entire library documentation.

---

## CORE LIBRARIES & STATE MANAGEMENT

### State Management Hierarchy

Follow this order of preference. Do not jump to a global solution prematurely.

1. **Local State (`useState`)**: For state confined to a single component.
2. **URL State (Search Params)**: For state that should be shareable via URL.
3. **Feature State (`useContext`)**: For state shared between a few components
   within the same feature slice.
4. **Global State (`Zustand`)**: **ONLY** for truly global state (e.g., user
   authentication, theme).

### Library Reference (Context7 IDs)

```json
{
  "Zustand": "/pmndrs/zustand",
  "React": "/facebook/react",
  "Next.js": "/vercel/next.js",
  "Magic UI": "/magicuidesign/magicui",
  "Aceternity UI": "/websites/ui_aceternity-components",
  "React Hook Form": "/react-hook-form/react-hook-form",
  "Zod": "/colinhacks/zod"
}
```

---

## BACKEND INTEGRATION

Before development begins, **ask the user to select their backend**:

### 1. FastAPI Integration

- Fetch data at build time (SSG)
- API logic → `src/features/<feature>/api/`
- Reusable `apiClient` (axios / fetch)
- Responses validated with Zod
- Support pagination / filter / sort via query params
- Webhook effects → polling or revalidation
- Docs → `README.md` in feature folder
- Base URL from validated env:

  ```ts
  export const env = envSchema.parse(process.env);
  const API_BASE = env.NEXT_PUBLIC_API_URL;
  ```

### 2. Google Sheets Integration

- Use public JSON endpoint (recommended) or Sheets API v4
- Treat each sheet as a read/write datastore for simple apps
- Cache client-side (Zustand / localStorage)
- Validate responses with Zod
- Document sheet name + columns in `README.md`

---

## UI/UX & STYLING

### Core Design Rules

Deliver a beautiful, modern, and responsive user experience.

- **Mobile-First Design**: All components and layouts **MUST** be designed for
  mobile devices first, then scaled up for tablets and desktops.
- **Responsiveness**: Use Tailwind CSS utility classes to ensure layouts are
  fluid and adapt to all screen sizes.
- **High-Quality Components**: Leverage best-in-class UI libraries for a
  polished look and feel. Recommended libraries include:
  - **shadcn/ui**: For foundational, accessible components.
  - **Aceternity UI**: For complex, visually stunning, copy-paste components.
  - **Magic UI**: For modern, animated, and engaging UI elements.
- **Visual Hierarchy**: Ensure a clear visual hierarchy through proper use of
  typography, spacing, and color to guide the user's attention.

### Accessibility

- All interactive elements must have accessible labels and ARIA attributes.
- Contrast ratios must meet **WCAG AA** or higher.

### Interactions & Animation

- Subtle transitions using **Framer Motion**.
- Animate key state changes (loading → loaded, modals, hover).
- Never animate purely for decoration — motion should serve clarity or delight.

---

## TYPESCRIPT & VALIDATION

### Strict `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

### Mandatory Type Practices

- **Return Types**: All functions and components **MUST** have explicit return
  types. For React components, use `ReactElement`.
- **No `any`**: Use `unknown` for values that are truly unknown and perform
  type-checking.
- **Zod-driven Types**: **ALWAYS** derive types from Zod schemas using
  `z.infer<typeof schema>`. This is the single source of truth.
- **Branded Types**: For all IDs and domain-specific primitives, use Zod's
  `.brand()` to prevent accidental type misuse.

### Data Validation with Zod

All data crossing a system boundary **MUST** be validated.

```typescript
import { z } from "zod";

// 1. Branded ID for type safety
const UserIdSchema = z.string().uuid().brand<"UserId">();
type UserId = z.infer<typeof UserIdSchema>;

// 2. Environment variables validation (in `src/lib/env.ts`)
export const envSchema = z.object({
  NODE_ENV: z.enum(["development", "test", "production"]),
  NEXT_PUBLIC_APP_URL: z.string().url(),
  DATABASE_URL: z.string().min(1),
});
export const env = envSchema.parse(process.env);

// 3. API response validation
export const apiResponseSchema = <T extends z.ZodTypeAny>(dataSchema: T) =>
  z.object({
    success: z.boolean(),
    data: dataSchema,
    error: z.string().optional(),
  });

// 4. Form validation
const formSchema = z.object({
  email: z.string().email(),
  username: z.string().min(3),
});
type FormData = z.infer<typeof formSchema>;
```

---

## LINTING, VALIDATION, AND ENVIRONMENT

### ESLint Configuration (`eslint.config.js`)

This configuration enforces style and prevents common errors.

```javascript
import { dirname } from "path";
import { fileURLToPath } from "url";
import { FlatCompat } from "@eslint/eslintrc";

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);
const compat = new FlatCompat({ baseDirectory: __dirname });

export default [
  ...compat.extends("next/core-web-vitals"),
  {
    rules: {
      // Enforce type safety and clarity
      "@typescript-eslint/no-explicit-any": "error",
      "@typescript-eslint/explicit-function-return-type": "error",
      // Prevent accidental logging
      "no-console": ["error", { allow: ["warn", "error"] }],
      // Standardize component definitions
      "react/function-component-definition": [
        "error",
        {
          namedComponents: "arrow-function",
        },
      ],
    },
  },
];
```

### Prettier

Code **MUST** be formatted with Prettier before committing. Run`npm run format`.

### Environment Validation (`src/lib/env.ts`)

```ts
import { z } from "zod";

export const envSchema = z.object({
  NEXT_PUBLIC_API_URL: z.string().url().optional(),
  NEXT_PUBLIC_GOOGLE_SHEET_ID: z.string().optional(),
  NODE_ENV: z.enum(["development", "test", "production"]),
});

export const env = envSchema.parse(process.env);
```

---

## TESTING & QUALITY GATES

### Vitest Configuration (`vitest.config.ts`)

Setup for a fast, modern testing environment.

```ts
import { defineConfig } from "vitest/config";
import react from "@vitejs/plugin-react";
import { resolve } from "path";

export default defineConfig({
  plugins: [react()],
  test: {
    environment: "jsdom",
    setupFiles: ["./src/test/setup.ts"],
    coverage: {
      reporter: ["text", "html"],
      thresholds: {
        branches: 80,
        functions: 80,
        lines: 80,
        statements: 80,
      },
    },
  },
  resolve: { alias: { "@": resolve(__dirname, "./src") } },
});
```

### Pre-Commit Checklist

**ALL** checks **MUST** pass before creating a pull request.

- [ ] `npm run type-check` passes
- [ ] `npm run test:coverage` ≥ 80%
- [ ] `npm run lint` passes
- [ ] `npm run format:check` passes
- [ ] All new components, hooks, and functions have JSDoc comments.
- [ ] All external data is validated with a Zod schema.
- [ ] All UI states (loading, error, empty, success) are handled.

---

## PERFORMANCE, SECURITY AND STATIC EXPORT

- **Static Site Generation (SSG)**: The project **MUST** be configured for
  static output. Set `output: 'export'` in `next.config.js`.
- **Build Verification:**
  ```bash
  npm run build && npm run export
  ```
  → Output in `/out`
- **Performance**:
  - **Default to Server Components**. Use Client Components (`'use client'`)
    only for interactivity.
  - Use `next/image` and `next/font` for automatic optimization.
  - Use dynamic imports (`next/dynamic`) for large, rarely-used client
    components.
- **Security**:
  - **Sanitize All Inputs**: Use Zod for validation.
  - **Environment Variables**: Load and validate environment variables via
    `src/lib/env.ts`. **NEVER** expose server secrets to the client.
  - **CSRF Protection**: Use Next.js built-in mechanisms for Server Actions.
    Use HTTPS endpoints only.

---

## DEPENDENCIES & SCRIPTS

### Core Dependencies

```json
{
  "dependencies": {
    "next": "^15.0.0",
    "react": "^19.0.0",
    "react-dom": "^19.0.0",
    "zod": "^3.23.0",
    "zustand": "^4.5.0",
    "framer-motion": "^11.0.0",
    "shadcn-ui": "latest",
    "tailwindcss": "^3.4.0"
  },
  "devDependencies": {
    "@hookform/resolvers": "^3.3.0",
    "@testing-library/react": "^15.0.0",
    "@types/node": "^20.12.0",
    "@types/react": "^18.3.0",
    "@types/react-dom": "^18.3.0",
    "eslint": "^8.57.0",
    "eslint-config-next": "15.0.0",
    "jsdom": "^24.0.0",
    "prettier": "^3.2.0",
    "typescript": "^5.4.0",
    "vitest": "^1.5.0"
  }
}
```

### Key Scripts

- `npm run dev`: Start the development server.
- `npm run build`: Create a production-ready static export in the `./out`
  directory.
- `npm run lint`: Check for linting errors.
- `npm run test`: Run all tests once.
- `npm run type-check`: Validate TypeScript types.
- `npm run validate`: Run all checks (types, lint, test). **MUST** pass before
  commit.

---
