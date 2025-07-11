---
title: 'Postmortem IR-1: Latency Issue on Homepage'
author: DimitryZH
modified: 2025-07-10T16:32:53.983Z
tags: []
metadata:
  type: Postmortem
time:
  live_span: 1h
template_variables: []
---


| INCIDENT PROPERTIES| |
|-|-|
| **Status**| resolved|
| **Severity**| SEV-2|
| **Started**| Jul 10, 2025 03:03 pm UTC|
| **Commander**| DimitryZH |
| **Incident Overview**| [IR-1](/incidents/1)|

_You can generate a postmortem [from any resolved incident](https://app.datadoghq.com/incidents?query=state%3Aresolved) with these fields pre-filled, along with incident metadata and timeline._

# What Happened?

### Impact on Customers

Customers are experiencing long loading times for our site.

This lasted for 1 hour, 25 minutes and 18 seconds from Jul 10, 2025 03:03 pm UTC to Jul 10, 2025 04:28 pm UTC.

# Why Did it Happen?

### Root Cause

Debug statements left in the ad service code and an uncaught N+1 query

# Timeline

---

#### Jul 10 2025 at 11:03 am EDT

**DimitryZH**

<https://app.datadoghq.com/monitors/177526453?from_ts=1752157995329&to_ts=1752159795329&live=true>

---

#### Jul 10 2025 at 11:06 am EDT

**Severity updated to** **SEV-2** by DimitryZH

**Attribute updated**

**Detection Method**: Monitor

---

#### Jul 10 2025 at 11:06 am EDT

**Impact added** by DimitryZH

**Scope**: Customers are experiencing long loading times for our site.

**Type**: Customer

**Started at**: Jul 10, 11:03 am

---

#### Jul 10 2025 at 11:29 am EDT

**Task created** by DimitryZH

Look at app traces

---

#### Jul 10 2025 at 11:30 am EDT

**Task deleted** by DimitryZH

Look at app traces

Assignees: DimitryZH,

---

#### Jul 10 2025 at 11:32 am EDT

**Task updated** by DimitryZH

Look at app traces

Assignees: DimitryZH

---

#### Jul 10 2025 at 11:34 am EDT

**DimitryZH**

[https://app.datadoghq.com/apm/trace/3150020307493418059?graphType=flamegraph&shouldShowLegend=true&sort=time&spanID=2729055489444765596&timeHint=1752160116785.7324&\__preview=Trace+3150020…+(env%3Aincident-management)](https://app.datadoghq.com/apm/trace/3150020307493418059?graphType=flamegraph&shouldShowLegend=true&sort=time&spanID=2729055489444765596&timeHint=1752160116785.7324&__preview=Trace+3150020%E2%80%A6+%28env%3Aincident-management%29)

---

#### Jul 10 2025 at 11:36 am EDT

**Task deleted** by DimitryZH

Look at app traces

Assignees: DimitryZH

---

#### Jul 10 2025 at 11:37 am EDT

**Task updated** by DimitryZH

Look at app traces

Assignees: DimitryZH

---

#### Jul 10 2025 at 11:39 am EDT

**Task deleted** by DimitryZH

Look at app traces

Assignees: DimitryZH

**Task updated** Look at app traces

Assignees: DimitryZH

---

#### Jul 10 2025 at 11:42 am EDT

**DimitryZH**

[https://app.datadoghq.com/apm/trace/3150020307493418059?graphType=flamegraph&shouldShowLegend=true&sort=time&spanID=2729055489444765596&timeHint=1752160116785.7324&\__preview=Trace+3150020…+(env%3Aincident-management)](https://app.datadoghq.com/apm/trace/3150020307493418059?graphType=flamegraph&shouldShowLegend=true&sort=time&spanID=2729055489444765596&timeHint=1752160116785.7324&__preview=Trace+3150020%E2%80%A6+%28env%3Aincident-management%29)

---

#### Jul 10 2025 at 11:46 am EDT

**Attribute updated** by DimitryZH

**Services**: advertisements-service

---

#### Jul 10 2025 at 11:48 am EDT

**Attribute updated** by DimitryZH

**Services**: Removed

---

#### Jul 10 2025 at 11:49 am EDT

**Task completed** by DimitryZH

Look at app traces

**Task updated** Investigate ad service code

Assignees: DimitryZH

---

#### Jul 10 2025 at 11:50 am EDT

**Attribute updated** by DimitryZH

**Services**: advertisements-service store-frontend

---

#### Jul 10 2025 at 12:25 pm EDT

**Task completed** by DimitryZH

Investigate ad service code

---

#### Jul 10 2025 at 12:26 pm EDT

**Task completed** by DimitryZH

Look at app traces

---

#### Jul 10 2025 at 12:27 pm EDT

**Task completed** by DimitryZH

Look at app traces

---

#### Jul 10 2025 at 12:28 pm EDT

**Attribute updated** by DimitryZH

**Root Cause**: Debug statements left in the ad service code and an uncaught N+1 query

---

#### Jul 10 2025 at 12:28 pm EDT

**Impact updated** by DimitryZH

**Scope**: Customers are experiencing long loading times for our site.

**Ended at**: Jul 10, 12:28 pm

---

# How do we prevent it in the future?

### Action Items

* ~~Look at app traces~~

* **~~Created by:~~**~~ DimitryZH~~

* ~~Investigate ad service code~~

* **~~Created by:~~**~~ DimitryZH~~

### Additional Follow-Up Recommendations
Based on this incident, we can consider the following improvements:

-  Formalize a process for managing debug statements, e.g., using tags like `// DEBUG-START` and `// DEBUG-END`
-  Add a latency regression test for the `advertisements-service` in CI/CD
-  Review the effectiveness of the latency monitor:
  - Was the detection fast enough?
  - Are the thresholds and alerting conditions ideal?
  - Consider refining or adding additional monitors