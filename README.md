# Cepter Docs

## Extension link:
https://chrome.google.com/webstore/detail/cepter/jkofigpfiofpdeghmlimifoooaipekbh?hl=en

## Basic Usage 

https://www.youtube.com/watch?v=UMzv02VMOxg&ab_channel=cepterinter

## Reponse JSON fields:

1. **body**:
   - A response body. Can be either an object (for XHR JSON responses) or a string (For other response types).
   - If present, it will replace the original response body.
   - If absent, an empty body will be used.

2. **responseHeaders**:
   - If given, it will override the original response headers.
   - If missing, the original response headers will be missing when choosing the "Replace" interception type.

3. **status**:
   - If absent, the default status is 200.

Note the different behavior:

|               | Replace Interception Type                            | Merge Interception Type                                                 |
|---------------|----------------------------------------------------|------------------------------------------------------------|
| **body**      | Will replace the original response body if present, empty body if absent. | Will be **merged** into the original response using lodash _.merge() if present, the original response body will be used if absent. |
| **responseHeaders** | Will override the original response headers if given, the original response headers will be missing if absent. | Will be merged into the original response headers if given, the original response headers will be included if missing. |



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
Notes:
- changes in response headers might not reflect in network tab, to make sure it worked - test with console / your server / postman
- as mentioned response headers are merged with original response-header, and also filtered to be unique.

### Changing a Request
- choose "Change" as interception rule type
- you can either change the request url entirely or use `<URL>` as a placeholder for original url
- requestHeaders will be **merged** into original request headers
- requestPostData will **replace** the original request post-data
- body / status / responseHeader fields are irrelevant when choosing "Change" interception type

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
Note: changes in request-url might not reflect in network tab, to make sure it worked - test with console / your server / postman

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

Here's a simple example local server: https://gist.github.com/ayal/9b577d0459b709594f8c387c735a676c

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
 - Do you have any other extension that might be interfering with Cepter?
    - Cepter has known major problems with "LastPass" and other password-managers, and minor problems with "Edit this cookie" extension.

## Handling Intercepting iFrame Requests

If you're intercepting iFrame requests, you might consider using the [chrome://flags#site-isolation-trial-opt-out](chrome://flags#site-isolation-trial-opt-out)
 flag.

**Note**: If you choose to use the `#site-isolation-trial-opt-out` flag, remember to turn it back on after testing if necessary.

# Future features:
- toggle specific interception rules
- export / import interceptions
- delay responses by time
