# Istio Virtual Service

Here I deployed two versions for `authors` services, two versions for `books` services and two versions for `frontend` services. 
## Authors:
`Authors` deployment has v1 and  v2 labels. v1 and v2 version uses `VirtualService` with a weight destination. For v1 I set 10%, for v2 I set 90%. For balancing traffic between these nodes uses `RANDOM` balancer.

## Books:
`Books` deployment has v1 and v2 labels. v1 and v2 version uses `VirtualService` with a weight destination. For v1 I set 10%, for v2 I set 90%. For balancing traffic between these nodes uses `ROUND_ROBIN` balancer.

## Frontend:
`Frontend` deployment has v1 and v2 labels. This deployment uses a match by a header for balancing traffic between v1 and v2.

## Traffic percentage:
![traffic_percentage](./screenshots/traffic_percentage.png "traffic_percentage")

## Responses:
1. Response from `authors:v2` and `books:v2`
```bash
ikorotia$ curl --location --request GET 'http://10.98.86.25/frontend-catalog/api/v1/dashboard' | python -m json.tool
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   606    0   606    0     0  28402      0 --:--:-- --:--:-- --:--:-- 28857
{
    "authors": [
        {
            "age": 30, // new field
            "firstName": "Loreth Anne",
            "id": 1,
            "lastName": "White"
        },
        {
            "age": 20, // new field
            "firstName": "Lisa",
            "id": 2,
            "lastName": "Regan"
        },
        {
            "age": 55, // new field
            "firstName": "Ty",
            "id": 3,
            "lastName": "Patterson"
        }
    ],
    "books": [
        {
            "authorId": 1,
            "id": 1,
            "pages": 326,
            "publishedYear": 1990, // new field
            "title": "Semiosis: A Novel"
...
```
2. Response from `authors:v1` and `books:v2`
```bash
ikorotia$ curl --location --request GET 'http://10.98.86.25/frontend-catalog/api/v1/dashboard' | python -m json.tool
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   606    0   606    0     0  28402      0 --:--:-- --:--:-- --:--:-- 28857
{
    "authors": [
        {
            "firstName": "Loreth Anne",
            "id": 1,
            "lastName": "White"
        },
        {
            "firstName": "Lisa",
            "id": 2,
            "lastName": "Regan"
        },
        {
            "firstName": "Ty",
            "id": 3,
            "lastName": "Patterson"
        }
    ],
    "books": [
        {
            "authorId": 1,
            "id": 1,
            "pages": 326,
            "publishedYear": 1990, // new field
            "title": "Semiosis: A Novel"
        },
...
```
3. Response from `authros:v2` and `books:v1`
```bash
ikorotia$ curl --location --request GET 'http://10.98.86.25/frontend-catalog/api/v1/dashboard' | python -m json.tool
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   606    0   606    0     0  28402      0 --:--:-- --:--:-- --:--:-- 28857
{
    "authors": [
        {
            "age": 30, // new filed
            "firstName": "Loreth Anne",
            "id": 1,
            "lastName": "White"
        },
        {
            "age": 20, // new filed
            "firstName": "Lisa",
            "id": 2,
            "lastName": "Regan"
        },
        {
            "age": 55, // new filed
            "firstName": "Ty",
            "id": 3,
            "lastName": "Patterson"
        }
    ],
    "books": [
        {
            "authorId": 1,
            "id": 1,
            "pages": 326,
            "title": "Semiosis: A Novel"
        },
...
```
4. Response from `frontend:v2`. Match by `x-developer` header:
```bash
ikorotia$ curl 'http://10.98.86.25/frontend-catalog/api/v1/' -H 'x-developer: illia-korotia' | python -m json.tool
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   635  100   635    0     0  67524      0 --:--:-- --:--:-- --:--:-- 70555
{
...
	{
            "authorId": 3,
            "id": 5,
            "pages": 466,
            "publishedYear": 2011,
            "title": "Revenant Gun"
        }
    ],
    "developer": "illia-korotia"
}
```
