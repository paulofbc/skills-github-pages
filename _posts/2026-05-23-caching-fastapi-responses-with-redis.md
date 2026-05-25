---
title: "Caching FastAPI responses with Redis"
date: 2026-05-23
category: backend
tags: [python, fastapi, redis, performance]
description: A minimal pattern for caching slow GET endpoints in FastAPI with Redis, and the gotchas I hit along the way.
---

* TOC
{:toc}

> **TL;DR** — Wrap slow GET handlers with a small decorator that reads
> from Redis first and falls back to the function. Key on
> `method + path + sorted query params`. Use a JSON-safe serializer
> and a sane default TTL.

## Why

A read-heavy endpoint was being called 600+ times per second and hitting
the database on every request. Roughly 90% of those calls returned the
same payload within a 30-second window — perfect cache shape.

## The minimal pattern

```python
import json
from functools import wraps
from redis.asyncio import Redis

redis = Redis.from_url("redis://localhost:6379/0", decode_responses=True)

def cache(ttl: int = 30):
    def deco(fn):
        @wraps(fn)
        async def wrapper(*args, **kwargs):
            key = f"{fn.__module__}:{fn.__name__}:{json.dumps(kwargs, sort_keys=True)}"
            hit = await redis.get(key)
            if hit is not None:
                return json.loads(hit)
            value = await fn(*args, **kwargs)
            await redis.set(key, json.dumps(value), ex=ttl)
            return value
        return wrapper
    return deco
```

Usage:

```python
@app.get("/shipping-cost")
@cache(ttl=30)
async def shipping_cost(origin: str, destination: str, weight_g: int):
    return await expensive_lookup(origin, destination, weight_g)
```

## Gotchas

- **Don't cache POST/PUT.** Cache idempotent GETs only.
- **Stampede protection.** When the key expires under load, dozens of
  concurrent requests all miss at once. Either add a small random
  jitter to the TTL, or use a lock (`SET NX`) so only one request
  refreshes the cache.
- **Negative caching.** Cache "not found" results too, with a shorter
  TTL, otherwise a missing key keeps hitting the DB.
- **Serialization.** `json.dumps` rejects `datetime`/`Decimal`. Either
  serialize with a custom default or convert before returning.

## References

- [FastAPI — Background tasks & dependencies](https://fastapi.tiangolo.com/)
- [redis-py — async client](https://redis.readthedocs.io/en/stable/examples/asyncio_examples.html)
