# Istio Virtual Service

Here I deployed three versions for `authors` services and two versions for `books` services. 
## Authors:
`Authors` deployment has v1, v2, and v3 labels. v1 and v2 use `VirtualService` with a weight destination. For v1 I set 10%, for v2 I set 90%. Also, If you want to send a request to deployment v3 you should use header `x-developer illia-korotia`. For balancing traffic between these nodes uses `RANDOM` balancer.

## Books:
`Books` deployment has v1 and v2 labels. For v1 I set 10%, for v2 I set 90%. For balancing traffic between these nodes uses `ROUND_ROBIN` balancer.

## Traffic percentage:
![traffic_percentage](./screenshots/traffic_percentage.png "traffic_percentage")
