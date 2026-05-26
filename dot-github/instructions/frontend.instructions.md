---
applyTo: "**/*.{ts,tsx,js,jsx,mjs,cjs}"
---

# Frontend Development Instructions

You are working on a **Next.js 16 frontend application** built with the Sainsbury's technology stack. Apply all of the following guidelines when writing, reviewing, or modifying code.

## Technology Stack

- **Next.js 16** — App Router, Server Components by default
- **React 19** — functional components with hooks
- **TypeScript** — strict mode throughout
- **Tailwind CSS** — via Fable design tokens only (see Design System below)
- **Node.js 24**

### Auth & Security
- **next-auth v5** with Microsoft Entra ID (SSO) and PKCE flow
- Security headers: CSP, HSTS, XSS protection configured in `next.config.js`

### Design System
- **@sainsburys-tech/fable** — React component library
- **@sainsburys-tech/fable-tokens** — design tokens
- **@sainsburys-tech/style** — Tailwind config mapped from Fable tokens
- **@sainsburys-tech/icons** — icon set
- **@sainsburys-tech/images** — logos and branded images
- **@sainsburys-tech/theme-provider** — theming

> **Fable Storybook** at https://sainsburys-tech.github.io/design-systems/ is the authoritative reference for all Fable components. Use it to look up available props, variants, sizes, slots, design tokens, and usage examples. Before using or building any UI component, check the Storybook first — the component may already exist in Fable.

### Dev Tools
- **ESLint** — TypeScript strict rules + SonarJS
- **Prettier** — code formatting
- **Jest + React Testing Library** — testing
- **Docker** — multi-stage builds

## Project Structure

```
src/
├── actions/          # Server actions
├── app/             # Next.js App Router pages and layouts
├── components/      # Reusable React components
├── constants/       # Shared text/wording (e.g. button labels, error messages)
├── lib/             # Utilities and helpers
├── config.ts        # Centralised environment configuration
└── global.css       # Global styles and Fable imports
```

### File naming conventions
- `component.tsx` — server component
- `component.client.tsx` — client component
- `component.test.tsx` — Jest unit test
- `component.e2e.ts` — Playwright end-to-end test
- `component.module.css` — CSS module

## Code Style & Standards

- **TypeScript strict mode** — no `any`, explicit types everywhere
- **Prefer `interface`** over `type` for object shapes; use `type` for unions and primitives
- **Avoid enums** — use `const` maps instead:
  ```ts
  const Direction = { Up: 'up', Down: 'down' } as const
  type Direction = typeof Direction[keyof typeof Direction]
  ```
- **No `console.log`** — use structured logging: `const logger = createComponentLogger('ComponentName')` then `logger.info()`, `logger.error()` etc.
- **Centralised env vars** — always access via `src/config.ts`, never use `process.env` directly elsewhere
- **Named exports** — prefer over default exports for components (default exports are acceptable)
- **`function` keyword** for named components and pure functions; arrow functions for callbacks and inline expressions
- **Template literals** — only allow `string`, `number`, and `boolean` expressions
- **`import type`** for type-only imports: `import type { MyType } from './types'`
- **`@/` alias** for all internal imports — never use relative `../../` paths
- **Descriptive variable names** — use auxiliary verbs: `isLoading`, `hasError`, `hasSubmitted`
- **Avoid early abstraction** — write code where it is used; only extract when needed in multiple places
- **Minimize `useEffect` and `setState`** — favour Server Components and server-side data fetching

## Design System (Fable)

- Use Fable components from `@sainsburys-tech/fable` wherever possible before building custom ones
- **Tailwind utility classes use `fable:` prefix** (e.g. `fable:flex fable:items-center fable:px-4`)
- **Custom CSS class names use `ds-` prefix** — any CSS you write for component-specific styles should use `ds-` (e.g. `.ds-product-card`)
- If migrating old CSS, update any `ds-` prefixed Tailwind utilities to `fable:` — `ds-` remains correct only for hand-written CSS class names
- Hover states: `hover:fable:bg-gray-50`
- Use `clsx` for conditional class application:
  ```tsx
  import clsx from 'clsx'
  className={clsx('fable:flex fable:gap-2', { 'fable:hidden': isHidden })}
  ```
- Implement responsive design with a **mobile-first** approach

### CSS Modules for Complex Styles

When styles are complex, conditional, or reference Fable design tokens directly, use co-located CSS modules:

```css
/* component.module.css */
.errorState {
  outline: solid 2px var(--fable-color-background-error-bold-default);
}
```

```tsx
import styles from './component.module.css'
import clsx from 'clsx'

<Box className={clsx('fable:mt-1', { [styles.errorState]: hasError })} />
```

### Brand Support

The app supports multiple Sainsbury's brands via Fable. Enable additional brands by uncommenting imports in `src/global.css`:
- Sainsbury's (default), Argos, Habitat, Tu, Nectar360

## Component Patterns

- **Server Components by default** — add `'use client'` only when interactivity requires it
- Client component files use `.client.tsx` suffix
- **Server actions** — mark with `'use server'` directive, use `next-safe-action` for type-safe actions
- Use Zod schemas for all user input validation
- Use `next/image` instead of `<img>` for all images — optimise with WebP format, explicit `width`/`height`, lazy loading
- Use `next/font` for font loading — never import fonts via CSS `@import`
- Use `next/dynamic` with `{ ssr: false }` for heavy client-only components to reduce bundle size
- Use `nuqs` for URL search parameter state management
- Do not reach for `useCallback`/`useMemo` unless there is a measured performance problem — avoid premature optimisation
- Optimise for Core Web Vitals: LCP, CLS, FID

## Page Patterns

- Export `metadata` (or `generateMetadata`) from every page for SEO
- Add a `loading.tsx` alongside pages that have async data fetching — use `<Suspense>` for granular loading states
- Add `error.tsx` for client-side error boundaries at route segment level
- Add `not-found.tsx` for custom 404 handling

## Accessibility

- Use semantic HTML elements (`<nav>`, `<main>`, `<section>`, `<article>`, `<button>`) over generic `<div>`
- All interactive elements must be keyboard accessible
- Always provide `alt` text for images — empty `alt=""` for decorative images
- Use `aria-*` attributes when semantic HTML isn't sufficient
- Ensure Fable components receive appropriate accessibility props (check Storybook for required props)
- Maintain sufficient colour contrast — rely on Fable design tokens which are a11y compliant


## Authentication

- All protected routes enforced via middleware
- Define protected paths in `src/config.ts` as `AUTH_PROTECTED_PATHS`
- Use `await auth()` in server components to access session
- SSO redirect URL pattern: `<domain>/api/auth/callback/entra-pkce`

## Security Requirements

- Never commit secrets — environment variables only
- CSP headers configured in `next.config.js`
- HSTS enabled in production only
- Frame options: DENY (prevent clickjacking)
- `unsafe inline/eval` only in development mode

## Linting

1. **Always fix linting errors through code changes first**
2. Only use `// eslint-disable-next-line` as a last resort
3. When a suppression is unavoidable, document exactly why:

```ts
// eslint-disable-next-line @typescript-eslint/no-unsafe-assignment
// Reason: third-party lib returns `any` and cannot be typed externally
```

## Testing

### When to use each type

| Test type | Tool | When to use |
|-----------|------|-------------|
| Component / unit | Jest + React Testing Library | Isolated component behaviour, logic, rendering |
| End-to-end | Playwright | Full user journeys, cross-page flows, auth, real browser |

### Component Tests (Jest + React Testing Library)

- Co-locate tests alongside components: `component.test.tsx`
- Test behaviour and output — not implementation details or internal state
- Use `jest.mock()` for server actions, API calls, and external dependencies
- Use `@/` path alias in all imports
- `ResizeObserver` is polyfilled for Fable component compatibility
- Use snapshots sparingly — prefer explicit behavioural assertions
- Query elements using accessible queries in priority order:
  1. `getByRole` — preferred
  2. `getByLabelText`, `getByPlaceholderText`
  3. `getByText`
  4. `getByTestId` — last resort only

```bash
npm run test          # run all tests
npm run test:watch    # watch mode
```

### End-to-End Tests (Playwright)

- Name files `feature.e2e.ts` — co-locate with the feature or place in `e2e/` folder
- Use the **Page Object Model** to encapsulate page interactions:
  ```ts
  // pages/login.page.ts
  export class LoginPage {
    constructor(private page: Page) {}
    async goto() { await this.page.goto('/login') }
    async login(email: string) { await this.page.getByLabel('Email').fill(email) }
  }
  ```
- **Prefer accessible locators** over CSS selectors or `data-testid`:
  - `page.getByRole('button', { name: 'Submit' })`
  - `page.getByLabel('Email address')`
  - `page.getByText('Confirm order')`
  - Use `data-testid` only when no accessible alternative exists
- Use `expect(locator).toBeVisible()` and Playwright's built-in auto-waiting — never add manual `sleep`/`waitForTimeout`
- Use `page.route()` to intercept and mock API calls where needed
- Use Playwright fixtures for shared setup (auth state, test data)
- Store authenticated state in `playwright/.auth/` and reuse across tests to avoid repeated logins

### Playwright MCP Server

You have the `@playwright/mcp` MCP server available. When writing or debugging Playwright tests, the agent can use it to:

- **Navigate** to the running app and take a page snapshot to understand the DOM structure before writing selectors
- **Verify UI** — take screenshots after changes to confirm the result looks correct
- **Debug failing tests** — interact with the page live to identify correct locators
- **Accessibility checks** — inspect the accessibility tree to validate `getByRole` selectors

> When asked to write Playwright tests, use the MCP server to navigate to the relevant page first, take a snapshot, then write selectors based on what is actually rendered — not assumed structure.

## Common Patterns

### Adding a New Page
1. Create route in `src/app/` following App Router conventions
2. Use server component by default
3. Add to `AUTH_PROTECTED_PATHS` in `src/config.ts` if auth is required
4. Include metadata for SEO

### Adding a New Environment Variable
1. Add to Bosun YAML files in `/config/`
2. Add to `src/config.ts` with proper typing
3. Document in `.env.example`

### Using Fable Components
1. Import from `@sainsburys-tech/fable`
2. **Always look up the component in [Fable Storybook](https://sainsburys-tech.github.io/design-systems/) before using it** — it documents all props, variants, sizes, slots, default values, and code examples; it is the single source of truth for the Fable design system
3. Design tokens (colours, spacing, typography) are documented in the Storybook and can also be browsed at https://design-systems-tokens.ext.prd.jspaas.uk/
4. Always use `fable:` prefix on Tailwind classes
5. Ensure proper accessibility attributes (required `aria-*` props are documented per component in Storybook)

## Build & Development

```bash
npm install           # requires GITHUB_TOKEN
npm run dev           # dev server with Turbopack
npm run build         # production build
npm run lint          # ESLint
npm run prettier      # format with Prettier
```

### Docker
```bash
docker build --secret id=GITHUB_TOKEN -t app-name .
docker run --rm -it -p 3000:80 app-name
```

## Resources

- [Fable Storybook](https://sainsburys-tech.github.io/design-systems/?path=/docs/documentation-introduction--introduction)
- [Fable Design Tokens](https://design-systems-tokens.ext.prd.jspaas.uk/?subCategory=all)
- [Next.js App Router](https://nextjs.org/docs/app)
- [next-auth](https://authjs.dev/)
