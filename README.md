# Radicale CalDAV/CardDAV (Kubernetes Edition)

This repository contains some simple Kubernetes resources for spinning up a Radicale CalDAV/CardDAV server. Depends on https://github.com/tomsquest/docker-radicale.

## Prerequisites

First we need to setup the authentication file for the users of radicale. The authentication in radicale itself is disabled. Instead we use the ingress controller for HTTP basic authentication. The ingress controller accepts a secret containing htpasswd-style data. This is what we create now (replace `my-username` with your username):

```
$ htpasswd -c auth my-username
New password: 
Re-type new password: 
Adding password for user my-username
```

This creates the file `auth` which we need to convert into base64 now:

```
$ cat auth | base64
bXktdXNlcm5hbWU6JGFwcjEkTXdMd09IMk4kZW1rci94MDhITU93UHZOZVI3YUd5MAo=
```

Now replace `[base64]` in the secret `02-secret-basic-auth.yaml` with the base64 string:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: basic-auth
  namespace: radicale
type: Opaque
data:
  auth: bXktdXNlcm5hbWU6JGFwcjEkTXdMd09IMk4kZW1rci94MDhITU93UHZOZVI3YUd5MAo=
```

## Deploy

First ensure the steps described above are fulfilled. Then apply the resources:

```
$ kubectl apply -f .
namespace/radicale created
configmap/config created
secret/basic-auth created
persistentvolumeclaim/radicale-collections-pvc created
deployment.apps/radicale created
service/radicale created
ingress.extensions/radicale created
```

## Interesting resources

The following resources helped building this repository:

- [Radicale configuration options](https://radicale.org/configuration/)
- [Radicale with reverse proxy](https://radicale.org/proxy/)
- [DAVx5 setup with Radicale](https://www.davx5.com/tested-with/radicale)
- [nginx-ingress authentication](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#authentication)
- [nginx-ingress authentication example](https://kubernetes.github.io/ingress-nginx/examples/auth/basic/)
- [nginx-ingress configuration-snippet](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#configuration-snippet)

## License

MIT
