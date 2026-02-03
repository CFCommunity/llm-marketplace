# Taffy URI and Metadata Configuration

## taffy:uri / taffy_uri

Defines which URIs a resource handles. Set on component.

```js
component extends="taffy.core.resource" taffy_uri="/users/{userId}" {
}
```

Or in tag syntax:

```xml
<cfcomponent extends="taffy.core.resource" taffy:uri="/users/{userId}">
</cfcomponent>
```

### Tokens

Curly braces define dynamic URI segments. Token names become argument names.

```js
component taffy_uri="/artists/{artistId}/albums/{albumId}" {
    function get(required numeric artistId, required numeric albumId) {
        // artistId and albumId populated from URI
    }
}
```

Tokens must be separated by at least one character (usually `/`).

**Invalid:** `/artist/{artistId}{artistName}` (consecutive tokens)

### URI Matching Order

URIs are sorted so static segments match before tokens:

- `/artists/list` matches before `/artists/{id}`

Design URIs to avoid conflicts. If artist ID could be "list", reconsider URI design.

## taffy:verb / taffy_verb

Override default verb-to-method mapping or support additional verbs (OPTIONS, HEAD).

```js
function getUser(numeric userId) taffy_verb="get" {
}

function handleOptions() taffy_verb="options" {
}
```

Default mapping (no taffy:verb needed):

- `get()` → GET
- `post()` → POST
- `put()` → PUT
- `delete()` → DELETE

## Hiding from Dashboard/Docs

### taffy:dashboard:hide / taffy_dashboard_hide

Hide resource from dashboard.

```js
component taffy_uri="/internal/health" taffy_dashboard_hide {
}
```

### taffy:docs:hide / taffy_docs_hide

Hide from generated documentation. Can apply to:

- Component (entire resource)
- Function (specific method)
- Argument (specific parameter)

```js
component taffy_uri="/users/{userId}" taffy_docs_hide {
}
```

```js
function get(
    required numeric userId,
    string _internal = "" taffy_docs_hide
) taffy_docs_hide {
}
```

### taffy:docs:name / taffy_docs_name

Override resource name in documentation.

```js
component taffy_uri="/u/{id}" taffy_docs_name="User Management" {
}
```

## Sample Responses

Add `sample{Method}Response` methods to provide simulated responses for dashboard/docs.

```js
function sampleGetResponse() {
    return { id: 1, name: "Example User", email: "user@example.com" };
}

function get(required numeric userId) {
    // actual implementation
}
```

Request simulated response: `?sampleResponse=true`

Configure via `simulateKey` and `simulatePassword` in variables.framework.
