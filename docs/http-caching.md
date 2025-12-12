---
title: HTTP Caching Guide
summary: Introduces caching benefits, locations, headers, validation, and best practices for HTTP responses.
---

<!-- markdownlint-disable MD025 MD003 MD022 MD026 MD024 MD012 -->

# HTTP Caching Guide

## Why cache?

Caching speeds page loads, reduces server load/bandwidth, and improves UX by serving buffers from intermediate stores instead of origin.

## Core terms

- **Client**: browser/app requesting resources.
- **Origin server**: source of truth content.
- **Stale content**: expired cache entry.
- **Fresh content**: cache entry still within TTL.
- **Cache validation**: re-checking origin before serving expired content.
- **Cache invalidation**: removing stale entries.

## Cache locations

- **Browser cache**: per-user, stores private responses. Hitting “Back” often serves cached content.
- **Proxy cache**: shared caches (ISPs) serving many users.
- **Reverse proxy cache**: sits near origin to offload server traffic; controlled by admins.

Browsers/proxies auto-cache defaults you can’t fully control; ensure your headers guide them correctly.

## Key headers

### Cache-Control directives

- `private/public`
- `no-store`, `no-cache`
- `max-age=seconds`, `s-maxage=seconds`
- `must-revalidate`, `proxy-revalidate`
- Combine directives carefully; `no-store` overrides `no-cache`, `s-maxage` overrides `max-age` for shared caches.

### Validators

- `ETag` (strong/weak): conditional `If-None-Match`.
- `Last-Modified`: conditional `If-Modified-Since`.
- Servers should include both validators whenever practical and respond with `304 Not Modified`.

## Server-side caching

- Configure Apache (`Header set Cache-Control ...`) or Nginx to emit cache directives per file type (images for a year, CSS/JS for a month).
- Fingerprint static assets (e.g. `style.hash.css`) so long TTLs work safely.

## Recommendations

- Aggressive caching on static assets (max 1 year).
- Cache dynamic content only if safe; prefer shorter TTLs.
- Always include validators (ETag/Last-Modified).
- Control visibility (`private` for user-specific, `public` for shared resources).
- Separate frequently changing vs stable content.
- Monitor headers with browser dev tools or `curl -I`.

## Summary

Caching is a balancing act—give static files long TTLs while validating dynamic responses, and use headers strategically to guide browsers, proxies, and reverse proxies.
---
title: HTTP Caching Guide
summary: Concepts, headers, caching locations, and best practices for controlling web cache behavior.
---

# HTTP Caching Guide

## Why caching matters

- Reduces latency, speeds page loads, cuts bandwidth, lowers server load.
- Caches sit between client & server to reuse responses when requests repeat.

## Key players

- **Client**: browser or application making the request.
- **Origin server**: source of truth delivering responses.
- **Fresh content**: cached item still valid.
- **Stale content**: cached item expired.
- **Cache validation**: server check to confirm content remains valid.
- **Cache invalidation**: removing stale entries when policies change.

## Caching locations

- **Browser cache**: per-user, stores private responses.
- **Proxy cache**: shared across users, operated by ISPs or organizations.
- **Reverse proxy cache**: controlled near origin servers to reduce load.

You can’t control browser/proxy caches directly—your headers must guide them.

## Controlling caches via headers

### Expires

- Pre-HTTP/1.1 header giving an absolute expiry date (GMT). Duration limited to 1 year.

### Cache-Control

HTTP/1.1 replacement for Expires. You can combine directives:

- `public`/`private`: public caches allowed, private limits to client.
- `no-store`: never cache.
- `no-cache`: must revalidate before reuse.
- `max-age`: lifespan in seconds.
- `s-maxage`: overrides `max-age` for shared caches.
- `must-revalidate`: stale content must be validated before serving.
- `proxy-revalidate`: same as `must-revalidate` but for shared caches.

### Validators

- `ETag`: strong or weak identifiers. Clients send `If-None-Match` to avoid full downloads when unchanged.
- `Last-Modified`: timestamp; clients send `If-Modified-Since`.
- Use both for strong validation; any mismatch triggers a full download.

## Practical recommendations

- Cache static assets aggressively (fingerprint filenames).
- Avoid caching sensitive data publicly; prefer `private`.
- Use validators (ETag) for dynamic responses.
- Separate frequently changing content.
- Monitor headers with browser devtools or `curl -I`.
- Implement server policies via Apache `.htaccess` or Nginx `location`/`server` blocks (examples given).

## Tools & concepts

- `.htaccess` and server configs can set caching without touching application code.
- Directives can combine via `filesMatch` to tailor caching per file type.
- When unsure about caching, lean toward no-cache/no-store.

