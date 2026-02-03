# Taffy Configuration (variables.framework)

Set in Application.cfc before calling `super.onApplicationStart()`.

## Default Settings

```js
variables.framework = {
    reloadKey = "reload",
    reloadPassword = "true",
    reloadOnEveryRequest = false,

    simulateKey = "sampleResponse",
    simulatePassword = "true",

    endpointURLParam = "endpoint",

    serializer = "taffy.core.nativeJsonSerializer",
    deserializer = "taffy.core.nativeJsonDeserializer",

    disableDashboard = false,
    disabledDashboardRedirect = "",
    dashboardHeaders = {},
    showDocsWhenDashboardDisabled = false,
    docs = { APIName = "", APIVersion = "" },

    jsonp = false,

    csrfToken = { cookieName = "", headerName = "" },

    unhandledPaths = "/flex2gateway",
    allowCrossDomain = false,
    globalHeaders = {},
    debugKey = "debug",

    useEtags = false,

    returnExceptionsAsJson = true,
    exceptionLogAdapter = "taffy.bonus.LogToScreen",
    exceptionLogAdapterConfig = {},

    beanFactory = "",
    environments = {}
};
```

## Key Settings

### Reload

- `reloadKey` / `reloadPassword`: URL param to reinitialize API (`?reload=true`)
- `reloadOnEveryRequest`: Set true in dev, false in production

### Serialization

- `serializer`: CFC path or bean name for output serialization
- `deserializer`: CFC path or bean name for input deserialization

### Dashboard & Docs

- `disableDashboard`: Hide dashboard in production
- `disabledDashboardRedirect`: URL to redirect when dashboard disabled
- `showDocsWhenDashboardDisabled`: Show docs even without dashboard
- `docs.APIName` / `docs.APIVersion`: Display in docs

### CORS

- `allowCrossDomain`: Set `true` for `*` or comma/semicolon/space-separated list of allowed origins

```js
allowCrossDomain = "http://example.com; http://foo.bar"
```

### JSONP

- `jsonp`: Set to callback parameter name (e.g., "callback") to enable JSONP for GET requests

### ETags

- `useEtags`: Enable HTTP ETag caching

### Error Handling

- `returnExceptionsAsJson`: Format errors as JSON (includes stack trace)
- `exceptionLogAdapter`: CFC for logging exceptions
- `exceptionLogAdapterConfig`: Config for adapter

### Paths

- `unhandledPaths`: Comma-delimited paths Taffy should ignore
- `endpointURLParam`: Query param for URI (`?endpoint=/users`)

### Bean Factory

- `beanFactory`: External DI container instance (ColdSpring, DI/1, etc.)

### Environments

Override settings per environment:

```js
environments = {
    production = {
        disableDashboard = true,
        reloadOnEveryRequest = false
    },
    development = {
        reloadOnEveryRequest = true
    }
}
```

Requires implementing `getEnvironment()` method.
