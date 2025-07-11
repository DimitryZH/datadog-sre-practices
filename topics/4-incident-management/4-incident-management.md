# Incident Management: Investigating and Resolving Latency Issues

##  Overview

Our e-commerce web application is experiencing performance issues — the home page is slow to load, and instead of the expected content, users are seeing a prolonged **“Please wait”** screen. Clearly, something is wrong.

We were first alerted by a Datadog monitor configured to track **average request latency** on the home page, measured using APM (Application Performance Monitoring). This monitor is set to trigger when the **average latency exceeds 3 seconds over a one-minute window**.

-  **Monitor Configuration:**  
  APM-based monitor targeting `store-frontend` latency  
  **Threshold:** Average latency > 3s (past 1 minute) 

store-frontend-average-latency-monitor.png

After receiving the alert, we opened the application in a browser to validate the problem and experienced the same delay. The app appeared stuck on the **loading screen**, failing to render the homepage.  

please-wait-page-load.png

##  Objective

In this work, we used **APM and Datadog Incident Management tools** to:

- Investigate the root cause of the performance degradation
- Track the issue across services and dependencies
- Use traces and logs to pinpoint the failure
- Walk through managing, documenting, and resolving the incident using Datadog’s integrated tools

---


