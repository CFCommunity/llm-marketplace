# Taffy Quickstart

## Minimal API Setup

**Application.cfc:**

```js
component extends="taffy.core.api" {}
```

**index.cfm:**

```html
<!-- this space left intentionally blank -->
```

**/resources/hello.cfc:**

```js
component extends="taffy.core.resource" taffy_uri="/hello" {
    function get() {
        return rep(['hello', 'world']);
    }
}
```

Point browser to the empty index.cfm to see the dashboard.

## Installation Options

### Global /taffy Mapping (Recommended)

Keep Taffy out of web root. Create global mapping in CF Administrator pointing `/taffy` to install location.

### /taffy in Web Root

Put the /taffy folder at root of domain.

### Sub-folder Installation

For application-specific Taffy versions, add these mappings to Application.cfc:

```js
this.mappings["/taffy"] = expandPath("./taffy");
this.mappings["/resources"] = expandPath("./resources");
```

Directory structure:

```
/your-api
├── Application.cfc
├── index.cfm
├── /taffy
│   ├── /bonus
│   ├── /core
│   └── /dashboard
└── /resources
    └── someResource.cfc
```

## Override HTTP Method

For clients that can't send PUT/DELETE, send POST with header:

```
X-HTTP-METHOD-OVERRIDE: PUT
```

or

```
X-HTTP-METHOD-OVERRIDE: DELETE
```
