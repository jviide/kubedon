# Kubedon

A [Mastodon](https://github.com/tootsuite/mastodon) conf for Kubernetes / Google Container Engine.

## Prerequisite: Generating Secrets

### CloudSQL

Enable the Cloud SQL Administration API.

Create a Cloud SQL PostgreSQL instance.

Create the secrets for the proxy as described in [Connecting from Google Container Engine](https://cloud.google.com/sql/docs/postgres/connect-container-engine). Note that we also add `instance_connection_name` to `cloudsql-secrets`. Furthermore take note of the PostgreSQL proxy user credentials, as they will be referred to as `PROXY_USER` and `PROXY_PASSWORD` later.

```sh
$ kubectl create secret generic cloudsql-secrets \
  --from-file=credentials.json=[PROXY_KEY_FILE_PATH] \
  --from-literal=instance_connection_name=[INSTANCE_CONNECTION_NAME]
```

### Mastodon Conf Secrets

```sh
$ kubectl create secret generic mastodon-secrets \
  --from-literal=PAPERCLIP_SECRET=[FIRST_RANDOM_STRING] \
  --from-literal=SECRET_KEY_BASE=[SECOND_RANDOM_STRING] \
  --from-literal=OTP_SECRET=[THIRD_RANDOM_STRING] \
  --from-literal=LOCAL_DOMAIN=[YOUR_DOMAIN] \
  --from-literal=DB_USER=[PROXY_USER] \
  --from-literal=DB_PASS=[PROXY_PASSWORD]
```

### Let's Encrypt Certs

Generate Let's Encrypt Certs.

```sh
$ kubectl create secret generic web-certificates \
  --from-file=fullchain.pem \
  --from-file=privkey.pem
```

## Run Migration

TBD

Somehow run `RAILS_ENV=production bundle exec rails db:migrate` on a container that has been seeded with the same env variables as the `web` container.

## Apply Conf

```sh
$ kubectl apply -f .
```
