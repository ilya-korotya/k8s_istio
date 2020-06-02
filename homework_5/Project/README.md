# Security in Istio 

Here I added `Auth0` authorization and added mTLS to services. Also, I updated these deployments to configure health checks which can work with mTLS.

Steps to reproduce:

1. Try making a request without an auth header: 
    ```bash
    curl http://10.98.86.25/frontend-catalog/api/v1/dashboard
    ```
   
   ```bash
   > GET /frontend-catalog/api/v1/dashboard HTTP/1.1
   > Host: 10.98.86.25
   > User-Agent: curl/7.54.0
   > Accept: */*
   >
   < HTTP/1.1 401 Unauthorized # We got 401. Because we didn't sent an auth header
   < content-length: 29
   < content-type: text/plain
   < date: Sun, 31 May 2020 15:35:26 GMT
   < server: istio-envoy
   < x-envoy-upstream-service-time: 0
   <
   * Connection #0 to host 10.98.86.25 left intact
   ```
   
 2. Try making a request with an auth header:
 
    ```bash
    curl -H 'Authorization: Bearer eyJhbGciOiJSUz...' http://10.98.86.25/frontend-catalog/api/v1/dashboard -v
    ```
    
    ```bash
    ...
    < HTTP/1.1 200 OK
    < date: Sun, 31 May 2020 15:37:06 GMT
    < content-length: 607
    < content-type: text/plain; charset=utf-8
    < x-envoy-upstream-service-time: 16
    < server: istio-envoy
    <
    {
        "authors": [
            ...
        ],
        ...
    }
    ```