---
description: Structured workflow for debugging and fixing bugs with regression test coverage
---

# Fix Bug Workflow

## Prerequisites
- Bug ticket with reproduction steps
- Local environment is running (see `setup-local.md`)

## Steps

### 1. Create a Fix Branch
```bash
git checkout develop
git pull origin develop
git checkout -b fix/<ticket-id>-<short-description>
```

### 2. Reproduce the Bug
Before touching any code, **reproduce the bug locally**:
- Follow the exact reproduction steps from the ticket.
- Confirm you see the expected failure (error message, wrong behavior, crash).
- If you cannot reproduce it, add a comment to the ticket and stop. Do not guess-fix.

### 3. Isolate the Problem
Narrow down the root cause:
- **Backend:** Check logs (`uvicorn` output), add temporary `structlog` debug logging at suspected boundaries.
- **Frontend:** Check browser console errors, network tab (failed requests, wrong payloads), React DevTools.
- **Database:** Check if the data is in the expected state. Run the relevant query manually.

Ask yourself:
- When did this last work? (`git log` / `git bisect` can help)
- What changed since then? (`git diff develop..main`)
- Is this a data issue, a code issue, or an environment issue?

### 4. Write a Failing Test First
Before writing the fix, write a test that **reproduces the bug**:
```python
# Backend example
def test_order_total_with_zero_quantity_returns_error():
    """Regression test for BUG-123: zero quantity caused division by zero."""
    response = client.post("/api/v1/orders", json={"product_id": "...", "quantity": 0})
    assert response.status_code == 422
```
```typescript
// Frontend example
it('should show error message when API returns 500', async () => {
  // Regression test for BUG-123
  server.use(http.get('/api/v1/orders', () => HttpResponse.error()));
  render(<OrderList />);
  expect(await screen.findByText(/something went wrong/i)).toBeInTheDocument();
});
```
Run the test — it **should fail**. This confirms you've captured the bug.

### 5. Implement the Fix
- Make the **minimal change** needed to fix the bug. Resist the urge to refactor adjacent code.
- If the fix requires a broader refactor, create a separate ticket for that.
- Follow the relevant persona rules (`fastapi-rules.md`, `react-rules.md`).

### 6. Verify the Fix
```bash
# Run the specific regression test
pytest tests/ -k "test_order_total_with_zero_quantity" -v

# Run the full test suite to check for regressions
pytest tests/ -v
npm run test
```

### 7. Commit and Push
```bash
git add -A
git commit -m "fix(<scope>): <short description>

- Root cause: <what was actually wrong>
- Fix: <what you changed and why>
- Added regression test

Closes #<ticket-id>"
git push origin fix/<ticket-id>-<short-description>
```

### 8. Create PR
- **Title:** `fix(<scope>): <short description>`
- **Description:** Include:
  - Link to bug ticket
  - Root cause analysis (1–2 sentences)
  - What the fix does
  - How to verify
- **Labels:** `bug`

### 9. Post-Fix Checklist
- [ ] Bug is reproducible before the fix
- [ ] Regression test fails before the fix, passes after
- [ ] Full test suite passes
- [ ] No unrelated changes included
- [ ] PR description includes root cause analysis
