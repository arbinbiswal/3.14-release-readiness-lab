# Rollback Plan

## Known-good target

`v2.3` is the documented production-stable release in `docs/architecture.md` (release history row dated 2026-06-01). Before execution, confirm that `kalvium/checkout-api:v2.3` exists in the registry and that the deployment revision or manifest for v2.3 is available.

## Trigger conditions

Initiate rollback for failed smoke tests, elevated 5xx/latency, dependency connection failures, or failed readiness after the agreed observation window. The Release Owner may initiate; the SRE/Operational Lead owns execution.

## Exact rollback commands

```bash
# Stop if the target image cannot be resolved.
kubectl -n checkout-system set image deployment/checkout-api \
  checkout-api=kalvium/checkout-api:v2.3
kubectl -n checkout-system annotate deployment/checkout-api \
  readiness.rollback/reason="v2.4 production rollback" --overwrite
kubectl -n checkout-system rollout status deployment/checkout-api --timeout=5m
```

If the v2.4 rollout created a usable prior ReplicaSet, the alternative is:

```bash
kubectl -n checkout-system rollout undo deployment/checkout-api
kubectl -n checkout-system rollout status deployment/checkout-api --timeout=5m
```

Do not use the second form without first confirming that the previous revision is v2.3.

## Verification after rollback

```bash
kubectl -n checkout-system get deployment checkout-api -o wide
kubectl -n checkout-system get pods -l app=checkout-api
kubectl -n checkout-system get events --sort-by=.lastTimestamp
kubectl -n checkout-system port-forward svc/checkout-api-service 8080:80
curl --fail http://127.0.0.1:8080/
curl --fail http://127.0.0.1:8080/health
```

Confirm the root response reports `v2.3`, all pods are Ready, error/latency metrics have returned to baseline, and database/Redis checks are connected. Record the incident, command output, start/end times, and approver.

## Test status

The rollback command sequence was reviewed against the Deployment and stable-version documentation and is syntactically valid shell. It was not executed against a cluster because no Kubernetes API server was available during this audit. A staging rollback rehearsal is a pre-Go gate.
