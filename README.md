# URL-Shortner
This is system design for a service like **Bitly** that converts a long URL into a short link
```yaml
    https://example.com/products/123?campaign=summer
              ↓
    https://sho.rt/aZ91Kd
```
## 1. Requirements:

 ### Functional:

   - Create a short URL from a valid long URL.
   - Redirect a short URL to its destination.
   - Optionally allow links to expire.
   - Prevent different URLs from accidentally receiving the same code.

### Non-functional:

   - Redirects must be fast.
   - The service should remain available during traffic spikes.
   - Created links must not be lost.
   - Repeated requests may safely return the same result.

#### Assumption in the case of this system design:

10 million new links per month.
Redirect traffic is 100 times higher than creation traffic.
A redirect should usually complete within 100 ms.

The system is therefore read-heavy.

## 2. API Design

```yaml
# Query:
    POST /links
    Content-Type: application/json

    {
    "url": "https://example.com/products/123",
    "expires_at": "2026-12-31T23:59:59Z"
    }
```

```yaml
# Response:
    {
    "code": "aZ91Kd",
    "short_url": "https://sho.rt/aZ91Kd"
    }
```

```yaml
# Redirect:
    GET /aZ91Kd
```
```yaml
    HTTP/1.1 302 Found
    Location: https://example.com/products/123
```
#### Note:
- Use 302 if destinations may change or clicks must pass through the service. A 301 permits stronger browser caching but makes later destination changes harder to enforce.





