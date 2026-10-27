# Deployment Ownership

The names and escalation contacts below are required fields. They are intentionally not invented from repository metadata.

| Role | Accountable responsibility | Named owner / contact | Status |
|---|---|---|---|
| Release Owner | Go/no-go decision, change record, release communications | **Unassigned: Release Manager must fill in** | Blocked |
| Application Owner | Fix application/configuration issues and validate dependency behavior | **Unassigned: Checkout API team must fill in** | Blocked |
| SRE / Operational Lead | Cluster execution, observability, rollback, and incident coordination | **Unassigned: SRE team must fill in** | Blocked |
| On-Call Support | Monitor the agreed deploy window and acknowledge alerts | **Unassigned: on-call rota must fill in** | Blocked |
| Security approver | Review vulnerability findings and any exception | **Unassigned: Security team must fill in** | Blocked |
| Escalation path | Primary to secondary to incident commander, with reachable channels | **Unassigned: service owner must fill in** | Blocked |

## Minimum handoff record

Before a Go decision, record the deployment window and timezone, change/incident ticket, primary and backup contacts, alert dashboard, communication channel, rollback approver, and maximum time to escalate. No production deployment should be scheduled while these fields are unassigned.
