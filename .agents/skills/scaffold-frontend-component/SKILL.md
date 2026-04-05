---
name: scaffold-frontend-component
description: Create a React component or feature slice with typed props, optional hook extraction, Redux integration, and tests. Use when adding a new UI component, page section, or feature-scoped frontend flow that should follow the repository conventions.
---

# Scaffold Frontend Component

Follow these steps while consulting `.agents/rules/react-rules.md` and `.agents/rules/qa.md`.

## 1. Component File
Create `frontend/src/components/<ComponentName>.tsx` or a feature-scoped component in `frontend/src/features/<feature>/`:
- Use a functional component.
- Define an explicit `interface <ComponentName>Props`.
- Use named export for reusable components and default export for pages.
- Style with Tailwind CSS utility classes.
- Ensure accessibility with semantic HTML and relevant ARIA attributes.

## 2. Custom Hook
If the component needs non-trivial logic, create `frontend/src/hooks/use<HookName>.ts`:
- Extract state, effects, and business logic into the hook.
- Return a typed object rather than a tuple.
- Handle loading, error, and success states explicitly.

## 3. Types
If shared types are needed, add them to `frontend/src/types/<resource>.ts`:
- Define interfaces that match backend API responses.
- Export types with named exports.

## 4. Redux Integration
If the component interacts with global state, create or update `frontend/src/features/<feature>/<feature>Slice.ts`:
- Use `createSlice` for synchronous state.
- Use `createAsyncThunk` for API calls.
- Export the reducer, actions, and typed selectors.

## 5. Test File
Create a colocated or nearby test file such as `frontend/src/components/<ComponentName>.test.tsx`:
- Use Vitest and React Testing Library.
- Test default rendering.
- Test user interactions.
- Test loading, error, and empty states where applicable.
- Mock API calls with `msw` when network behavior is involved.

## 6. Checklist
- [ ] Component file created with typed props
- [ ] Tailwind classes used and inline styles avoided
- [ ] Accessibility handled with semantic HTML and keyboard support
- [ ] Custom hook extracted when logic is non-trivial
- [ ] Shared types added when new data shapes are introduced
- [ ] Redux slice created or updated when global state is involved
- [ ] Test file created with meaningful assertions
