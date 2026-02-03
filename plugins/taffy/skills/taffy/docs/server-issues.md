# Taffy Server-Specific Issues

## Tomcat / JBoss

### 404 on Subdirectory APIs

Vanilla Tomcat (not ACF's bundled version) may return 404 for APIs in subdirectories:

```
/api/index.cfm/myResource → 404 Not Found
```

**Fix:** Add servlet mapping to web.xml:

```xml
<servlet-mapping>
    <servlet-name>CFMLServlet</servlet-name>
    <url-pattern>/api/index.cfm/*</url-pattern>
</servlet-mapping>
```

Tomcat doesn't support two wildcards (`*/index.cfm/*`), so specify full path for each API.

## Lucee

### Custom HTTP Status Messages

Lucee's default Tomcat setup ignores custom status text:

```js
.withStatus(403, "Account Past Due")  // Shows as "403 Forbidden"
```

**Fix:** Add to catalina.properties:

```
org.apache.coyote.USE_CUSTOM_STATUS_MSG_IN_HEADER=true
```

Or pass status info in response body instead.

### PUT and FORM Scope

Pre-4.1.1.002 and pre-4.2 Lucee populated FORM scope on PUT requests. Not an issue for APIs but may cause test failures. Upgrade Lucee to fix.

## IIS

### Custom Error Bodies Ignored

IIS replaces non-200 response bodies with its own error pages.

**Fix:** Add web.config to webroot:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <httpErrors existingResponse="PassThrough" errorMode="Custom" />
    </system.webServer>
</configuration>
```

Do NOT use "Detailed Errors" setting—it leaks sensitive data.

## Symlinks

Adobe ColdFusion handles symlinks in web root poorly. Avoid them entirely.

## URL Rewriting

For clean URLs without index.cfm, configure your web server's rewrite rules. See Taffy wiki for examples:

- Apache mod_rewrite
- IIS URL Rewrite
- nginx rewrite

Basic pattern: rewrite requests to `/api/resource` to `/api/index.cfm/resource`.
