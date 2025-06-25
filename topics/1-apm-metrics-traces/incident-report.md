#  Incident Report: Holiday Sale Performance Degradation

**Date:** 17 June 2025  
**Prepared by:** Dmitry Zhuravlev  
**Monitoring Tool:** Datadog APM & Logs

---

##  Summary

During a scheduled system performance validation aligned with a holiday sale campaign, our monitoring suite revealed application slowdowns and service-level errors triggered by a surge in traffic.

Using Datadog’s tracing and log correlation tools, we performed a structured investigation focusing on the most affected areas. Below is a detailed breakdown of our findings.

---

##  Key Findings

-  **Traffic Surge:** Request volume increased across all services, as expected. Most services handled the load within acceptable latency thresholds.

-  **High Latency:** The `store-frontend` service experienced elevated latency, with spikes observed in the `p95` range. However, these were not tied to downstream services.

-  **500 Errors:** A subset of requests to the `store-frontend` endpoint `GET /api/products/_slug` failed with 500 errors. The error rate peaked around 30% for this resource.

-  **No Dependency Failures:** Traces confirmed that the errors were not linked to `store-backend`, `store-discounts`, or `postgres`. The issues were confined to the frontend tier.

-  **Span Inspection:** Flame graph analysis revealed a lack of downstream delays. The frontend spans ended prematurely with errors, and no retries or recovery behavior was evident.

-  **Unstructured Errors:** Trace error messages and logs suggest these are **unhandled exceptions** or poorly validated inputs within this route.

---

##  Supporting Metrics & Visuals

- RED metrics from Datadog Service Page (latency, errors, throughput)
- Filtered trace view of `store-frontend` with correlated spans
- Timeseries visualizations showing latency patterns
- Pie chart and grouped span metrics by Rails route
- Error log correlation using trace_id linkage

---

##  Recommendations

1. **Triage the GET /api/products/_slug handler**
   - Audit controller logic for error-prone operations.
   - Add try/catch blocks and return appropriate HTTP status codes for known failures.

2. **Expand Logging Context**
   - Include request parameters and user context in structured logs for better diagnostics.

3. **Establish an SLO**
   - Define a Service Level Objective for `store-frontend` error rate, especially for this route.

4. **Regression Testing**
   - Simulate malformed or edge-case slugs in a controlled environment to reproduce failures.

5. **Monitor Post-Fix**
   - Track the same RED metrics post-deployment to confirm error rate reduction.

---

## Status

**Issue Triaged:** ✅  
**Handoff to Engineering:** ✅  
**Remediation Ticket Created:** ✅

---

**Prepared By:**  
Dmitry Zhuravlev  
Production Engineer
