# Taffy Authentication & Security

## HTTP Basic Auth

Recommend using other auth methods over basic auth, but if basic auth is being used the following helper is available. Use with SSL only. Credentials easily sniffed without encryption.

```js
function onTaffyRequest(verb, cfc, requestArguments, mimeExt, headers, methodMetadata, matchedURI) {
    var creds = getBasicAuthCredentials();

    if (creds.username == "" || creds.password == "") {
        return noData().withStatus(401, "Unauthorized")
            .withHeaders({ "WWW-Authenticate": 'Basic realm="API"' });
    }

    if (!validateCredentials(creds.username, creds.password)) {
        return noData().withStatus(401, "Invalid Credentials");
    }

    // Pass user info to resources
    arguments.requestArguments.authenticatedUser = creds.username;
    return true;
}
```

## API Key Authentication

```js
function onTaffyRequest(verb, cfc, requestArguments, mimeExt, headers, methodMetadata, matchedURI) {
    var apiKey = "";

    // Check header first, then query param
    if (structKeyExists(headers, "X-API-KEY")) {
        apiKey = headers["X-API-KEY"];
    } else if (structKeyExists(requestArguments, "apiKey")) {
        apiKey = requestArguments.apiKey;
    }

    if (apiKey == "" || !isValidApiKey(apiKey)) {
        return noData().withStatus(401, "Invalid API Key");
    }

    arguments.requestArguments.apiKeyOwner = getKeyOwner(apiKey);
    return true;
}
```

## Role-Based Security

Use method metadata to define required roles.

**Resource:**

```js
function get(required numeric userId) taffy_verb="get" role="user_reader" {
    // Only accessible to users with "user_reader" role
}

function delete(required numeric userId) taffy_verb="delete" role="user_admin" {
    // Only accessible to users with "user_admin" role
}
```

**Application.cfc:**

```js
function onTaffyRequest(verb, cfc, requestArguments, mimeExt, headers, methodMetadata, matchedURI) {
    // Authenticate user first...
    var user = authenticateFromHeaders(headers);

    if (structKeyExists(methodMetadata, "role")) {
        var requiredRole = methodMetadata.role;
        if (!arrayFind(user.roles, requiredRole)) {
            return noData().withStatus(403, "Forbidden");
        }
    }

    arguments.requestArguments.currentUser = user;
    return true;
}
```

## Token-Based Auth (JWT-style)

```js
function onTaffyRequest(verb, cfc, requestArguments, mimeExt, headers, methodMetadata, matchedURI) {
    if (!structKeyExists(headers, "Authorization")) {
        return noData().withStatus(401);
    }

    var authHeader = headers.Authorization;
    if (!authHeader.startsWith("Bearer ")) {
        return noData().withStatus(401, "Invalid Authorization header");
    }

    var token = mid(authHeader, 8, len(authHeader));

    try {
        var payload = validateAndDecodeToken(token);
        arguments.requestArguments.tokenPayload = payload;
        return true;
    } catch (any e) {
        return noData().withStatus(401, "Invalid token");
    }
}
```

## CSRF Protection

Configure in variables.framework for dashboard:

```js
csrfToken = {
    cookieName = "XSRF-TOKEN",
    headerName = "X-XSRF-TOKEN"
}
```

Dashboard reads cookie and sends value in header. Implement verification in `onTaffyRequest` for your API endpoints if needed.

## Skipping Auth for Certain Endpoints

```js
function onTaffyRequest(verb, cfc, requestArguments, mimeExt, headers, methodMetadata, matchedURI) {
    // Skip auth for health checks and public endpoints
    var publicPaths = ["/health", "/public/"];
    for (var path in publicPaths) {
        if (matchedURI.startsWith(path)) {
            return true;
        }
    }

    // Normal auth for everything else
    return authenticateRequest(headers, requestArguments);
}
```
