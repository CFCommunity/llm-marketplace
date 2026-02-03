---
name: taffy
description: Taffy is a CFML framework for writing REST APIs on ColdFusion and Lucee. Use when working on CFC files with component metadata extends="taffy.core.resource" or extends="taffy.core.api", or when otherwise aware you're working on a Taffy API resource or configuration.
---

# Taffy REST Framework

Resources are CFCs that handle requests. They extend `taffy.core.resource` and use `taffy:uri` (or `taffy_uri`) metadata to define endpoints. HTTP verbs map to method names.

```js
component extends="taffy.core.resource" taffy_uri="/users/{userId}" {
    function get(required numeric userId) {
        return rep({ id: userId, name: "Example" });
    }
    function post(required string name) {
        // create user
        return rep({ id: 123, name: name }).withStatus(201, "Created");
    }
}
```

## Documentation Files

Read these as needed based on the task:

**docs/quickstart.md** - New API setup, installation options, minimal hello world. Read when creating a new Taffy API.

**docs/resources.md** - Resource CFC methods: `rep()`, `noData()`, `noContent()`, `withStatus()`, `withHeaders()`, `qToArray()`, `qToStruct()`, `streamBinary()`, `streamFile()`, `streamImage()`, `encode.string()`. Read when writing resource handler methods.

**docs/configuration.md** - `variables.framework` settings: reload, serializer, deserializer, dashboard, CORS, ETags, JSONP, CSRF, environments. Read when configuring API behavior in Application.cfc.

**docs/application-cfc.md** - Application.cfc methods: `onTaffyRequest()`, `onTaffyRequestEnd()`, `getBasicAuthCredentials()`, `getEnvironment()`, bean factory integration. Read when adding request hooks or auth.

**docs/uri-metadata.md** - URI tokens, matching order, `taffy:uri`, `taffy:verb`, `taffy:dashboard:hide`, `taffy:docs:hide`, sample responses. Read when designing URIs or hiding endpoints.

**docs/serializers.md** - Custom serializers and deserializers, `taffy:mime`, `taffy:default`, multi-format APIs (JSON, XML). Read when customizing request/response formats.

**docs/caching.md** - Caching hooks: `validCacheExists()`, `getCachedResponse()`, `setCachedResponse()`, `getCacheKey()`. Read when implementing API response caching.

**docs/authentication.md** - Basic auth, API keys, role-based security, JWT patterns, `onTaffyRequest()` for auth. Read when implementing authentication.

**docs/server-issues.md** - Tomcat 404s, Lucee custom status messages, IIS error responses, symlinks. Read when troubleshooting deployment.
