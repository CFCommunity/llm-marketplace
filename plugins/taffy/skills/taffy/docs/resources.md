# Taffy Resource CFC Methods

Resources extend `taffy.core.resource`. These methods are available inside resource CFCs.

## Response Methods

### rep() / representationOf()

Return data to consumer. `rep()` is alias for `representationOf()`.

```js
function get() {
    return rep({ name: "John", age: 30 });
}
```

### noData()

Return response with no body. Defaults to 200 status, Content-Type `application/json`.

```js
return noData().withStatus(404, "Not Found");
```

### noContent()

Return 204 with no body and Content-Type `text/plain`. Better than `noData()` for truly empty responses.

```js
return noContent();
```

### withStatus(statusCode, statusText)

Chain after `rep()` or `noData()` to set HTTP status.

```js
return rep({ error: "Invalid input" }).withStatus(400, "Bad Request");
```

### withHeaders(headerStruct)

Add custom response headers.

```js
return rep(data).withHeaders({ "X-Custom-Header": "value" });
```

## Query Helpers

### qToArray(query, callback?)

Convert query to array of structs. Preserves column name case.

```js
return rep(qToArray(myQuery));
```

With callback for transformation:

```js
return rep(qToArray(myQuery, function(row) {
    row.startDate = dateFormat(row.startDate, "yyyy-mm-dd");
    return row;
}));
```

### qToStruct(query, callback?)

Convert single-row query to struct. Uses first row only.

```js
return rep(qToStruct(myQuery));
```

Callback receives column name and value:

```js
return rep(qToStruct(myQuery, function(colName, val) {
    if (colName == "startDate") {
        return dateFormat(val, "yyyy-mm-dd");
    }
    return val;
}));
```

## Streaming Methods

### streamBinary(binaryData)

Stream binary data. Must use `withMime()`.

```js
return streamBinary(pdfBinary).withStatus(200).withMime("application/pdf");
```

### streamFile(filePath)

Stream file from disk. Chain `.andDelete(true)` to delete after streaming.

```js
return streamFile("/path/to/file.pdf").withMime("application/pdf");
```

### streamImage(base64Data)

Stream base64-encoded image.

```js
return streamImage(imageData);
```

### withMime(mimeType)

Required when using stream methods to set Content-Type.

## Utility Methods

### encode.string(input)

Ensure numeric-looking strings stay strings in JSON output (e.g., phone numbers, zip codes).

```js
row.phone = encode.string(row.phone);
```

### saveLog(exception)

Log data using configured logging adapter.

```js
saveLog(cfcatch);
```

### addDebugData(data)

Save data for exception log adapters when errors occur.

```js
addDebugData({ userId: arguments.userId, action: "delete" });
```
