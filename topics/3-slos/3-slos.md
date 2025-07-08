## 🔍 1. Investigate App Performance

### Scenario

We’ve received reports that users are experiencing issues on the **Product page**. Some products take a while to load, and users may need to click multiple times before items appear. No errors are shown on screen, but this kind of behavior typically indicates underlying performance or reliability issues.

To investigate further, we’ll use **Datadog Real User Monitoring (RUM)** and **Software Catalog**.

---

###  Step 1: Start with Datadog RUM

**Real User Monitoring (RUM)** gives end-to-end visibility into real-time activity and user experience across our application. Since we don’t yet have a specific error to follow up on, this is the ideal starting point.

1. In Datadog, we navigate to **Digital Experience > Real User Monitoring**.
2. We ensure that our application is selected in the **top-left corner**.
3. We review the dashboard and pay attention to the following:
   - What types of errors are showing up?
   - Which service seems to generate the most errors?
   - What trends or latency spikes do we observe?

 **Observation:**  
A high error rate is observed in the application. Multiple requests are failing silently. After discussing with the team, we decide to track **error rate** and **latency** using **SLOs** so we can proactively monitor performance and user experience.

---

###  Step 2: Use Software Catalog to Investigate store-frontend

Since the **store-frontend** service appears to be the source of the issue, we’ll now drill into its performance metrics.

1. We use the **Quick Nav** to search for **Software Catalog Services** and hit Enter.
2. From the **main Software Catalog** page, we go to the **Performance** tab.
3. We hover over the **store-frontend** service and click **Service Page**.

---

###  Step 3: Identify Key Metrics for SLOs

We collect the relevant metrics to define our future SLIs and SLOs:

1. On the store-frontend service page, we locate the **Requests and Errors** graph.
2. We click **View Full Screen**.
3. We take note of the following metrics:
   - `trace.web.request.hits` – successful requests
   - `trace.web.request.errors` – failed requests

4. Next, we locate and expand the **Latency** graph.
   - We record the latency metric shown here (typically `trace.web.request` duration or latency distribution)

 These metrics will be essential when we define Service Level Indicators (SLIs) for the SLOs in the next steps.

---

###  Key Metrics Explained

| Metric | Description |
|--------|-------------|
| `trace.web.request` | Total number of traced web requests – useful for calculating request volume. |
| `trace.web.request.hits` | Successful requests (2xx status codes) – helps measure healthy traffic. |
| `trace.web.request.errors` | Failed requests (4xx/5xx status codes) – helps detect app or user experience problems. |

---

 **Next Step**: We review this information with our team to ensure alignment on metrics and expectations, and prepare to define SLOs that reflect our performance goals.

  analyze_app_performance.gif


