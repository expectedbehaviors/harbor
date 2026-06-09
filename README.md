# Harbor Helm Chart

Baseline Helm chart for [Harbor](https://goharbor.io) container registry. Wraps the [upstream Harbor Helm chart](https://github.com/goharbor/harbor-helm) with **External Secrets** for database and Docker Hub credentials.

## Subcharts

| Subchart | Source | Values prefix | Description |
|----------|--------|---------------|-------------|
| **harbor** | [goharbor/harbor-helm](https://github.com/goharbor/harbor-helm) | `harbor.*` | Harbor core, registry, Trivy scanner, ingress. |

## Templates

| Template | Purpose |
|----------|---------|
| `externalsecrets.yaml` | `harbor-db` and `dockerhub` secrets from your ClusterSecretStore |

## External database and Redis

Harbor uses **external PostgreSQL** and **external Redis** (not bundled subcharts):

| Setting | Baseline placeholder | Override in your values |
|---------|---------------------|-------------------------|
| PostgreSQL host | `postgresql.example.svc.cluster.local` | Your DB service FQDN |
| Database name | `harbor` | Create DB on your Postgres server |
| Credentials | Secret `harbor-db` from ESO | Set `externalSecrets.db.itemTitle` |
| Redis host | `redis.example.svc.cluster.local` | Your Redis service FQDN |
| Redis auth | `existingSecret: ""` (no auth) | Set `harbor.redis.external.existingSecret` when Redis requires a password |

**Greenfield:** public defaults create a registry PVC (`30Gi`, cluster default StorageClass). **Existing PVC:** override with `harbor.persistence.persistentVolumeClaim.registry.existingClaim` in your private values.

## Prerequisites

- Kubernetes `>=1.21`
- External PostgreSQL with database `harbor`
- External Redis (optional auth)
- External Secrets Operator + ClusterSecretStore
- Ingress controller and TLS (when expose type is ingress)

## Install

```bash
helm dependency update .
helm template release . -f values.yaml
```

## Support this project

I build tools to get the best homelab experience I can from what's available and to grow as a programmer along the way. If you'd like to contribute, donations go toward homelab operating costs and subscriptions that keep this tooling maintained. Optional and appreciated.

[![Donate with PayPal](https://www.paypalobjects.com/en_US/i/btn/btn_donate_LG.gif)](https://www.paypal.com/donate/?business=9RHVW92WMWQNL&no_recurring=0&item_name=Optional+donations+help+support+Expected+Behaviors%E2%80%99+open+source+work.+Thank+you.&currency_code=USD)
