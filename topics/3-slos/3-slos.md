# Service Level Objectives (SLOs)

##  1. Investigate App Performance

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


##  2. Creating SLOs

In the previous section, we worked with the app service to identify metrics that define our Service Level Objectives (SLOs). We successfully selected three key metrics to track the error rate and latency of our web application:

- `trace.web.request`
- `trace.web.request.hits`
- `trace.web.request.errors`

In this section, we'll create **two SLOs** to track the performance of the `store-frontend` service in Storedog. We'll start by building a latency monitor, then use it to create a monitor-based SLO, followed by a metric-based SLO for error rate.

---

###  2.1 Build a Latency Monitor

To track latency for our app, we’ll first create a monitor. This will serve as the **Service Level Indicator (SLI)** for our first SLO.

1. Open the **Quick Nav** menu, type `New Monitor`, and press Enter.
2. In the top-left corner, select **Metric**.
3. In the bottom-right corner, choose **Configure Metric Monitor**.
4. Scroll to **Define the Metric** and enter: `trace.web.request`
5. In the **from** field, enter: `service:store-frontend`
6. Under **Set alert conditions**, set:
- **Alert threshold**: 3
- **Warning threshold**: 2
7. Under **Configure notifications & automations**, set the title to: `store-frontend latency alert`
8. Add a description: `store-frontend latency is high. Customers may be experiencing slow responses.`

9. Click **Create** in the bottom-right corner.

 store-frontend_alert_monitor.gif

 > We've now created a monitor to track latency for the `store-frontend` service. Involving the team in selecting thresholds is essential, as every app and environment is different.

---

###  2.2 Create a Monitor-Based SLO

With our latency monitor in place, we'll now create a **monitor-based SLO**. This allows us to define an uptime target based on the monitor status.

1. Open the **Quick Nav**, type `New SLO`, and press Enter.
2. Under **Define our SLO measurement**, select **By Monitor Uptime**.
3. Choose our previously created monitor: `store-frontend latency alert`
4. Under **Set our target & time window**, select:
- **Target**: 90%
- **Time window**: 7 Days
5. Add a name: `store-frontend latency`
6. Add a description: `Tracks store-frontend latency to ensure fast response times and a smooth user experience.`
7. Under **Services**, select: `store-frontend`
8. Click **Create**.

 configure_monitor_based_slo.gif

 We've successfully created our first SLO for latency. Collaborating with the team helped us agree on a 90% target as a good starting point.

---

###  2.3 Create a Metric-Based SLO (Error Rate)

Next, we’ll create a **metric-based SLO** to track error rates for `store-frontend`.

1. On the main **SLO** page, click **New SLO** in the upper-right.
2. Under **Define your SLO measurement**, select **By Count**.

####  Good Events (Successful Requests)
- **Metric**: `trace.web.request.hits`
- **from**: `service:store-frontend`

Click **Add Query**.

####  Bad Events (Failed Requests)
- **Metric**: `trace.web.request.errors`
- **from**: `service:store-frontend`

3. Below the queries, set the **Formula** to: `a-b`

####  Total Events (Baseline)
- **Metric**: `trace.web.request.hits`
- **from**: `service:store-frontend`

4. Under **Set your target & time window**:
- **Target**: 95%
- **Time window**: 7 Days

5. Name the SLO: `store-frontend error rate`

6. Add a description: `Tracks error rate for store-frontend to ensure reliable user experience and reduce silent failures.`

7. Under **Services**, select `store-frontend`.
8. Click **Create**.

configure_metric_based_slo.gif
> We've now created our second SLO, focused on error rates.

---

> In this section, we created two SLOs to track the performance of the `store-frontend` service in Storedog:

- A **monitor-based SLO** to track **latency**
- A **metric-based SLO** to track **error rate**

We collaborated with our team to define meaningful thresholds and targets, ensuring these SLOs reflect realistic service goals. In the next part, we’ll refine our SLOs and create **burn rate monitors** to proactively detect issues.


##  3. Adjust SLO Goals

Now that our SLOs are set up, the next step is to ensure they **accurately reflect user experience** and **performance expectations**. While our existing latency and error rate SLOs appear to be performing well in Datadog, customer complaints are still coming in. This suggests that our SLO targets might be **too lenient** and are not capturing real-world issues.

To address this, we’ll revise both SLOs to reflect **stricter, more realistic targets** that align with the **business impact** and user expectations.

---

###  3.1 Update the Latency SLO

Your team agrees that the current target of **90%** is too low, especially given the critical importance of the `store-frontend` service.

#### Steps:

1. Open the **Quick Nav** menu, type `SLOs`, and press Enter.  
   *(Be sure to select `SLOs` and **not** `New SLO`.)*
2. Select the **`store-frontend latency`** SLO.
3. Click the **gear icon** in the top-right corner and choose **Edit SLO**.
4. Under **Set your target & time window**, change:  `From: 90% To: 99%`
5. Click **Save**.

 This update aligns your latency goal with the critical nature of frontend performance and gives your team better visibility into subpar user experience.

---

###  3.2 Update the Error Rate SLO

The original **95% target** for error rate is also too relaxed. After team discussions, you’ve decided to raise the target to **99.9%** to reflect your app’s reliability standards.

#### Steps:

1. From the main **SLO** page, select the **`store-frontend error rate`** SLO.
2. Click the **gear icon** in the top-right corner and choose **Edit SLO**.
3. Under **Set your target & time window**, update the target: `From: 95% To: 99.9%`
4. Click **Save**.

 adjusted_slos_list.gif

---

###  Why Stricter SLOs Matter

After updating your SLOs, you may notice the **error rate SLO turns red** (i.e. no longer within the target). While that might seem like a negative change, it's actually a **positive insight**:

> A lower target like 95% could **hide reliability issues**, whereas raising the bar to 99.9% **uncovers areas that need improvement**.

This update will help drive conversations, prioritize engineering efforts, and improve end-user experience in meaningful ways.

---

> We've successfully **adjusted both latency and error rate SLOs** to more accurately reflect application performance and user expectations. In the next section, we’ll create **monitors for SLO burn rate** to proactively detect when service degradation might breach our targets.


##  4. Monitor an Error Budget

Creating SLOs is only the beginning — the real power comes from **monitoring** how much of your **error budget** is being consumed. In this step, we'll create a **monitor** that tracks the error budget for the `store-frontend` **latency SLO**. This enables your team to react quickly if too much of the budget is being used too soon.

>  **What is an error budget?**  
> An error budget represents the **allowable margin for failure** before breaching an SLO. 

---

###  Steps to Monitor the Error Budget

1. From the main **SLOs** page, select **`store-frontend latency`**.
2. In the top-right corner, click the **gear icon** and choose **Set up alerts**.
3. Ensure **store-frontend latency** is the selected SLO.
4. Under **Set alert conditions**, make sure **Error Budget** is selected.
5. Set the thresholds:
   - **Alert value** → `100`
   - **Warn value** → `75`
6. Add a **monitor name**: `store-frontend latency SLO alert`
7. Provide a **short description**, such as:`The store-frontend latency SLO is burning its error budget too quickly. Please investigate to avoid breaching the SLO.`
8. Click **Create** to finalize the monitor.

monitor_latency_slo_alert.gif

---

> Result.

Our team is now equipped with a **monitor-based alert** that will trigger when the error budget for latency is approaching exhaustion. This kind of proactive monitoring allows teams to:

- Catch performance degradation **before** it impacts users.
- Adjust release velocity based on SLO health.
- Collaborate cross-functionally to resolve issues before breaching targets.

 **Reminder**: Always calibrate alert/warning thresholds in collaboration with your engineering and reliability teams. Different environments and SLAs require different tolerances.

---

##  5. Monitor the Burn Rate

While monitoring an SLO’s **error budget** is essential, it doesn’t always give a complete picture of how quickly things are going wrong. You may only catch an issue **after** breaching a threshold. To act earlier, we need to track how fast we’re consuming our error budget — this is known as the **burn rate**.

Monitoring the **burn rate** enables our team to:
- Identify **escalating issues** before an SLO is violated.
- React **proactively** to performance degradation.
- Understand the **velocity** of failure, not just its presence.

---


We’ll now create a **burn rate alert** on our existing SLO for the `store-frontend` error rate.

#### Steps:

1. Open the **Quick Nav** menu, type `SLOs`, and press **Enter**.
2. Select the SLO: **`store-frontend error rate`**.
3. Click the **gear icon** in the top-right and choose **Set up alerts**.
4. Ensure `store-frontend error rate` is selected.
5. Under **Set alert conditions**, select **Burn Rate**.

---
### Burn Rate Calculation
With an SLO target of **99.9%**, our error budget is **100% - 99.9% = 0.1%** 


For a **7-day SLO window** (168 hours), this means: **Error budget time = 168 hrs × 0.001 = 0.168 hrs = 10.08 minutes**

To act before breaching this budget, we set a burn rate alert threshold for when **10% of the budget** is used in a **1-hour window**.

**Burn Rate Formula:**
`burn rate = (SLO duration in hours × error budget used %) ÷ long window (in hours) × 100%`  
`burn rate = (168 × 0.1) ÷ 1 × 100% = 16.8`

6. Set thresholds:

- **Alert value** → `16.8`
- **Warn value** → `8.4`

7. Under **Configure notifications & automations**, add:

- **Name**: ` store-frontend error rate SLO alert `

- **Description**:  `The burn rate for store-frontend's error rate SLO is too high. Investigate this service immediately to avoid breaching the SLO target.`


7. Click **Create** to activate the burn rate monitor.

create_burn_rate_monitor.gif

---

>  Result

We've now implemented a **burn rate alert** that tracks how quickly the error budget is being consumed.

If the `store-frontend` service continues to experience elevated error rates, this alert will fire **before** the SLO is violated — giving your team time to investigate and correct the issue.

>  **Takeaway**: SLO burn rate monitoring helps bridge the gap between visibility and actionability. It ensures your team isn’t just reacting to failures — you’re staying ahead of them.

---
##  Summary

In this work, we designed and implemented a complete SLO monitoring system for the `store-frontend` service. This work led to the following key outcomes:

-  We created and tuned **monitor-based and metric-based SLOs** to track latency and error rate for a high-traffic service impacting real users.
-  We built a **custom latency monitor** and defined clear alert and warning thresholds aligned with application behavior.
-  We configured an **SLO target of 99% for latency** and **99.9% for error rate**, reflecting stricter expectations after initial review.
-  We calculated and implemented a **burn rate alert** using a real formula to track error budget consumption in near real-time, enabling proactive response before SLO violations occur.
-  We identified relevant SLIs (`trace.web.request.hits`, `trace.web.request.errors`, `trace.web.request`) that meaningfully represent user-perceived performance.
-  We validated our setup by adjusting targets to better reflect reality and exposing gaps between internal metrics and external user experience.
-  With monitors and burn rate alerts in place, we enabled the team to detect and respond faster to performance regressions, well before they turn into customer complaints.







 









