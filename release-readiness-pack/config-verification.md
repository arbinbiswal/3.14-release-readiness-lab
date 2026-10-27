# Configuration Verification

## Sources reviewed

- `app/app.py`
- `k8s/configmap.yaml`
- `k8s/deployment.yaml`
- `docs/architecture.md`

## Configuration matrix

| Variable | Application behavior | Candidate manifest | Production status |
|---|---|---|---|
| `PORT` | Defaults to `5000` | ConfigMap sets `5000` | Verified for the declared container port. |
| `LOG_LEVEL` | Defaults to `INFO` and is upper-cased | ConfigMap sets `info` | Verified. |
| `DATABASE_URL` | If absent, reports `warning_fallback_sqlite` and uses volatile fallback behavior | Missing from ConfigMap | **Blocker: must be injected from a production Secret or external secret manager.** |
| `REDIS_URL` | If absent, reports `warning_fallback_local` and uses non-distributed local cache behavior | Missing from ConfigMap | **Blocker: must be injected from a production Secret or external secret manager.** |

## Verification findings

1. `deployment.yaml` imports only `checkout-api-config` via `envFrom`; there is no Secret reference.
2. `configmap.yaml` explicitly notes that `DATABASE_URL` and `REDIS_URL` were omitted.
3. `/health` always returns HTTP 200, even when either dependency is in fallback mode. A rollout could therefore pass Kubernetes readiness while not using production services.
4. No credential values were committed, correctly avoiding secret leakage. The target namespace, Secret name, endpoint values, and rotation owner still require confirmation from the platform owner.

## Required pre-Go evidence

- Platform owner confirms the production PostgreSQL and Redis endpoints.
- Secret is provisioned out-of-band and referenced by the Deployment without plaintext values in Git.
- A deployed pod is inspected with `kubectl exec` or an equivalent approved check to confirm both variables are present without printing their values.
- `/health` returns connected status for both dependencies, and the probe semantics are reviewed so fallback mode cannot be considered healthy.
