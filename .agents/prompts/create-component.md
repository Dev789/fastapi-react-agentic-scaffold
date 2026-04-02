# Task: Create a new React Component

Please generate a new component named: **[INSERT COMPONENT NAME HERE]**

Follow these exact steps, adhering to `.agents/rules/react-rules.md`:

## 1. Component File
Create `frontend/src/components/<ComponentName>.tsx` (or `frontend/src/features/<feature>/<ComponentName>.tsx` for feature-scoped components):
- Functional component with explicit `interface <ComponentName>Props`.
- Use **named export** for reusable components, **default export** for pages.
- Style with Tailwind CSS utility classes.
- Ensure accessibility: semantic HTML, `aria-*` attributes where needed, keyboard navigation.

## 2. Custom Hook (if needed)
If the component requires non-trivial logic, create `frontend/src/hooks/use<HookName>.ts`:
- Extract state, effects, and business logic into the hook.
- Return a typed object (not a tuple) for clarity.
- Handle loading, error, and success states.

## 3. Types
If new shared types are needed, add to `frontend/src/types/<resource>.ts`:
- Define `interface` for data shapes matching backend API responses.
- Export all types with named exports.

## 4. Redux Integration (if needed)
If the component interacts with global state, create or update `frontend/src/features/<feature>/<feature>Slice.ts`:
- Use `createSlice` for synchronous state.
- Use `createAsyncThunk` for API calls.
- Export the reducer, actions, and typed selectors.

## 5. Test File
Create `frontend/src/components/<ComponentName>.test.tsx`:
- Use Vitest + React Testing Library.
- Test rendering with default props.
- Test user interactions (clicks, inputs, form submissions).
- Test error and loading states.
- Mock API calls with `msw` if the component fetches data.

## 6. Checklist
- [ ] Component file created with typed props
- [ ] Tailwind classes used (no inline styles)
- [ ] Accessible: semantic HTML, keyboard navigable, proper `aria-*`
- [ ] Custom hook extracted (if applicable)
- [ ] Types defined in `types/` (if new data shapes)
- [ ] Redux slice created/updated (if global state needed)
- [ ] Test file created with meaningful assertions
