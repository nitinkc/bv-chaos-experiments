# BitVelocity Chaos Engineering

This module provides chaos experiments and game day scenarios for testing system resilience and reliability.

## Structure

```
bv-chaos-experiments/
├── experiments/           # Chaos Mesh experiment definitions
├── game-days/            # Game day runbooks and scenarios
├── chaos-results/        # Experiment results and learnings
└── scripts/              # Helper scripts for running experiments
```

## Goals

- Validate resilience patterns (circuit breakers, retry policies, bulkheads)
- Practice incident response and debugging under failure
- Build confidence in system reliability
- Identify single points of failure
- Improve observability and alerting

## Prerequisites

1. Kubernetes cluster (kind, minikube, or cloud)
2. Chaos Mesh installed:
   ```bash
   curl -sSL https://mirrors.chaos-mesh.org/v2.6.0/install.sh | bash
   ```
3. Observability stack running (Prometheus, Grafana, Jaeger)

## Quick Start

### Run a Simple Experiment

```bash
# Apply pod kill experiment
kubectl apply -f experiments/pod-failure-order-service.yaml

# Watch the experiment
kubectl get podchaos -w

# Check service behavior
kubectl logs -f deployment/order-service

# Pause experiment
kubectl annotate podchaos order-service-pod-kill experiment.chaos-mesh.org/pause=true

# Delete experiment
kubectl delete -f experiments/pod-failure-order-service.yaml
```

## Experiment Categories

### 1. Pod Failures
- Random pod kills
- Container restarts
- Pod deletion during traffic

### 2. Network Chaos
- Network latency injection
- Network packet loss
- Network partitioning
- DNS failures

### 3. Resource Stress
- CPU stress
- Memory stress
- I/O stress

### 4. Time Chaos
- Clock skew
- Time offset

### 5. Application-Level Chaos
- JVM crashes
- Database connection failures
- Kafka broker failures

## Safety Guidelines

⚠️ **Important Safety Rules:**

1. **Always start with low blast radius** - Test single pods before clusters
2. **Run in non-production environments first**
3. **Have rollback plan ready** - Know how to pause/delete experiments
4. **Monitor actively** - Watch metrics/logs during experiments
5. **Document learnings** - Record findings in `chaos-results/`
6. **Communicate** - Notify team before running game days

## Game Days

Scheduled chaos engineering exercises with team participation.

See `game-days/README.md` for runbooks and schedules.

### Example Game Day Scenario: Order Service Failure

1. **Hypothesis**: System should handle order service pod failures with < 5% error rate
2. **Blast Radius**: Single order-service pod in dev environment
3. **Duration**: 5 minutes
4. **Success Criteria**: 
   - No cascading failures
   - Alerts fire within 1 minute
   - Circuit breakers activate
   - p95 latency < 500ms (degraded but acceptable)

## Integration with CI/CD

Chaos experiments can run automatically in staging:
- Nightly chaos testing
- Pre-release resilience validation
- Regression testing for resilience patterns

> **Note**: Automated chaos workflows will be added in `.github/workflows/` as the platform matures.

## Learning Resources

- [Chaos Mesh Documentation](https://chaos-mesh.org/docs/)
- [Principles of Chaos Engineering](https://principlesofchaos.org/)
- [Game Day Runbook Example](game-days/runbook-inventory-failure.md)
