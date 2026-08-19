# The Vault — DevOps Exercises

A production-style Kubernetes setup on AWS EKS, built incrementally as part of a DevOps course.
Three-tier app (frontend + backend + database), fully managed through Helm charts and
orchestrated by Helmfile, with node isolation, monitoring, and autoscaling.

## Architecture

- **EKS cluster** with two node groups:
  - `app-nodes` (`t3.small`, labeled `role=app`) — runs frontend + backend
  - `db-nodes` (`m7i-flex.large`, labeled `role=db`, tainted `workload=database:NoSchedule`) — runs the database only
- **Frontend** and **Backend**: custom Helm charts (Deployment, Service, Ingress, HPA, ServiceMonitor)
- **Database**: a thin wrapper chart around the production-ready Bitnami PostgreSQL chart
- **Helmfile** (`helmfile.yaml`) declares every release, dependency order, and namespace in one file
- **Monitoring**: `kube-prometheus-stack` (Prometheus + Grafana + Alertmanager), with custom
  ServiceMonitors and Grafana dashboards provisioned via ConfigMap
- **Autoscaling**: HPA (CPU/memory-based, with scale-up/down cooldowns) on frontend/backend,
  VPA (recommendation-only mode) on all three services

## Repo layout

\`\`\`
charts/
  frontend/    # Custom Helm chart
  backend/     # Custom Helm chart
  database/    # Wrapper chart around Bitnami postgresql
values/        # Per-release Helmfile value overrides
dashboards/    # Grafana dashboard JSON, provisioned via ConfigMap
helmfile.yaml  # Single source of truth for all releases
\`\`\`

## Progress

- [x] Section 1 — Architecture Overview
- [x] Section 2 — Helm Charts (frontend, backend, database)
- [x] Section 3 — Helmfile setup and orchestration
- [x] Section 4 — Node separation (labels, taints, tolerations, affinity)
- [x] Section 5 — Monitoring (Prometheus + Grafana)
- [x] Section 6 — HPA & VPA autoscaling
- [ ] Section 7 — Database backups to S3
- [ ] Section 8 — ExternalDNS
- [ ] Section 9 — ExternalSecrets
- [ ] Section 10 — Karpenter / Cluster Autoscaler
- [ ] Section 11 — Final integration exercise

## Notes

- Frontend/backend currently use placeholder container images — this course is
  infrastructure-focused, not application-focused.
- Database credentials and Grafana admin credentials are wired via Kubernetes Secrets
  (\`existingSecret\` pattern), never hardcoded in values — real secret creation is
  handled by ExternalSecrets in Section 9.
