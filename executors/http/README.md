# Venom - Executor HTTP

Step for execute a HTTP Request

## Input
In your yaml file, you can use:

```yaml
  - method optional, default value: GET
  - url mandatory
  - unix_sock optional
  - path optional
  - body optional
  - bodyFile optional
  - headers optional
  - proxy optional: set to use a proxy server for connection to url
  - ignore_verify_ssl optional: set to true if you use a self-signed SSL on remote for example
  - basic_auth_user optional: username to use for HTTP basic authentification
  - basic_auth_password optional: password to use for HTTP basic authentification
  - no_follow_redirect optional: indicates that you don't want to follow Location if server returns a Redirect (301/302/...)
  - skip_body: skip the body and bodyjson result
  - skip_headers: skip the headers result

```

```yaml

name: Title of TestSuite
testcases:

- name: GET http testcase
  steps:
  - type: http
    method: GET
    url: https://eu.api.ovh.com/1.0/
    assertions:
    - result.body ShouldContainSubstring /dedicated/server
    - result.body ShouldContainSubstring /ipLoadbalancing
    - result.statuscode ShouldEqual 200
    - result.bodyjson.apis.apis0.path ShouldEqual /allDom


- name: POST http with bodyFile
  steps:
  - type: http
    method: POST
    url: https://eu.api.ovh.com/1.0/
    bodyFile: /tmp/myfile.tmp
    assertions:
    - result.statuscode ShouldNotEqual 200


- name: POST http with multipart
  steps:
  - type: http
    method: POST
    url: https://eu.api.ovh.com/1.0/
    multipart_form:
        file: '@/tmp/myfile.tmp'
    assertions:
    - result.statuscode ShouldNotEqual 200

- name: GET API health over Unix Socket
  steps:
  - type: http
    method: GET
    unix_sock: /run/myapp/health.sock
    url: http://unix/health
    assertions:
    - result.bodyjson.success ShouldBeTrue
```
*NB: to post a file with multipart_form, prefix the path to the file with '@'*

## Output

```
result.executor
result.timeseconds
result.timehuman
result.statuscode
result.body
result.bodyjson
result.headers
result.error
```
- result.timeseconds & result.timehuman: time of execution
- result.executor.executor.method: HTTP method used, example: GET
- result.executor.executor.url: url called
- result.executor.executor.multipartform: multipartform if exists
- result.err: if exists, this field contains error
- result.body: body of HTTP response
- result.bodyjson: body of HTTP response if it's a JSON. You can access json data as result.bodyjson.yourkey for example.
- result.headers: headers of HTTP response
- result.statuscode: Status Code of HTTP response

### JSON keys

JSON keys are lowercased automatically (eg. use `result.bodyjson.yourkey`, not
`result.bodyjson.YourKey`).

On top of that, if a JSON key contains special characters, they will be translate to underscores.

### JSON arrays

When a HTTP response contains a JSON array, you have to use following syntax
to access specific key of an array: `result.bodyjson.array_name.array_name_index_in_array.key`

Example if you want to get value of `path` key of *second* element in `apis` array: `result.bodyjson.apis.apis1.path`


## Default assertion

```yaml
result.statuscode ShouldEqual 200
```
