# cfr-python-to-sql

Cloud Functions or Run with Python to connect to Cloud SQL PostgreSQL

## GCP Resources

- One project
- One VPC
- One serverless VPC connector
- One SQL instance with PostgreSQL 15
- One GCE instance
  - spot
  - shuts down after a maximum of four hours runtime
- Artifact Registry
- GCS bucket
- Cloud Functions
- Cloud Run (not yet in-use)

## Log Book

### SQL Instance Details

instanceName sql-instance

connName 

DBname postgres

Tablename hello

IP 

Port 5432

Username postgres

Password 

### SQL Instance Notes
To connect, add authorized networks 0.0.0.0/0 to the Cloud SQL instance.

Alternatively, you use the Cloud SQL Auth Proxy.

## Cloud Functions - hello world
Authorization - required

Ingress - From VPC only (won't work from your laptop etc.)

The Hello world function can be triggered from the GCE instance

```shell
curl -m 70 -X POST https://northamerica-northeast1... \
-H "Authorization: bearer $(gcloud auth print-identity-token)" \
-H "Content-Type: application/json" \
-d '{
  "name": "Hello World"
}'
```

You get a 404, if you try to trigger it from elsewhere (outside the VPC, like your laptop).

You get a 404, if you don't have the run.invoker role on your account.

```shell
speedy@DESKTOP-1O1OVAU MINGW64 ~/mypython/python-docs-samples/cloud-sql/postgres/sqlalchemy (main)
$ curl -m 70 -X POST https://northamerica-northeast1... -H "Authorization: bearer $(gcloud auth print-identity-token)" -H "Content-Type: application/json" -d '{
  "name": "Hello World"
}'
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   299  100   272  100    27   1007    100 --:--:-- --:--:-- --:--:--  1111
<html><head>
<meta http-equiv="content-type" content="text/html;charset=utf-8">
<title>404 Page not found</title>
</head>
<body text=#000000 bgcolor=#ffffff>
<h1>Error: Page not found</h1>
<h2>The requested URL was not found on this server.</h2>
<h2></h2>
</body></html>
```

## Cloud Functions - Quickstart tutorial

Tutorial URL: https://cloud.google.com/sql/docs/postgres/connect-instance-cloud-functions

Deploy command below:

```shell
gcloud functions deploy quickstart-function \
--gen2 \
--runtime python310 \
--trigger-http \
--entry-point votes \
--region northamerica-northeast1 \
--vpc-connector=serverless-vpc-connector \
--ingress-settings=internal-only \
--set-env-vars INSTANCE_HOST= \
--set-env-vars DB_USER=postgres \
--set-env-vars DB_PASS='' \
--set-env-vars DB_NAME=postgres \
--set-env-vars DB_PORT=5432
```

The curl with POST doesn't work.

```shell
# POST doesn't work
curl -m 70 -X POST https://northamerica-northeast1... -H "Authorization: bearer $(gcloud auth print-identity-token)"
```

Response you will get:

```html
<!doctype html>
<html lang=en>
<title>400 Bad Request</title>
<h1>Bad Request</h1>
<p>The browser (or proxy) sent a request that this server could not understand.</p>
```

This works.

```shell
# works :-)
curl -m 70 -X GET https://northamerica-northeast1-gke-rbac-groups-2022-01-25.cloudfunctions.net/quickstart-function -H "Authorization: bearer $(gcloud auth print-identity-token)"
```

Snippet of curl response from Google's Python quickstart app:

```html
<html lang="en">
<head>
    <title>Tabs VS Spaces</title>
    <link rel="stylesheet"
          href="https://cdnjs.cloudflare.com/ajax/libs/materialize/1.0.0/css/materialize.min.css">
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/materialize/1.0.0/js/materialize.min.js"></script>
</head>
<body>
[...]
```

## Next steps

The Google quickstart app is an interactive app where the user has to click a button to
trigger a SQL statement.
A simpler app is needed to run a SQL statement without user interaction.
