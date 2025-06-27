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

![group_filter_hosts_list](https://github.com/user-attachments/assets/75982a24-794b-4d87-b2d0-31112824887d)


###  2. Use Datadog's Host Map

After noticing that `discounts-host` reports a CPU usage of ~100%, it's important to determine whether this is an isolated issue or indicative of a broader infrastructure problem. Datadog's **Host Map** is an ideal tool for this.

####  Why Use Host Map?

The **Host List** is useful for direct queries, but the **Host Map** offers a **visual overview** of your entire infrastructure—across platforms, cloud providers, data centers, and orchestration tools—surfacing trends and hotspots.

####  Steps:

1. Navigate to **Infrastructure > Hosts > Host Map**.
2. Each host appears as a hexagon; applications running on the host are shown as boxes within the hexagon (e.g., `container`, `docker`, `agent`).
3. **Color Fill in Host Map**:
   - Background colors represent a selected metric defined in the **Fill** field.
   - A legend in the lower-right corner helps interpret the colors.

####  Explore Host Metrics Visually:

4. In the **Fill** field at the top:
   - Enter `system.disk.free` to visualize available disk space across hosts.
   - Replace with `system.load.1` to visualize system load averages over the past minute.
   - Finally, replace with **CPU Utilization** (first dropdown option) to highlight CPU usage.

5. To the right of the **Fill** field, change the aggregation method from `avg` to `max` to identify peak values.

>  At this point, you'll  see **only `discounts-host` turning red**, indicating ~100% CPU utilization, while other hosts remain normal. This confirms that the issue is isolated.

![use_host_map](https://github.com/user-attachments/assets/42dee8c0-0606-48aa-ac26-9f4644622d88)

---
You’re now ready to notify the responsible team and create a **monitor** to automatically alert them if CPU usage spikes again.
