# Risk Analysis

| Risk | Severity | Likelihood | Mitigation / release gate | Owner |
|---|---|---|---|---|
| `DATABASE_URL` is absent and the app falls back to volatile SQLite behavior | High | High | Provision a production Secret reference, verify connectivity in a deployed pod, and make fallback non-healthy before release | Application Owner |
| `REDIS_URL` is absent and each pod uses a local, non-distributed cache | High | High | Provision Redis Secret/configuration and verify connected health on all replicas | Application Owner |
| pip-audit reports known vulnerabilities in Flask 3.0.2 and pytest 8.0.2 | High | High | Upgrade to fixed versions or obtain a time-bound security exception; rerun audit with zero unapproved findings | Application Owner / Security |
| Container image is mutable tag `kalvium/checkout-api:v2.4`, not an immutable digest | High | Medium | Build, scan, sign, and deploy a registry digest; record the digest in the release evidence | Release Owner |
| Docker build and Kubernetes apply were not executable in the audit environment | Medium | High | Run CI and a staging deployment with archived logs before approval | SRE/Operational Lead |
| Rollback has not been rehearsed against a cluster and v2.3 image availability is unconfirmed | High | Medium | Confirm registry artifact, rehearse rollback in staging, and capture rollout/health output | SRE/Operational Lead |
| Health probe returns HTTP 200 during dependency fallback | High | High | Change probe/readiness semantics or add a separate dependency-aware readiness check; verify rollout behavior | Application Owner |
| No named release-window owner, on-call, or escalation contact exists in the repository | Medium | Medium | Complete `ownership.md` with accountable people and reachable escalation contacts before scheduling | Release Manager |
| Deployment has no PodDisruptionBudget or autoscaling policy | Medium | Medium | Review availability requirements for three replicas and add/approve operational controls before production | SRE/Operational Lead |

## Risk decision

The highest open risks are High severity with High likelihood. They are release-blocking and drive the No-Go decision in `readiness-report.md`.
