# Datadog Application Performance Monitoring (APM), APM Metrics & Traces

# Part 1: APM overview

## 1. Accessing Trace Explorer

1. Navigate to **APM > Traces > Explorer** in the Datadog interface.
2. Close the introductory banner to maximize the workspace.
3. Filter by environment:
4. Observe the reporting services and live trace data.
5. Expand the **Requests, Errors, and Latency (RED)** section to see service summaries.


![apm-traces-explorer](https://github.com/user-attachments/assets/4c899920-c8e5-4a4a-9e85-a4d6a29aa5de)

## 2. Exploring Service-Level RED Metrics

1. Go to **APM > Software Catalog > Services**.
2. Select the **discount** service and click **Service Page**.
3. Confirm:
- Metadata (language, type)
- Environment tag (`env:apm-getting-started`)
- Primary operation name (e.g., `flask.request`)
4. Set the time range to **Past 15 minutes**.

apm-software-catalog-services.gif

### RED Metrics
- **Requests & Errors**: Observe spikes in traffic or failure rates.
- **Latency**: Toggle between `p50`, `p75`, and `p90` to assess outliers.
- **Downstream Time**: See if latency is due to downstream services (e.g., `postgres`).


![latency-graph](https://github.com/user-attachments/assets/e6c1f14e-1a55-4a23-8fcf-05711caf4ace)

## 3. Endpoint-Level Metric Analysis

1. Scroll to the **Endpoints** section.
2. Focus on:
- `GET /discount`
- `GET /discount-code`
3. Use the legend to isolate the `GET /discount` data.
4. Click **Resource Page** for that endpoint.

### Endpoint Resource Page
- Confirm the primary operation (`flask.request`).
- Identify latency spikes.
- Click a spike to open the **Trace View** side panel.
  

![endpoint-resource-page](https://github.com/user-attachments/assets/5177a504-757d-428e-b8b6-06addd1b7a0d)

## 4. Analyzing Traces in Detail

### Flame Graph
- Shows all spans for the trace.
- Top-level span (frontend service) shows entry point and full duration.
- Hover to view:
- Duration
- Service
- Operation name


![flame-graph](https://github.com/user-attachments/assets/6c2179c7-7029-497a-9c24-0529576e49c7)

###  Waterfall View
- Hierarchical timeline of spans.

###  Span List
- Table of all resources in the trace.
- Sort/filter by:
- Span count
- Duration
- Execution time
- Service

###  Map View
- Service dependency diagram.
- Shows percentage of execution time by service.


##  Summary of Tasks Completed

- Explored **Trace Explorer**
- Navigated via **Software Catalog** to the service page
- Analyzed **RED metrics** (service and endpoint levels)
- Interpreted **Flame Graphs**
- Used **Waterfall**, **Span List**, and **Map** views


#  Part 2: Analyzing Metrics and Traces to Assess Performance Changes

##  Scenario

A holiday sale has just gone live, and with it comes a surge of user traffic. The engineering team has proactively optimized the system, anticipating a significant increase in load across services.

Our goal is to **observe how application metrics and distributed traces reflect changes in system behavior under pressure**. We will use Datadog APM tools to detect potential regressions, isolate performance bottlenecks, and investigate how traffic spikes influence error rates and latency patterns.


##  Setting Goals

During this workflow, we will:

-  **Search and filter traces** to isolate service-specific behavior
-  **Group and visualize trace data** to identify patterns and performance anomalies
-  **Interpret real-time metrics** to understand system responsiveness
-  **Analyze performance degradation** triggered by the traffic surge (e.g., spikes in latency, error rates, or saturation)


##  Step-by-Step Workflow


### 1. Observe Incoming Traffic and Behavior

When you're interested in **live data**, **Trace Explorer** is a good starting point because it shows a continuous stream of all incoming trace telemetry.

####  Steps:

1. Navigate to **APM > Traces > Trace Explorer**.
2. At the top of the page, confirm the time range is set to `LIVE`. This view includes trace data from the last 15 minutes.
3. (Optional) Click the **Pause** button near the time picker to temporarily stop the incoming stream and review current traces.
4. Click **Stream** to resume real-time updates.

####  Narrowing Down Traces with Facets

Facets allow you to **filter the trace list** based on key service, environment, operation, and other metadata:

5. In the left sidebar, expand the **Env** facet and ensure that only `apm-getting-started` is selected.  
   > Tip: Deselect any other environments from previous labs or runs.

6. Scroll to the **Service** facet. Hover over `store-frontend` and click to isolate its traces.
7. Observe how the list and the search bar update dynamically with the selected filter.
8. To clear the filter, either:
   - Click the service name again, or  
   - Hover over `service:store-frontend` in the search bar and click **X**.

9. Optionally, deselect `store-worker-redis` to exclude it from view.
10. Explore other facets like **Operation Name**, **Status**, and **Resource**. Observe how selecting each value changes the results in the trace list.

11. When finished, click the **X** in the search bar to clear all filters.
12. Re-select `apm-getting-started` under the **Env** facet to return to the full lab environment view.


####  Advanced Filtering with Span Attributes

We can use **span attributes** (key-value metadata) to further refine and explore traces:

13. Filter to only show traces for the `store-backend` service.
14. Expand the **Operation Name** facet and select `rails.action_controller`.

15. Open any trace in the list to view its **Trace View side panel**.
16. Scroll down to **Span > Overview > Span Attributes**.

We will find structured data such as:
- Language: Ruby  
- Environment: apm-getting-started  
- Rails-specific keys: e.g., `rails.route.action`, `rails.route.controller`

17. Hover over `rails.route.action`, click the **gear icon**, and select **Create facet for @rails.route.action**.
18. Confirm by clicking **Add** in the dialog.
19. Close the Trace View side panel.
20. Scroll down the Trace Explorer sidebar. You should now see a new facet: `rails.route.action`.
21. Expand it to view route actions (e.g., `create`, `show`, etc.). This lets you filter traces by specific request actions in Rails.

![create-facet](https://github.com/user-attachments/assets/27ea1c8d-f110-4e3c-9c07-7147b1c86027)

---

In the next step, we will see how to **group and visualize trace data** to discover patterns and anomalies in your service behavior.

### 2. Group and Visualize Trace Data

Trace Explorer allows you to **visualize and group traces** to identify patterns, distributions, and trace characteristics across services and operations.

Instead of browsing long lists of raw trace data, switching to visual formats like pie charts or bar graphs helps you understand relationships between endpoints, actions, and performance bottlenecks.


####  Steps:

1. Scroll to the top of the page. The search bar should include:
`env:apm-getting-started` `service:store-backend` `operation_name:rails.action_controller`

2. Locate the **Visualize as** option and change it from `List` to **Pie Chart**.

3. Under the **Group into fields** dropdown, select:
**Show count of all spans by rails.route.action**

This pie chart shows how traces are distributed across different **Rails actions** (e.g., `create`, `update`, `show`) based on span attributes in the `store-backend` service.

4. Click a **segment** of the pie chart.  
→ In the context menu that appears, select **View spans** to open the trace list filtered by that specific action.

5. Click any trace in the list to open the **Trace View side panel**.  
→ The flame graph should now outline the `rails.route.action` that corresponds to the selected chart segment.

6. Close the Trace View panel.

####  Continue Exploring

7. Experiment with different combinations of:
- **Facets** (e.g., status, resource, response code)
- **Group by** fields (e.g., controller, service, error type)
- **Visualize as** options (e.g., bar chart, time series)

This helps uncover trends like which operations dominate latency, or which endpoints are error-prone.

8. When finished:
- Close any open panels,
- Clear the search bar by clicking the **X**,
- Reset **Visualize as** to `List`.

> Tip *Use visualization mode when comparing performance between different operations, services, or endpoints. It makes spikes and anomalies easier to detect.*

![visualize-trace-data](https://github.com/user-attachments/assets/3df4f805-5ddc-4a6f-8176-fd670f4128aa)

---

Now that we have practiced analyzing trace data, we can go further by correlating traces with logs — logs capture detailed events and messages that provide additional context for the traces we've been examining.

### 3. Correlate Traces with Logs

Datadog allows you to **correlate traces with their associated logs**, offering deeper insights into system behavior. If log collection is enabled and properly integrated, each log and trace shares a common `trace_id`, enabling seamless navigation between them.

####  Steps:

1. In the left-hand navigation menu, go to:
**Logs > Search & Analytics > Explorer**

>  **Note:** Logs are not enabled by default. If prompted, click the **Enable Logs** button and follow the setup instructions.

2. Once inside **Log Explorer**, we will notice a familiar layout—search bar, facet panel, and live-updating log entries.

3. Under the **Service** facet, select `store-ads-java` to filter logs for that service.

4. Look for a log event with the message containing: `/ads called`

You can use the search field to enter that text for easier filtering.

> Tip: Pause the log stream to view logs more easily.

5. Click a relevant log event to open its **details panel**.  
→ In the **Event Attributes** section, click the **Trace** tab.

6. You’ll now see a **flame graph** for the associated trace—just like in Trace Explorer. This is possible because Datadog uses the shared `trace_id`.

7. Click **View Trace in APM** to open the **full trace view** in APM.

8. Inside the trace view, navigate to the **Logs** tab to see all logs that share the same trace ID and are part of this execution path.


>  Correlating traces with logs helps you connect **high-level performance issues** (e.g., latency spikes) with **low-level root causes** (e.g., exceptions, warnings, and retry loops).


![correlate-traces-with-logs](https://github.com/user-attachments/assets/c7b5f273-1509-40e8-9195-ce75467f499a)

---

Now that we have connected traces to logs, we will observe **system performance during the holiday sale surge** to assess how the application handles increased traffic.

### 4. Observe Application Performance Under Load

As the holiday sale drives a surge in traffic, **Trace Explorer's summary graphs reveal system strain**—with rising request volumes, increased errors, and latency spikes in key services. The engineering team deployed updates to handle the load, and now it's time to **verify if the system is managing it effectively**.

To begin, we'll analyze latency distribution across services to identify potential bottlenecks.


####  Visualize Latency Across Services

1. Under the **Visualize as** option, select `Timeseries`.

2. In **Group into fields**, choose: `Show Duration by Service` 

> The graph now displays average request duration per service over time.

3. Hover over service names in the legend to highlight their corresponding graph lines.
- `store-frontend` (yellow) and `store-worker` (purple) exhibit the most variable and highest latencies.
- Other services like `store-discounts` and `store-ads-java` remain relatively stable.
- `store-backend` has a brief latency spike but is otherwise consistent.

**Insight**: High latency in `store-frontend`—the public-facing service—may affect user experience and deserves further investigation.

![timeseries-data](https://github.com/user-attachments/assets/2f7a2f1e-1a7a-4edc-aad0-6aeab30d3924)

---

####  Drill Down Into Frontend Errors

4. In the search bar, type: `service:store-frontend`

*(Ensure `env:apm-getting-started` remains in the query.)*

5. Change the **visualization** type back to `List`.

6. Observe the **Requests, Errors, and Latency** graphs. You'll notice:
- Sharp spikes in latency
- A noticeable rise in 500 errors
- High request volumes

7. In the top-left corner, click the **Service** tab to open the **store-frontend Service Page**.

8. On the Service Page, scroll to the **Service Summary** section. The RED metrics reflect high traffic and frequent errors.

9. Notice the **Errors** graph is showing a consistent stream of 500 errors.

10. Use the graph’s dropdown to switch from **Count** to **Error Rate**.  
 → This will show errors as a percentage of total requests, which makes it easier to assess severity.

####  Identify the Faulty Endpoint

11. Scroll down to the **Resources** section. The **Errors** graph here breaks down error counts by endpoint.

 - The legend indicates that only one endpoint is responsible for errors:
   ```
   GET /api/products/_slug
   ```

12. Below the graph, locate the **Resource Metrics Table**.

13. Click the **Error Rate** column to sort endpoints by error rate.

 → Only `GET /api/products/_slug` has a non-zero error rate—around **30%**.

 >  Other endpoints may exhibit latency, but they're not failing. This isolates the source of errors.


![error-rate](https://github.com/user-attachments/assets/2e36652f-19a5-4ec8-a270-04023a9c0834)

---

We now have actionable insights to include in a ticket:
- High frontend latency under load
- Repeated 500 errors from a single resource
- Other endpoints stable but slightly slower

Next, we will investigate the **specific error traces** to identify what’s going wrong inside `GET /api/products/_slug`.

### 5. Investigate Failing Traces

Now that we've identified `GET /api/products/_slug` as the primary source of 500 errors in the `store-frontend` service, it's time to inspect the **error traces** to understand the root cause.

By exploring **failing spans in Trace Explorer**, we can determine which services are affected, isolate the issue, and collect supporting evidence for handoff to the engineering team.

---

####  Trace the Origin of Errors

1. On the **store-frontend Service Page**, scroll to the **Resources** section.

2. In the **Errors** graph:
   - Hover over any bar showing elevated errors
   - Click the bar and select:
     ```
     View related traces
     ```

   >  This action opens **Trace Explorer**, pre-filtered based on the error data you selected.

3. Click any trace in the list to open the **Trace View** side panel.

4. Observe the **flame graph**:
   - Error spans are **highlighted** in red
   - Hover over spans to view detailed metadata

   >  The flame graph shows that **error spans are isolated to the `store-frontend` service**.  
   There are **no upstream or downstream dependencies involved**, ruling out external causes.

5. Scroll down to the error message section below the graph.  
   - The **lack of extra error metadata** suggests this could be an **unhandled exception**.

![errors-graph-related-traces](https://github.com/user-attachments/assets/d973e7f2-11e1-4502-aef7-7a48af44c505)

---

We have now collected enough information for a detailed incident report:
- The error is isolated to one endpoint: `GET /api/products/_slug`
- The `store-frontend` service is solely responsible for the issue
- No database or other service dependencies are involved
- The issue may stem from an unhandled error scenario in the frontend code

**Recommendation**: Pass these findings to the frontend engineering team for further triage and resolution.

##  Summary

The application has been able to handle the increased traffic from the holiday sale, though not without some issues.

Throughout this workflow, you used Datadog’s APM tools to analyze service-level performance, trace-level execution, and log correlation to diagnose the system’s behavior under load. Here's what was accomplished:

1. **Searched and filtered traces** to focus on specific services and operations.
2. **Observed key RED metrics** — request volume, latency, and error rates — across microservices like `store-frontend`, `store-discounts`, and `store-backend`.
3. **Expanded filtering options** in Trace Explorer by promoting span attributes to facets.
4. **Grouped and visualized data** to detect patterns (e.g., by service, action, or duration).
5. **Enabled logs** and used `trace_id` to correlate logs with corresponding traces.
6. **Analyzed service pages** with detailed RED metrics, endpoint-level latency, and error distribution.
7. **Investigated error traces** to determine which services and resources were impacted.



> We have now collected enough information for a detailed incident report:

- The error is isolated to a single endpoint: `GET /api/products/_slug`.
- The affected service is `store-frontend`, responsible for rendering the public-facing site.
- No database (`postgres`) or other backend services were involved — latency and errors originated and ended within the frontend service.
- The spike in 500 errors began during increased traffic and was concentrated on one resource.
- Error traces and span views suggest this is likely due to **an unhandled error or edge-case input scenario**.

 **Recommendation**:  
- The frontend engineering team should investigate the `GET /api/products/_slug` route.
- Logs and traces indicate a need for **better error handling** or **validation logic** to address unexpected request parameters or states.
- Consider defining an **SLO** for this critical endpoint to ensure future performance remains within acceptable thresholds.


**Based on this work, you can find an example of the Incident Report here: [`incident-report.md`](incident-report.md)**