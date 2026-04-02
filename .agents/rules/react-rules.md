# React (Vite) Frontend Rules

## 1. Stack & Tooling
- **Build:** Vite exclusively. No Create React App.
- **Language:** TypeScript strict mode. No `any` types unless explicitly justified with a `// REASON:` comment.
- **Styling:** Tailwind CSS. Use `@apply` sparingly — prefer composing utility classes directly in JSX. Extract repeated patterns into component-level classes only.
- **State Management:** Redux Toolkit for global/server state. Use `createSlice` and `createAsyncThunk`. Avoid raw `useReducer` for shared state.
- **Routing:** React Router v6+ with lazy-loaded route components via `React.lazy()` + `Suspense`.
- **HTTP Client:** Axios with a centralized instance in `src/api/client.ts`. Attach auth tokens via interceptors.

## 2. Component Rules
- **Functional only.** No class components.
- **One component per file.** File name matches component name in `PascalCase` (e.g., `UserCard.tsx`).
- **Props:** Define props with a named `interface` (e.g., `interface UserCardProps`). No inline type literals for props.
- **Default exports** for page components. **Named exports** for reusable components.
- **No business logic in components.** Extract into custom hooks (`src/hooks/`) or Redux thunks.

## 3. Hooks
- Prefer **custom hooks** for any non-trivial logic. Place in `src/hooks/`.
- Minimize `useEffect` to actual side-effects (subscriptions, DOM mutations, analytics). Never use `useEffect` as a state-sync mechanism.
- Always provide a cleanup function in `useEffect` when subscribing to external resources.
- Memoize expensive computations with `useMemo` and stable callback references with `useCallback` — but do not prematurely optimize.

## 4. API Layer
- All API calls go through `src/api/`. One file per resource (e.g., `src/api/users.ts`).
- Return typed responses. Never use raw `fetch` directly in components.
- Handle loading, error, and success states explicitly in Redux slices (use `extraReducers` with `createAsyncThunk` lifecycle).

## 5. Error Handling
- Wrap route trees with `ErrorBoundary` components. Provide user-friendly fallback UIs.
- Use a global error boundary at the `App` level and feature-level boundaries for critical sections.
- API errors must surface user-readable messages — never expose raw stack traces.

## 6. Forms
- Use a form library (React Hook Form preferred) for anything beyond a single input.
- Validate with Zod schemas. Share validation logic with backend Pydantic schemas where possible.

## 7. Accessibility
- All images must have `alt` attributes.
- Interactive elements must be keyboard-navigable.
- Use semantic HTML (`<nav>`, `<main>`, `<section>`, `<button>`) over generic `<div>` with click handlers.
- Color contrast must meet WCAG 2.1 AA standards.

## 8. Performance
- Lazy-load routes and heavy components.
- Use `React.memo` only for components that re-render with identical props frequently.
- Avoid inline object/array literals in JSX props (causes unnecessary re-renders).
- Images: use WebP format, provide `width`/`height`, and lazy-load below the fold.

## 9. Environment Variables
- Access via `import.meta.env.VITE_*`. Never prefix with `REACT_APP_`.
- Document all required variables in `frontend/.env.example`.

## 10. File & Folder Structure
```
src/
├── api/          # Axios client + per-resource API modules
├── app/          # Redux store setup (store.ts, hooks.ts)
├── assets/       # Static assets
├── components/   # Reusable components (grouped by domain)
│   └── ui/       # Primitives: Button, Input, Modal, etc.
├── features/     # Feature slices (Redux slice + feature components)
├── hooks/        # Custom hooks
├── layouts/      # Layout wrappers (AuthLayout, DashboardLayout)
├── pages/        # Route-level components
├── types/        # Shared TypeScript interfaces/types
├── utils/        # Pure helper functions
├── App.tsx       # Root component with router
└── main.tsx      # Entry point
```
