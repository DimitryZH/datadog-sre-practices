# Incident Report: Network Performance Degradation in `store-discounts`

## Timeframe

**Start:** 14:00 EST  
**End:** 16:30 EST  
**Duration:** 2.5 hours

---

## Summary

During the holiday promotion window, users began reporting intermittent issues when applying promo codes and slower response times across the Storedog platform. Initial CPU and application-level metrics were ruled out. Using **Cloud Network Monitoring (CNM)** and correlated APM traces in **Datadog**, we identified a **network-level degradation** involving the `store-discounts` service as the root cause.

---

## Root Cause Analysis

- **High TCP Latency and RTT** values were observed for flows where `store-discounts` was the client.
- **Sustained high TCP Retransmit %** indicated severe packet loss in connections between `store-discounts` and the `database` service.
- **APM traces** confirmed long request durations to `/discount`, but no application errors or failed queries were found.
- The combination of network metrics and trace data strongly suggested a **faulty network interface or container connectivity issue** on the `store-discounts` host.

---

## Resolution

- The network issue was reported to the `store-discounts` service owners.
- The team identified a misconfigured network driver on the container host and redeployed the service with updated configuration.
- Network telemetry confirmed that latency and retransmit rates dropped significantly after the fix.

---

## Preventative Actions

- A **Datadog monitor** was created to alert on `system.cpu.user > 90%` to catch CPU-related issues proactively.
- **CNM alerts will be implemented** for detecting unusually high TCP Retransmit % in service-to-database flows.
- **Network performance SLOs** will be defined for critical services like `store-discounts`.

---

## Recommendations

- **Network Engineering** should audit container network configurations across all services to prevent recurrence.
- **Discounts team** should build logic to degrade gracefully during partial failures (e.g., cached responses or fallback flows).
- **Observability team** should expand CNM usage to define baseline behavior and detect regressions faster.
