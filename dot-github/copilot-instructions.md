# GitHub Copilot Instructions

## Stack
- **Backend:** C# / .NET — ASP.NET Core APIs
- **Frontend:** Next.js (TypeScript) with React

## Behaviour
- If told that something is wrong, think critically about whether that's true and respond with facts — don't capitulate just to agree
- Do not apologise unnecessarily or use filler phrases like "You're right" or "Great question"
- Avoid hyperbole and excitement — be concise and pragmatic
- Always ensure responses are relevant to the context of the code provided
- Revalidate before responding — think step by step

## General Principles
- Prefer clarity and readability over cleverness
- Always handle errors explicitly — never silently swallow exceptions
- Prefer async/await over callbacks or raw Task continuations
- Write self-documenting code; only add comments when intent isn't obvious
- Always run existing tests before considering a task done
- Do not remove existing tests unless explicitly asked

## C# / .NET
- Use latest C# language features where they improve readability (records, pattern matching, primary constructors)
- Follow Microsoft naming conventions (PascalCase for types/methods, camelCase for locals)
- Prefer `IEnumerable<T>` / LINQ over manual loops where it reads naturally
- Use `Result`-style patterns or exceptions consistently — don't mix both
- Prefer constructor injection for dependencies; avoid service locator pattern
- Keep controllers thin — business logic belongs in services
- Use `CancellationToken` in all async methods that do I/O
- Prefer `record` types for DTOs and value objects
- Use `sealed` on classes that are not designed for inheritance
- Enable nullable reference types (`#nullable enable`) — treat all nullable warnings as errors

## Next.js / TypeScript / React

### Core
- Use TypeScript strictly — avoid `any`, prefer explicit types
- Prefer functional components with hooks
- Co-locate component styles, tests, and types with the component
- Use `function` keyword for named components and pure functions; arrow functions for callbacks and inline expressions
- Prefer `Server Components` by default in Next.js App Router; use `'use client'` only when necessary
- Use named exports over default exports for components
- Keep components small and focused — extract logic into custom hooks
- Use `next-safe-action` for type-safe server actions
- Use Zod schemas for all user input validation
- No `console.log` — use structured logging instead

### Design System (Sainsbury's Fable)
- Use Fable components from `@sainsburys-tech/fable` wherever possible
- **Always use `fable:` prefix** for all Tailwind utility classes: `className="fable:flex fable:items-center"`
- Hover states use `hover:fable:` prefix: `hover:fable:bg-gray-50`
- Use `clsx` for conditional class application, combining Fable Tailwind classes with CSS module classes
- For complex or repeated styles, use CSS modules co-located with the component and reference Fable design tokens via CSS custom properties: `var(--fable-color-background-error-bold-default)`

### Authentication
- Use `next-auth v5` with Microsoft Entra ID (SSO) and PKCE flow
- All protected routes are enforced via middleware — define protected paths in `src/config.ts`
- Use `await auth()` in server components to access session

### Linting
- Always attempt to fix ESLint errors through code changes first
- Only use `// eslint-disable-next-line` as a last resort — document why the rule can't be satisfied
- Never leave suppressions without an explanatory comment

## Testing
- C# backend: xUnit v3 with NSubstitute for mocking and AwesomeAssertions for assertions
- Frontend: Jest + React Testing Library for component tests
- Name tests using: `MethodName_Scenario_ExpectedResult` (C#) or descriptive `it('should ...')` blocks (JS/TS)
- Prefer testing behavior over implementation details
- Test coverage should include:
  - Success scenarios
  - Failure scenarios (404, exceptions)
  - Verify method calls with `.Received()`
  - Test parameter validation

## Code Style
- Max line length: 120 characters
- Braces on new lines in C# (Allman style)
- Prettier defaults for TypeScript/JavaScript
- No unused variables, imports, or dead code

## Git Conventions
- Use conventional commits: `feat:`, `fix:`, `chore:`, `docs:`, `refactor:`, `test:`, `ci:`
- Branch naming: `{type}/{ticket-id}-short-description` (e.g., `feat/STAT-138-blue-yonder-client`)
- **Never work directly on `main`** — always create a feature branch before starting work
- Always check which branch you are on before making any changes
- Keep commits small and focused — one logical unit of change per commit
- Commit after each meaningful piece of work — don't accumulate everything into one commit at the end
- Include the ticket ID in commit messages where applicable

## Workflow
- Before starting any task, read the relevant files — never assume their content
- Only change code that is necessary for the task — do not refactor unrelated code
- If you discover something broken outside the scope of the current task, report it rather than silently fixing it
- Do not add features or behaviour that were not explicitly asked for
- Always verify that imports, methods, types, and file paths you reference actually exist in the codebase — never invent them
- Never comment out, skip (`t.Skip`, `[Ignore]`, `xit`), or modify tests just to make them pass — fix the code instead
- If a test legitimately needs to change because requirements changed, explain why before modifying it
- If you are stuck or cannot proceed after a reasonable attempt, **stop and explain the problem clearly** — do not loop indefinitely trying variations of the same approach

## Security
- Never commit secrets, credentials, API keys, or connection strings
- Use environment variables or Azure Key Vault for sensitive configuration
- No hardcoded URLs, usernames, or passwords in source code

## What to Avoid
- Do not generate code with `TODO` placeholders unless explicitly asked
- Do not use `var` in C# when the type isn't obvious from the right-hand side
- Do not use `any` in TypeScript
- Do not add unnecessary abstractions or over-engineer solutions
