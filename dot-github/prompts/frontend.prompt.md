# Frontend Development Prompt

You are working on a **Next.js 15 frontend application** built with the Sainsbury's technology stack. Apply all of the following guidelines when writing, reviewing, or modifying code.

## Technology Stack

- **Next.js 15** — App Router, Server Components by default
- **React 19** — functional components with hooks
- **TypeScript** — strict mode throughout
- **Tailwind CSS** — via Fable design tokens only (see Design System below)
- **Node.js 20**

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
├── lib/             # Utilities and helpers
├── config.ts        # Centralised environment configuration
└── global.css       # Global styles and Fable imports
```

## Code Style & Standards

- **TypeScript strict mode** — no `any`, explicit types everywhere
- **No `console.log`** — use structured logging: `const logger = createComponentLogger('ComponentName')` then `logger.info()`, `logger.error()` etc.
- **Centralised env vars** — always access via `src/config.ts`, never use `process.env` directly elsewhere
- **Named exports** — prefer over default exports for components
- **`const` arrow functions** for components: `const MyComponent = () => {}`
- **Template literals** — only allow `string`, `number`, and `boolean` expressions

## Design System (Fable)

- Use Fable components from `@sainsburys-tech/fable` wherever possible before building custom ones
- **Always use `fable:` prefix** for all Tailwind utility classes:
  ```tsx
  className="fable:flex fable:items-center fable:px-4"
  ```
- Hover states: `hover:fable:bg-gray-50`
- Use `clsx` for conditional class application:
  ```tsx
  import clsx from 'clsx'
  className={clsx('fable:flex fable:gap-2', { 'fable:hidden': isHidden })}
  ```

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
- **Server actions** for form submissions and data mutations
- Use `next-safe-action` for type-safe server actions
- Use Zod schemas for all user input validation

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

- Co-locate unit tests alongside components: `component.test.tsx`
- Use **React Testing Library** — test behaviour, not implementation
- Use `jest.mock()` for server actions and external dependencies
- Use snapshots sparingly — prefer behavioural assertions
- `ResizeObserver` is polyfilled for Fable component compatibility
- Use `@/` path alias in test imports

```bash
npm run test          # run all tests
npm run test:watch    # watch mode
```

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
2. Reference [Fable Storybook](https://sainsburys-tech.github.io/design-systems/) for component APIs
3. Always use `fable:` prefix on Tailwind classes
4. Ensure proper accessibility attributes

## Build & Development

```bash
npm install           # requires GITHUB_PACKAGES_AUTH_TOKEN
npm run dev           # dev server with Turbopack
npm run build         # production build
npm run lint          # ESLint
npm run prettier      # format with Prettier
```

### Docker
```bash
docker build --secret id=GITHUB_PACKAGES_AUTH_TOKEN -t app-name .
docker run --rm -it -p 3000:80 app-name
```

## Resources

- [Fable Storybook](https://sainsburys-tech.github.io/design-systems/?path=/docs/documentation-introduction--introduction)
- [Fable Design Tokens](https://design-systems-tokens.ext.prd.jspaas.uk/?subCategory=all)
- [Next.js App Router](https://nextjs.org/docs/app)
- [next-auth](https://authjs.dev/)
