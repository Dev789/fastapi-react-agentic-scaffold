---
description: Safely integrate a third-party API with error handling, retry logic, and testability
---

# Add Third-Party API Integration Workflow

## When to Use
When integrating with any external service (payment gateway, email provider, maps API, analytics, AI model, etc.).

## Steps

### 1. Research & Document
Before writing any code:
- [ ] Read the API documentation thoroughly.
- [ ] Identify: authentication method, rate limits, pricing, SLA guarantees.
- [ ] Document the integration in an ADR (see `write-adr.md`):
  - Why this provider? What alternatives were considered?
  - What happens if the service goes down?

### 2. Create an API Service Wrapper
Create a dedicated service file — never call external APIs directly from route handlers:

```
backend/app/services/external/
├── __init__.py
├── base.py              # Base HTTP client with retry logic
├── <provider>.py        # Provider-specific wrapper
└── <provider>_types.py  # Request/response types for the provider
```

#### Base Client Pattern
```python
# backend/app/services/external/base.py
import httpx
from tenacity import retry, stop_after_attempt, wait_exponential

class ExternalServiceBase:
    def __init__(self, base_url: str, api_key: str, timeout: float = 10.0):
        self.client = httpx.AsyncClient(
            base_url=base_url,
            headers={"Authorization": f"Bearer {api_key}"},
            timeout=timeout,
        )

    @retry(stop=stop_after_attempt(3), wait=wait_exponential(min=1, max=10))
    async def _request(self, method: str, path: str, **kwargs):
        response = await self.client.request(method, path, **kwargs)
        response.raise_for_status()
        return response.json()
```

#### Provider Wrapper Pattern
```python
# backend/app/services/external/<provider>.py
from app.services.external.base import ExternalServiceBase
from app.core.config import settings

class StripeService(ExternalServiceBase):
    def __init__(self):
        super().__init__(
            base_url="https://api.stripe.com/v1",
            api_key=settings.STRIPE_API_KEY,
        )

    async def create_payment_intent(self, amount: int, currency: str = "usd"):
        return await self._request("POST", "/payment_intents", data={
            "amount": amount,
            "currency": currency,
        })
```

### 3. Add Configuration
Add required environment variables to `backend/app/core/config.py`:
```python
class Settings(BaseSettings):
    # ... existing settings ...
    STRIPE_API_KEY: str
    STRIPE_WEBHOOK_SECRET: str
```
Update `backend/.env.example` with placeholder values.

### 4. Add Error Handling
Define provider-specific exceptions:
```python
# backend/app/core/exceptions.py
class ExternalServiceError(Exception):
    """Base exception for external API failures."""
    def __init__(self, service: str, message: str, status_code: int = 502):
        self.service = service
        self.message = message
        self.status_code = status_code
```

Handle gracefully in the route handler:
```python
try:
    result = await stripe_service.create_payment_intent(amount=1000)
except ExternalServiceError as e:
    raise HTTPException(status_code=e.status_code, detail=f"{e.service}: {e.message}")
```

### 5. Add Circuit Breaker (Optional but Recommended)
For critical integrations, prevent cascading failures:
```python
# pip install circuitbreaker
from circuitbreaker import circuit

class StripeService(ExternalServiceBase):
    @circuit(failure_threshold=5, recovery_timeout=30)
    async def create_payment_intent(self, amount: int, currency: str = "usd"):
        ...
```

### 6. Create Mock for Testing
```python
# backend/tests/mocks/mock_stripe.py
class MockStripeService:
    async def create_payment_intent(self, amount: int, currency: str = "usd"):
        return {
            "id": "pi_test_123",
            "amount": amount,
            "currency": currency,
            "status": "requires_payment_method",
        }
```

Use in tests via dependency override:
```python
# backend/tests/conftest.py
@pytest.fixture
def mock_stripe(app):
    app.dependency_overrides[get_stripe_service] = lambda: MockStripeService()
    yield
    app.dependency_overrides.clear()
```

### 7. Write Tests
```python
# backend/tests/unit/test_stripe_service.py
async def test_create_payment_intent_success(mock_stripe, client):
    response = await client.post("/api/v1/payments", json={"amount": 1000})
    assert response.status_code == 201

async def test_create_payment_intent_service_down(client):
    # Mock the service to raise ExternalServiceError
    response = await client.post("/api/v1/payments", json={"amount": 1000})
    assert response.status_code == 502
```

### 8. Frontend Integration
If the frontend calls the external API directly (e.g., Stripe Elements):
- Load the SDK via a dedicated hook: `useStripe()`.
- Handle the API key via `import.meta.env.VITE_STRIPE_PUBLISHABLE_KEY`.
- Never expose secret keys on the frontend.

### 9. Integration Checklist
- [ ] ADR documented (why this provider, alternatives, failure handling)
- [ ] Service wrapper in `services/external/<provider>.py`
- [ ] API key in settings (`core/config.py`) and `.env.example`
- [ ] Retry logic with exponential backoff
- [ ] Circuit breaker for critical integrations
- [ ] Custom error handling (`ExternalServiceError`)
- [ ] Mock created for testing
- [ ] Unit tests (success + failure scenarios)
- [ ] Integration test with real API on staging (optional)
- [ ] Frontend SDK loaded securely (no secret keys exposed)
- [ ] Rate limits documented and respected
