# Kubedon

A [Mastodon](https://github.com/tootsuite/mastodon) conf for Kubernetes / Google Container Engine.

## Prerequisite: Generating Secrets

### CloudSQL

Enable the Cloud SQL Administration API.

Create a Cloud SQL PostgreSQL instance.

Add the secrets for the proxy as described in [Connecting from Google Container Engine](https://cloud.google.com/sql/docs/postgres/connect-container-engine). Note that we deviate from the documentation a bit by adding `instance_connection_name` to `cloudsql-db-credentials`.

```sh
kubectl create secret generic cloudsql-instance-credentials --from-file=credentials.json=[PROXY_KEY_FILE_PATH]
```

```sh
kubectl create secret generic cloudsql-db-credentials --from-literal=username=[PROXY_USER] --from-literal=password=[PROXY_PASSWORD --from-literal=instance_connection_name=[INSTANCE_CONNECTION_NAME]
```

### Mastodon Conf Secrets

```sh
$ kubectl create secret generic mastodon-secrets --from-literal=PAPERCLIP_SECRET=[FIRST_RANDOM_STRING] --from-literal=SECRET_KEY_BASE=[SECOND_RANDOM_STRING]--from-literal=OTP_SECRET=[THIRD_RANDOM_STRING]--from-literal=LOCAL_DOMAIN=[YOUR_DOMAIN]
```

### Let's Encrypt Certs

Generate Let's Encrypt Certs.

```sh
$ kubectl create secret generic web-certificates --from-file=fullchain.pem=fullchain.pem --from-file=privkey.pem=privkey.pem --from-file=cert.pem=cert.pem --from-file=chain.pem=chain.pem
```

## Run migration

TBD

Somehow run `RAILS_ENV=production bundle exec rails db:migrate` on a container that has been seeded with the same env variables as the `web` container.

## Apply Conf

```sh
$ kubectl apply -f .
```
