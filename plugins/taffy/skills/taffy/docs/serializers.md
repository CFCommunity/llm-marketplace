# Taffy Serializers and Deserializers

## Custom Serializers

Serializers convert native data to response formats (JSON, XML, etc.).

### Creating a Serializer

Extend `taffy.core.baseSerializer` and implement `getAs{Format}` methods with `taffy:mime` attribute.

```js
component extends="taffy.core.baseSerializer" {

    function getAsJson() taffy_mime="application/json" taffy_default="true" {
        return serializeJSON(variables.data);
    }

    function getAsXml() taffy_mime="application/xml" {
        return convertToXML(variables.data);
    }
}
```

- `variables.data` contains data from resource's `rep()` call
- `taffy:mime` defines Content-Type header and Accept header matching
- `taffy:default="true"` marks default format when none specified

### Format Selection

Clients specify format via:

- URL extension: `/users/42.json` or `/users/42.xml`
- Accept header: `Accept: application/json`

### Configure

```js
variables.framework.serializer = "path.to.MySerializer";
// or bean name if using external factory
variables.framework.serializer = "mySerializerBean";
```

## Custom Deserializers

Deserializers parse request bodies into native data.

### Creating a Deserializer

Extend `taffy.core.baseDeserializer` and implement `getFrom{Format}` methods.

```js
component extends="taffy.core.baseDeserializer" {

    function getFromJson(required string body) taffy_mime="application/json,text/json" {
        if (!isJson(arguments.body)) {
            throwError(msg="Invalid JSON", statusCode=400);
        }
        var data = deserializeJSON(arguments.body);
        if (!isStruct(data)) {
            return { "_body": data };
        }
        return data;
    }

    function getFromXml(required string body) taffy_mime="application/xml,text/xml" {
        // parse XML to struct
        return xmlToStruct(arguments.body);
    }
}
```

### Base Class Methods

`taffy.core.baseDeserializer` provides:

- `getFromForm()` - handles form posts (default)
- `throwError(msg, statusCode, headers?)` - abort with error response
- `addHeaders(struct)` - add response headers

### Configure

```js
variables.framework.deserializer = "path.to.MyDeserializer";
```

## Default Classes

- Serializer: `taffy.core.nativeJsonSerializer` (uses `serializeJSON`)
- Deserializer: `taffy.core.nativeJsonDeserializer` (uses `deserializeJSON`)

## Multiple Format Example

```js
component extends="taffy.core.baseSerializer" {

    function getAsJson() taffy_mime="application/json" taffy_default="true" {
        return application.jsonUtil.serialize(variables.data);
    }

    function getAsXml() taffy_mime="application/xml" {
        return application.xmlUtil.toXML(variables.data);
    }

    function getAsYaml() taffy_mime="application/x-yaml" {
        return application.yamlUtil.dump(variables.data);
    }
}
```

Clients request via:

- `/resource.json`, `/resource.xml`, `/resource.yaml`
- `Accept: application/json`, `Accept: application/xml`, etc.
