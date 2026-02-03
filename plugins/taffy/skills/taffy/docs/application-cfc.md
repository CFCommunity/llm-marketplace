# Taffy Application.cfc Methods

Application.cfc extends `taffy.core.api`. The following methods can be overridden or called.

## Request Lifecycle Hooks

### onTaffyRequest(verb, cfc, requestArguments, mimeExt, headers, methodMetadata, matchedURI)

Inspect/abort requests before they reach resources. Return `true` to continue, or return `noData()`/`rep()` to abort.

```js
function onTaffyRequest(verb, cfc, requestArguments, mimeExt, headers, methodMetadata, matchedURI) {
    // Check API key
    if (!structKeyExists(headers, "X-API-KEY") || !isValidKey(headers["X-API-KEY"])) {
        return noData().withStatus(401, "Unauthorized");
    }

    // Add data to requestArguments (passed to resource)
    arguments.requestArguments.userId = getUserFromKey(headers["X-API-KEY"]);

    return true;
}
```

Arguments:

- `verb`: HTTP method (GET, POST, etc.)
- `cfc`: Resource CFC name (bean name if using external factory)
- `requestArguments`: Struct of URI tokens + query params (mutable)
- `mimeExt`: Requested format extension ("json")
- `headers`: Request headers struct
- `methodMetadata`: Non-Taffy metadata on resource method
- `matchedURI`: Matched taffy:uri pattern (`/users/{userId}`)

### onTaffyRequestEnd(verb, cfc, requestArguments, mimeExt, headers, methodMetadata, matchedURI, parsedResponse, originalResponse, statusCode)

Runs after request completes. Useful for logging. No return value needed.

```js
function onTaffyRequestEnd(verb, cfc, requestArguments, mimeExt, headers, methodMetadata, matchedURI, parsedResponse, originalResponse, statusCode) {
    logRequest(verb, matchedURI, statusCode);
}
```

## Environment

### getEnvironment()

Override to return current environment name. Used to apply environment-specific settings. (see variables.framework in configuration.md)

```js
function getEnvironment() {
    if (findNoCase("prod", cgi.server_name)) {
        return "production";
    }
    return "development";
}
```

## Response Methods (in onTaffyRequest)

### rep(data) / representationOf(data)

Return data and abort request.

### noData()

Abort with empty body.

### noContent()

Abort with 204 status and empty body.

All support chaining `.withStatus()` and `.withHeaders()`.

## Bean Factory

### getBeanFactory()

Returns configured bean factory (Taffy's internal or external).

### getExternalBeanFactory()

Returns only the external bean factory, even when also using `/resources` folder.

## Path Override

### getPath()

Override if ACF is installed on JEE server (Tomcat, Glassfish) with non-standard path resolution.
