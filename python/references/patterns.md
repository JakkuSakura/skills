# Strict Python Rewrite Patterns

## Field access
- Avoid: `obj.get("price", 0)`
- Prefer:
  - `if "price" not in obj: raise ValueError("Missing price")`
  - `price = obj["price"]`

## Attribute access
- Avoid: `getattr(order, "id", None)`
- Prefer: `order.id`

## Type checks
- Avoid: `if isinstance(msg, dict): ...`
- Prefer:
  - Use protocol/expected shape.
  - Attempt direct access and fail with explicit message when missing.

## Exception handling
- Avoid:
  - broad `try/except Exception` around normal control flow
- Prefer:
  - straight-line logic
  - a narrow `try/except` only for a required fallback boundary (e.g., alternate backend call)
