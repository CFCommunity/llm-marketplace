# Taffy Caching Hooks

Implement custom response caching (Redis, Memcached, EhCache, etc.) by overriding these methods in Application.cfc.

## Methods to Implement

### getCacheKey(cfc, requestArguments, matchedURI)

Generate unique cache key for request. Called before checking cache.

```js
function getCacheKey(cfc, requestArguments, matchedURI) {
    // Create unique key from URI and relevant arguments
    var key = arguments.matchedURI;
    for (var arg in arguments.requestArguments) {
        key &= "_" & arg & "=" & arguments.requestArguments[arg];
    }
    return hash(key);
}
```

### validCacheExists(cacheKey)

Check if valid (non-expired) cache exists. Return `true` if cached, `false` otherwise.

```js
function validCacheExists(cacheKey) {
    return application.cache.exists(arguments.cacheKey)
        && !application.cache.isExpired(arguments.cacheKey);
}
```

### getCachedResponse(cacheKey)

Return cached response data. Only called after `validCacheExists` returns true.

```js
function getCachedResponse(cacheKey) {
    return application.cache.get(arguments.cacheKey);
}
```

### setCachedResponse(cacheKey, data)

Store response in cache. Called after resource returns data.

```js
function setCachedResponse(cacheKey, data) {
    application.cache.set(arguments.cacheKey, arguments.data, createTimeSpan(0, 0, 5, 0));
}
```

## Complete Example

```js
component extends="taffy.core.api" {

    function getCacheKey(cfc, requestArguments, matchedURI) {
        var parts = [arguments.matchedURI];
        var sortedKeys = structKeyArray(arguments.requestArguments).sort("textnocase");
        for (var key in sortedKeys) {
            arrayAppend(parts, key & "=" & arguments.requestArguments[key]);
        }
        return hash(arrayToList(parts, "|"), "SHA-256");
    }

    function validCacheExists(cacheKey) {
        return structKeyExists(application, "apiCache")
            && structKeyExists(application.apiCache, arguments.cacheKey)
            && application.apiCache[arguments.cacheKey].expires > now();
    }

    function getCachedResponse(cacheKey) {
        return application.apiCache[arguments.cacheKey].data;
    }

    function setCachedResponse(cacheKey, data) {
        if (!structKeyExists(application, "apiCache")) {
            application.apiCache = {};
        }
        application.apiCache[arguments.cacheKey] = {
            data: arguments.data,
            expires: dateAdd("n", 5, now())
        };
    }
}
```

## Notes

- Caching hooks only apply to GET requests
- Cache invalidation is your responsibility
- Consider cache key collisions with different users/permissions
- ETags (`variables.framework.useEtags=true`) provide browser-side caching without server implementation
