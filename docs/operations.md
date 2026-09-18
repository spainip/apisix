# API gateway operations

The Fleet bundle owns the APISIX Helm release, the mock API, the edge Ingress objects, the rate-limit bootstrap, and the monitoring objects.

## Public endpoints

- `https://api.spainip.es/v1/hello` — public route, limited per client IP.
- `https://api.spainip.es/docs` — Swagger UI for the mock API.
- `https://api.spainip.es/v1/items` — API-key protected route.
- `https://api.spainip.es/v1/echo` — API-key protected route.
- `https://api-admin.spainip.es/ui/` — embedded APISIX Dashboard, protected at the Traefik edge.

## Key and consumer management

APISIX Admin API and Dashboard manage consumers, credentials, consumer groups and plugins. The Fleet bootstrap creates a non-production demo consumer and two example groups:

- `basic`: 300 requests/minute per authenticated consumer.
- `premium`: 3000 requests/minute per authenticated consumer.

The demo key is generated outside Git and stored in the cluster Secret `api-gateway-demo-key`. Rotate it in the Dashboard before using this installation for real clients.

The public route is limited to 60 requests/minute per client IP. The authenticated route applies the consumer group limit and a route-level 300 requests/minute limit.

## etcd backup and restore

The APISIX chart installs one persistent etcd replica for this single-node k3s cluster. Before a configuration migration, take a snapshot using an ephemeral etcdctl pod and copy the snapshot to protected backup storage. Restore must be rehearsed on a separate temporary etcd instance before using it for production recovery; do not restore over the live data directory.

## IAM limitation

The embedded APISIX Dashboard provides gateway administration; it is not an end-user registration or full IAM portal. If end-user self-registration, MFA, social login, or federation is needed, put Keycloak or another OIDC provider in front of the protected API and use APISIX JWT/OIDC integration.
