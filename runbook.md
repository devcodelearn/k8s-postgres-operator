# 📘 Runbook: Kubernetes Data Platform Operations

## Overview
This runbook provides operational guidance for managing and troubleshooting data services running on Kubernetes, including PostgreSQL, MongoDB, and Kafka.

It is intended for platform engineers and on-call responders supporting production systems.

---

## Incident Severity Levels

| Severity | Description                          | Example                          | Response Time |
|----------|--------------------------------------|----------------------------------|---------------|
| SEV1     | Critical system outage               | Kafka cluster unavailable        | Immediate     |
| SEV2     | Partial degradation                  | High latency, partial failures   | < 30 min      |
| SEV3     | Minor issue                          | Single pod restart               | < 2 hours     |

---

## Common Alerts & Troubleshooting

### 1. High CPU / Memory Usage

**Alert:**
container_cpu_usage_seconds_total > threshold

**Steps:**
1. Identify affected pod:
   kubectl top pods -n <namespace>
2. Check logs:
   kubectl logs <pod>
3. Verify resource limits:
   kubectl describe pod <pod>
## Action:
Scale deployment if needed
Adjust resource requests/limits
Investigate inefficient queries or workloads

## 2. ⚠️ Pod CrashLoopBackOff
**Steps:**
kubectl get pods
kubectl describe pod <pod>
kubectl logs <pod> --previous

## Common Causes:
Misconfiguration (env variables, secrets)
Failed DB connections
Resource limits exceeded

## Action:
Fix configuration
Restart pod after fix:
kubectl delete pod <pod>

## 3. 🐘 PostgreSQL Failover Issue
1. Check cluster status:
kubectl get postgresql
2. Check primary pod:
kubectl describe pod <postgres-primary>
## Action:
Verify replica health
Restart failed replica
Ensure PVCs are attached correctly

## 4. 📡 Kafka Consumer Lag
Alert:
High lag detected in Prometheus
**Steps:**
Check consumer group lag in Grafana
Verify consumer pod health:
kubectl get pods -l app=consumer
## Action:
Restart consumer pods
Scale consumers
Check broker health

## 5. 📉 Prometheus Target Down

## Steps:
Check target status in Prometheus UI
Verify service endpoints:
kubectl get svc
kubectl get endpoints
## Action:
Restart exporter pod
Fix service selector mismatch
🔁 Standard Operating Procedures
🔄 Restart Deployment
kubectl rollout restart deployment <name>
## 📈 Scale Deployment
kubectl scale deployment <name> --replicas=3
## 📦 Check Logs
kubectl logs -f <pod>
## 🔐 Verify RBAC Issues
kubectl auth can-i <action> --as=<user>
## 📊 Observability Stack
Metrics: Prometheus
Dashboards: Grafana
Logs: Splunk
Key SLIs
Request success rate
Latency
Error rate
Resource utilization
## 📉 SLO Guidelines
Service	SLO Target
API uptime	99.9%
Kafka lag	< 5 sec
DB latency	< 100 ms
## 🧠 On-Call Checklist
 Acknowledge alert
 Identify severity
 Check dashboards (Grafana)
 Inspect logs (Splunk)
 Mitigate issue
 Document root cause
 Create follow-up ticket
## 📝 Post-Incident Review
After resolving incidents:
Document root cause
Add monitoring/alerts if missing
Improve automation or safeguards
Share learnings with team
## 📚 References
Kubernetes Docs
Prometheus Alerting Guide
Internal Architecture Docs
## 💡 Notes
Always prioritize system stability over quick fixes
Avoid manual changes outside GitOps workflow
Ensure all changes are version-controlled
   
