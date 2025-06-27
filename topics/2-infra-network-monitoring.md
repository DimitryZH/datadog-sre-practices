#  Infrastructure & Cloud Network Monitoring

This guide walks through two essential capabilities in Datadog:

- **Infrastructure Monitoring** — for tracking system-level metrics, host health, and telemetry across environments.
- **Cloud Network Monitoring (CNM)** — for visualizing and monitoring real-time communication between cloud-based services and hosts.


###  1. Host List: Grouping and Filtering

Datadog's **Host List** provides a real-time table of all monitored hosts, allowing you to filter and organize by tags, view key performance indicators, and inspect host metadata.

####  Steps:

1. Use the left sidebar to navigate to **Infrastructure > Hosts**.
2. In the upper-right corner, find the **Past 2 Hours** dropdown. This sets the reporting window for visible hosts (can be adjusted up to one week).
3. Review the host list. Each row contains:
   - **Hostname** – The host’s alias.
   - **OS & Agent Icons** – OS (e.g., Linux penguin) and Agent version (hover to reveal).
   - **Status** – Shows `ACTIVE` if reporting metrics; `INACTIVE` otherwise.
   - **CPU** – Latest CPU usage.
   - **IOWait** – Percent of CPU time spent waiting on I/O (if available).
   - **Load 15** – Load average over the past 15 minutes.
   - **Apps** – Any active Datadog integrations.

4. Near the top of the page, locate the **Search by** and **Group by** fields:
   - In **Search by**, enter `region:us-east-1` to filter hosts in that region (e.g., `backend-host`, `frontend-host`).
   - Clear the filter to show all hosts again.

5. In **Group by**, enter or select `instance-type`. The host list is now organized into groups by instance type. Expand groups to view the hosts they contain.

6. Combine **Search by** and **Group by** for more powerful queries:
   - Leave `Group by: instance-type`.
   - In **Search by**, enter `apps:container`.
   - You now see only containerized hosts, grouped by instance type.

This interactive view helps you quickly analyze infrastructure distribution and isolate groups of interest.
