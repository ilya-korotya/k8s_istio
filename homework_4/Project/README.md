# Istio Fault Injection
Here I created fault with delay and http error for `authrors` services.

For testing delay fault need to send a request with the header `end-user: jason-delay`. For testing HTTP fault need to send a request with the header `end-user: jason-fault`.

### Steps to reproduce:
1. Testing delay fault:
    ```bash
    $ curl http://authors:8080/api/v1/authors -H "end-user:jason_delay" -v  | python -m json.tool
    ```
    
    ```bash
    > GET /api/v1/authors HTTP/1.1
    > Host: authors:8080
    > User-Agent: curl/7.64.0
    > Accept: */*
    > end-user:jason_delay # Header for testing delay fault
    >
      0     0    0     0    0     0      0      0 --:--:--  0:00:39 --:--:--     0< HTTP/1.1 200 OK
    < content-type: application/json;charset=UTF-8
    < date: Thu, 28 May 2020 12:47:38 GMT
    < x-envoy-upstream-service-time: 7
    < server: envoy
    < transfer-encoding: chunked
    <
    { [189 bytes data]
    100   178    0   178    0     0      4      0 --:--:--  0:00:40 --:--:--    47 # We have a delay of 40 sec
    * Connection #0 to host authors left intact
    [
        {
            "age": 30,
            "firstName": "Loreth Anne",
            "id": 1,
            "lastName": "White"
        },
        {
            "age": 20,
            "firstName": "Lisa",
            "id": 2,
            "lastName": "Regan"
        },
        {
            "age": 55,
            "firstName": "Ty",
            "id": 3,
            "lastName": "Patterson"
        }
    ]
    ```
2. Testing HTTP fault:
    ```bash
    $ curl http://authors:8080/api/v1/authors -H "end-user:jason_fault" -v  | python -m json.tool
    ```
    
    ```bash
    > GET /api/v1/authors HTTP/1.1
    > Host: authors:8080
    > User-Agent: curl/7.64.0
    > Accept: */*
    > end-user:jason_fault # Header for testing HTTP fault
    >
    < HTTP/1.1 500 Internal Server Error # Error from Istio Virtual Service
    < content-length: 18
    < content-type: text/plain
    < date: Thu, 28 May 2020 12:49:56 GMT
    < server: envoy
    <
    { [18 bytes data]
    100    18  100    18    0     0    118      0 --:--:-- --:--:-- --:--:--   120
    * Connection #0 to host authors left intact
    No JSON object could be decoded
    ```
