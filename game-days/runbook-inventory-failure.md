# Game Day: Inventory Service Failure Scenario

**Date**: TBD  
**Duration**: 90 minutes  
**Environment**: Dev/Staging  
**Participants**: Backend engineers, SRE, on-call rotation  

## Objectives

1. Practice incident response procedures
2. Validate monitoring and alerting effectiveness
3. Test service resilience patterns
4. Identify gaps in observability
5. Build team confidence in handling production incidents

## Scenario Overview

Simulate a realistic inventory service failure during peak traffic:
- Inventory service pods experiencing intermittent crashes
- Network latency to database
- Memory pressure causing cache evictions
- Cascading impact on order creation flow

## Pre-Game Day Checklist

- [ ] Notify all participants (no surprises!)
- [ ] Verify observability stack is running
- [ ] Verify alert routes are configured
- [ ] Set up incident communication channel (Slack/Teams)
- [ ] Prepare rollback scripts
- [ ] Review relevant runbooks
- [ ] Verify backup/snapshot of database

## Timeline

### Phase 1: Baseline (15 minutes)

1. **Start load testing** (t=0)
   ```bash
   cd bv-performance-testing/gatling-tests
   ./gradlew gatlingRun -Psimulation=OrderFlowSimulation
   ```

2. **Establish baseline metrics** (t=0-15)
   - Note normal p95 latency for order creation
   - Check Kafka consumer lag
   - Verify cache hit ratio
   - Document normal error rate

3. **Take screenshots** of key dashboards

### Phase 2: Inject Chaos (30 minutes)

4. **Experiment 1: Pod Kill** (t=15)
   ```bash
   kubectl apply -f experiments/pod-failure-inventory-service.yaml
   ```
   - **Expected**: Alerts fire, pod restarts, brief degradation
   - **Observe**: Time to detect, time to recover, error rate impact

5. **Experiment 2: Network Latency** (t=25)
   ```bash
   kubectl apply -f experiments/network-latency-inventory.yaml
   ```
   - **Expected**: Increased latency, circuit breaker activation
   - **Observe**: Timeout behavior, fallback logic

6. **Experiment 3: Memory Stress** (t=35)
   ```bash
   kubectl apply -f experiments/resource-stress-inventory.yaml
   ```
   - **Expected**: Cache evictions, increased DB load
   - **Observe**: Cache miss ratio, database query latency

### Phase 3: Incident Response (30 minutes)

7. **Team responds** (t=45-75)
   - On-call engineer receives alert
   - Team investigates using dashboards/logs/traces
   - Root cause identified
   - Mitigation steps applied
   - Service restored

8. **Facilitator observations**
   - Time to acknowledge alert
   - Time to identify root cause
   - Effectiveness of runbooks
   - Communication quality
   - Mitigation effectiveness

### Phase 4: Recovery & Validation (15 minutes)

9. **Clean up experiments** (t=75)
   ```bash
   kubectl delete -f experiments/pod-failure-inventory-service.yaml
   kubectl delete -f experiments/network-latency-inventory.yaml
   kubectl delete -f experiments/resource-stress-inventory.yaml
   ```

10. **Verify recovery** (t=75-85)
    - Confirm metrics return to baseline
    - Check for lingering issues
    - Verify no data loss
    - Confirm consumer lag caught up

11. **Stop load testing** (t=85)

## Metrics to Track

| Metric | Pre-Chaos | During Chaos | Post-Chaos |
|--------|-----------|--------------|------------|
| Order creation p95 latency | | | |
| Error rate (%) | | | |
| Inventory service availability | | | |
| Cache hit ratio | | | |
| Kafka consumer lag | | | |
| Database connection pool usage | | | |
| Time to detect (TTD) | N/A | | N/A |
| Time to recovery (TTR) | N/A | | N/A |

## Success Criteria

✅ **Passed** if:
- Alerts fire within 2 minutes of failure
- No data loss or corruption
- Service recovers within 5 minutes of mitigation
- p95 latency degrades < 3x baseline
- Error rate < 10% during chaos

⚠️ **Needs improvement** if:
- Alerts delayed or missed
- Manual intervention required for recovery
- Cascading failures to other services
- Poor communication during incident

## Debrief Questions

1. What went well?
2. What could be improved?
3. Were runbooks helpful and accurate?
4. Did monitoring/alerting work as expected?
5. What gaps were identified?
6. What action items should we capture?

## Post-Game Day Actions

- [ ] Update runbooks based on learnings
- [ ] File issues for identified gaps
- [ ] Update alerts/dashboards if needed
- [ ] Document findings in `chaos-results/`
- [ ] Share learnings with wider team
- [ ] Schedule next game day

## Notes Section

(Facilitator to fill during game day)

### Timeline of Events
- 

### Key Learnings
- 

### Action Items
- 

### Participants
- 
