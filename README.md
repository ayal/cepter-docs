# Cepter Docs

## Extension link:
https://chrome.google.com/webstore/detail/cepter/jkofigpfiofpdeghmlimifoooaipekbh?hl=en

## Basic Usage 

https://www.youtube.com/watch?v=UMzv02VMOxg&ab_channel=cepterinter

## Reponse JSON fields:

|               | Replace                             | Merge                                       |
|---------------|-------------------------------------|---------------------------------------------|
| **body**      | A response body. Can be either an object (for XHR JSON responses) or a string (For other response types). If absent - empty body will be used. | A response body. Can be either an object (for XHR JSON responses) or a string (For other response types). If absent - Original response body will be used. |
| **responseHeaders** | If given, will override original response-headers. If missing, original response headers will be missing when choosing "Replace" interception type. | If given, will be merged to original response headers. If missing, original response headers will be included when choosing "Merge" interception type. |
| **status**    | If absent - default is 200           | If absent - default is 200                   |



### Changing a Request
- choose "Change" as interception rule type
- you can either change the request url entirely or use `<URL>` as a placeholder for original url
- requestHeaders will be **merged** into original request headers
- requestPostData will **replace** the original request post-data
- body / status / responseHeader fields are irrelevant when choosing "Change" interception type

### Interception rule examples:

#### Replacing / Merging body JSON
```json
{
  "body": {
    "key": "value"
  }
}
```

#### Replacing / Merging response headers JSON:
```json
{
  "body": {},
  "responseHeaders": [
    {
      "name": "headerName",
      "value": "headerValue"
    }
  ],
}
```

#### example for changing a request ("Change" type):
```json
{
  "requestUrl": "<URL>?newParam=newValue",
  "requestHeaders": [
    {
      "name": "headerName",
      "value": "headerValue"
    }
  ],
  "requestMethod": "GET",
  "requestPostData": {
    "newKey": "newValue"
  }
}
```

#### example for a "redirect" response:
```json
{
  "responseHeaders": [
    {
      "name": "Location",
      "value": "http://localhost:3300/handler"
    }
  ],
  "status": 307
}
```
You can have a local server running at `localhost:3300` with more advanced handling of request / response

# Known issues:
- Closing / Disconnecting / Detaching one tab disconnects all tabs https://bugs.chromium.org/p/chromium/issues/detail?id=1170381
- when intercepting requests made to a different domain and changing their response, you might need to include a CORS headers (allow-origin / allow-headers etc...) in your response to make the intercepted response work:
```json
{
  "responseHeaders": [
    {
      "name": "Access-Control-Allow-Origin",
      "value": "*"
    }
  ]
}
```
you might also need to handle the OPTIONS preflight request and return the CORS headers there too

# Troubleshooting:
- Check your interception rule:
  - is your URL pattern Regex accurate / specific enough? make sure it matches your url exactly and not matching unwanted requests.
  - do you need original body / headers? - better choose "Merge" interception type.
  - is your interception method correct?
 - Do you have any other extension that might be interfering with Cepter ("Edit this Cookie" extension used to cause some trouble)

# Future features:
- toggle specific interception rules
- export / import interceptions
- delay responses by time
