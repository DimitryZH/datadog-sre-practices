

#  Datadog SRE Practices

This repository is a curated collection of **practical Site Reliability Engineering (SRE) workflows** using **Datadog**. It covers real-world techniques for application observability, performance monitoring, incident response, and reliability metrics.

> These exercises are structured to simulate production environments and support hands-on exploration of metrics, traces, and service behavior using Datadog.

You'll explore how to **understand application performance**, **accurately monitor infrastructure and networking in real time**, and **implement SLO-driven strategies**—ensuring you can quickly detect, analyze, and resolve system-wide issues.

By working through these projects, you will learn how to:

- ✅ Track service health by interpreting key application performance metrics (e.g., request volume, error rate, latency)
- ✅ Investigate infrastructure metrics by filtering, grouping, and visualizing data with tags
- ✅ Gain deep visibility into network performance to identify bottlenecks and failures
- ✅ Establish effective SLIs, set realistic SLO targets, and manage SLOs with Datadog
- ✅ Centralize and manage incident response with observability tooling

## Application Architecture
 
The project simulates a containerized microservices architecture consisting of:

- **Frontend service** – Next.js application
- **Backend service** – Ruby on Rails (business logic)
- **Discounts service** – Python/Flask (promotions API)
- **Ads service** – Java/Spring Boot (third-party ad server)
- **PostgreSQL** – Relational database

Each service is instrumented with Datadog Agents to stream telemetry for analysis.


##  Project Topics

| # | Category | Description |
|--:|:---------|:------------|
| 1 | [APM Metrics & Traces](topics/1-apm-metrics-traces/1-apm-metrics-traces.md) | Trace visualization and RED metrics |
| 2 | [Infrastructure & Cloud Network Monitoring](topics/2-infra-network-monitoring/2-infra-network-monitoring.md) | Monitoring servers, containers, and network flows |
| 3 | [Service Level Objectives (SLOs)](topics/3-slos/3-slos.md) | Define and track service reliability metrics |
| 4 | [Incident Management](topics/4-incident-management/4-incident-management.md) | Runbooks, alert triage, root cause analysis, postmortems |







