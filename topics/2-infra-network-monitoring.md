#  Infrastructure & Cloud Network Monitoring

This guide walks through two essential capabilities in Datadog:

- **Infrastructure Monitoring** — for tracking system-level metrics, host health, and telemetry across environments.
- **Cloud Network Monitoring (CNM)** — for visualizing and monitoring real-time communication between cloud-based services and hosts.


##  1. Infrastructure Monitoring


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



###  3. Explore Host Details

To dive deeper into the metrics of a specific host like `discounts-host`, you can access its full details directly from the Host Map.

####  Steps:

1. **Clear** any existing filters or groupings in the Host Map.
2. **Click** the hexagon labeled `discounts-host` to open its detailed view.

####  Host Detail Layout:

- **Left Panel (Apps)**: Shows running applications on the host.
- **Center Panel (Agent & System Info)**: Includes system details, metrics, containers, and Datadog Agent version.
- **Right Panel (Tags)**: Lists all tags associated with the host.

At the top, you’ll see a **horizontal menu of links** to explore more host data:
- **Dashboard**, **Network**, **Processes**, **Containers**, etc.

3. Click the **Dashboard** link to access the **prebuilt host dashboard**.

####  What You’ll See:

- **CPU Usage %** and **CPU Usage by Process %** charts show spikes nearing 100%.
- **Hover over CPU Usage %** to observe that most high usage originates from the `system.cpu.user` metric.
- These CPU spikes generally **correlate with the Load Averages chart**, suggesting user activity is driving load.
- Other metrics such as **disk I/O, memory usage**, and **network traffic** appear unaffected.

![host_details_dashboard](https://github.com/user-attachments/assets/2a4bf73f-2926-48ea-b987-ec756e0bc402)

>  This host-specific dashboard is a powerful tool for **root cause analysis**, especially when correlating CPU load with user processes.

###  4. Create a Monitor

After confirming that high CPU usage on `discounts-host` is due to user space processes—and isolated to that host—you contact the responsible team. They begin investigating but ask you to set up a **Datadog monitor** to catch similar issues in the future.

####  Steps to Create a CPU Usage Monitor:

1. From the **discounts-host dashboard**, locate the **CPU usage (%)** chart.
2. Click the **three dots** above the chart, then select **Create Monitor** from the dropdown.
3. In the modal:
   - Select the metric: `system.cpu.user{host:discounts-host}`.
   - Click **Create Monitor**.

####  Configure the Monitor:

**Under "Define the metric":**
- Remove `host:discounts-host` from the **from** field so it applies **everywhere**.
- Change the aggregator from `avg by` to `max by`.
- In the **grouping field**, enter or select `host`.

>  This allows the monitor to track CPU usage individually for each host and detect spikes independently.

**Under "Set alert conditions":**
- Set:
  - **Warning threshold** = `80`
  - **Alert threshold** = `90`

**Under "Configure notification & automations":**
- **Subject**:  
  `High CPU load on {{host.name}}`
- **Message**:
  ```plaintext
  {{host.name}} is demonstrating an unusually high `system.cpu.user` load. Investigate and resolve accordingly.

- Leave remaining fields unchanged and click Create.

- You'll be redirected to the monitor details page, where you can view its configuration or adjust settings later if needed.

![create_monitor](https://github.com/user-attachments/assets/d874bd15-f45e-44ee-94f6-8ce9b83411b9)



###  5. Mute a Monitor

After configuring a monitor for high CPU usage, the `discounts-host` team starts receiving alerts. Since they are actively working on resolving the issue, they ask you to **mute notifications** for one hour to avoid unnecessary interruptions.

####  Review the Monitor

1. If you're not already on the monitor's detail page, go to **Monitors > Monitors List** and select **High CPU load on {{host.name}}**.
2. In the upper-right, change the time range to **Past 30 minutes**.
3. Examine the **timeline chart** near the top — red bars represent CPU usage spikes that crossed the alert threshold.
4. Review the **host:discounts-host** graph below. Any red shading indicates recent or current CPU usage above the alert threshold.

---

###  Option 1: Mute a Single Monitor

You can mute notifications for this specific monitor only:

1. On the monitor’s detail page, click the **Mute** button (upper-left).
2. In the modal that appears, click **Specific Groups**.
3. From the dropdown, choose the **discounts-host**.
4. Do not click "Create Downtime" for now. Instead, cancel to try another method.

![mute_monitor_p1](https://github.com/user-attachments/assets/c5c026b5-fe22-475c-9d2d-e4c3d3386399)

>  This method only affects this specific monitor.

---

###  Option 2: Mute All Alerts for a Host

To mute **all alerts across all monitors** related to a specific host:

1. Navigate to **Infrastructure > Hosts**.
2. Hover over `discounts-host` — click the **Mute host alerts** icon (volume symbol).
3. From the dropdown, select **1h** (one hour).
4. Return to **Monitors > Monitors List** and re-select **High CPU load** monitor.
5. Set time range to **Past 30 minutes**.
6. Review the **host:discounts-host** chart — a **gray shaded region** marks the mute period.

![mute_monitor_p2](https://github.com/user-attachments/assets/da91c154-02ad-4471-9d29-60e567fe9cd5)

>  This is a more efficient way to silence alerts when you're already aware of the issue and actively working on it.


###  6. Unmute Monitor – Host Recovery

After deploying a fix, the team managing `discounts-host` reports that the high CPU usage issue has been resolved. Now it's time to verify host recovery and **unmute alerts** to resume normal monitoring.

####  Confirm Host Recovery

1. Go to **Infrastructure > Hosts** to access the **Host List**.
2. Click **discounts-host** to open its detail panel.
3. In the panel, click **Open in Host Dashboard** (top-right).
4. Use the time range dropdown (top-right of dashboard) to select **Past 1 hour**.

You should see:
- **CPU Usage by Process %** and **CPU Usage %** charts showing total usage below 5%.
- **Load Averages** trending downward — confirming system stability.

---

###  Unmute Alerts for the Host

Now that the issue is resolved and the host is healthy:

1. Return to **Infrastructure > Hosts**.
2. Hover over **discounts-host** to reveal host action icons.
3. Click the **volume icon** — it now reads **Unmute host alerts**.

All monitor alerts for `discounts-host` are now active again. If CPU usage spikes in the future, the appropriate team will be notified automatically.


##  2. Cloud Network Monitoring (CNM)

###  1. Network Analytics and Flows

1. Navigate to **Infrastructure > Cloud Network > Analytics** in the Datadog sidebar.  
   This **Analytics** page provides deep insights into network health and application performance.

2. One of the core data types shown here is **network flows**. A **network flow** is a recorded communication between any two tagged infrastructure objects—such as:
   - Hosts
   - Services
   - Availability Zones
   - Kubernetes Pods
   - IP Addresses
   - Security Groups, etc.

3. Every network flow includes both a **client** and a **server**:
   - The **client** is the origin of the network connection.
   - The **server** is the destination of that connection.

4. A single object can be a client in one interaction and a server in another.  
   For example:
   - `frontend-service → backend-service`  
     - Client: `frontend-service`  
     - Server: `backend-service`
   - `backend-service → database-service`  
     - Client: `backend-service`  
     - Server: `database-service`

   Each of these communications is tracked as a separate flow.

5. The **Client** and **Server** are shown in the first two columns of the **Network Flows** table.

6. Additional telemetry is available in other columns:
   - **Traffic Volume**
   - **Latency**
   - **TCP Retransmits**
   - And more

7. To customize the view, click the **Customize** button.  
   - Add the **Rtt** column (Round-Trip Time).
   - Then sort the table in **descending order** by Rtt to highlight the flows with the highest latency.

 Insert GIF


##  2. Cloud Network Monitoring (CNM)

###  1. Network Analytics and Flows

1. Navigate to **Infrastructure > Cloud Network > Analytics**.
   This **Analytics** page gives you visibility into the health and performance of network communications between infrastructure components.

2. Datadog tracks **network flows**—communication between two tagged objects (hosts, services, AZs, pods, etc.).
   - A **client** initiates the connection.
   - A **server** receives it.

   > Example: `frontend-service` → `backend-service` → `database-service`.  
   Each step is a separate flow.

3. Each flow includes telemetry:
   - **Traffic Volume**
   - **Latency**
   - **TCP Retransmits**
   - More

4. Use the **Customize** button above the flows table to reveal additional metrics like **Rtt** (Round Trip Time).  
   Sort by **Rtt (descending)** to identify the highest latency flows.

Insert GIF

---

### 2. Explore the Analytics Page

####  Recommended Queries

- Located at the top of the page.
- Highlight recently changed metrics or high-traffic flows.
- Hover for details or click to execute the query.
- The `Search for` field will auto-populate, and graphs + flows will update.

 *Clear `Search for` before proceeding.*

---

####  2.1 Filtering

Filtering lets you focus on flows by tag, region, or role.

1. Try typing `region:us-east-1` into `Search for`.  
   Datadog will auto-correct it to:  
   `(client_region:us-east-1 OR server_region:us-east-1)`

2. Specify client or server explicitly, e.g.:
   - `client_team:backend`
   - `server_team:backend`

---

**Facet Sidebar Filtering**

1. Clear `Search for`.
2. Select the **Client** tab at the top of the sidebar.
3. Under **Infrastructure > Instance Type**, select `n2-standard-2`.
   - This generates: `client_instance-type:n2-standard-2`
4. To exclude agent traffic:
   - In **Client Service**, unselect `agent`.
   - Switch to **Server** tab and unselect `agent` again.
   - Final query:
     ```
     client_instance-type:n2-standard-2 -client_service:agent -server_service:agent
     ```

Insert GIF

---

####  2.2 Grouping

Grouping allows you to understand patterns in how infrastructure communicates.

1. Clear the `Search for` field.

2. Under `View clients as` and `View servers as`, change from:
   - Default: `Auto-grouped traffic`
   - To: `service` or `container_name`

Insert GIF

3. You can group client and server by different tags.

 *Try mixing and matching:*

- `team`
- `port`
- `container_name`
- `env`
- `host`
- `instance-type`
- `ip`
- `ip_type`
- `network.transport`
- `region`
- `service`

This flexibility helps uncover traffic patterns and bottlenecks across layers of infrastructure.


