# Cepter Docs

## Basic Usage 

https://www.youtube.com/watch?v=UMzv02VMOxg&ab_channel=cepterinter

### Replacing a Response body  (or intercepting at Request-Stage)
- choose "Replace" as interception rule type
#### Basic response JSON:
```json
{
  "body": {
    "key": "value"
  },
  "status": 200
}
```
### Replacing response headers:
#### example JSON:
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
response JSON fields for "Replace" interception type:
- body: A response body. Can be either an object (for XHR JSON responses) or a string (For other response types), If absent - **empty body** will be used (if "Replace" interception type is chosen)
- responseHeaders:
  -  if given, will override original response-headers
  - if missing, original response headers will be **missing** when choosing "Replace" interception type
- status: if missing - default is 200, will override status if present

### Merging Response body (or intercepting at Response-Stage)
- choose "Merge" as interception rule type
- if is request is of XHR type and body is of JSON type - response body will be merged into the original body using `_.merge()` function (`lodash`) 

response JSON fields:
- body: A response body. Can be either an object (for XHR JSON responses) or a string (For other response types), If absent - **Original response body** will be used (if "Merge" interception type is chosen)
- responseHeaders:
  - if given, will be **merged** to original response headers
  - if missing, original response headers will be included when choosing "Merge" interception type
- status: if missing - default is 200, will override status if present

### Changing a Request

- choose "Change" as interception rule type
- example JSON for changing the request:
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
- you can either change the request url entirely or use `<URL>` as a placeholder for original url
- requestHeaders will be **merged** into original request headers
- requestPostData will **replace** the original request post-data
- body / status / responseHeader fields are irrelevant when choosing "Change" interception type


# Known issues:
- when intercepting requests to different domain and changing response, you might need to include a CORS header to make the intercepted response work:
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

# Troubleshooting:
- Check your interception rule:
 -- is your regex accurate / specific enough (make sure it matches your url exactly, not less and not more)
 -- is your method correct
 - Do you have any other extension that might be interfering with Cepter (Edit this Cookie extension, for example)
