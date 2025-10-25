---
hide:
  - navigation
---

# Format

## Rate limit string notation

Instead of manually constructing instances of `RateLimitItem`
you can instead use the following `Parsing functions`.

- `parse()`
- `parse_many()`

These functions accept rate limits specified as strings following the format::

```
[count] [per|/] [n (optional)] [second|minute|hour|day|month|year]
```

You can combine rate limits by separating them with a delimiter of your choice.

## Examples

* `10 per hour`
* `10/hour`
* `10/hour;100/day;2000 per year`
* `100/day, 500/7days`