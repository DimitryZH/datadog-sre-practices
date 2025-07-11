# Incident response workflow in Datadog: detect, investigate, and resolve a latency issue

##  Overview

Our e-commerce web application is experiencing performance issues — the home page is slow to load, and instead of the expected content, users are seeing a prolonged **“Please wait”** screen. Clearly, something is wrong.

We were first alerted by a Datadog monitor configured to track **average request latency** on the home page, measured using APM (Application Performance Monitoring). This monitor is set to trigger when the **average latency exceeds 3 seconds over a one-minute window**.

-  **Monitor Configuration:**  
  APM-based monitor targeting `store-frontend` latency  
  **Threshold:** Average latency > 3s (past 1 minute) 


<img width="1318" height="863" alt="store-frontend-average-latency-monitor" src="https://github.com/user-attachments/assets/e21f1dd8-903a-4709-bb68-3c62de5c9dea" />

---
After receiving the alert, we opened the application in a browser to validate the problem and experienced the same delay. The app appeared stuck on the **loading screen**, failing to render the homepage.  

---
<img width="1399" height="540" alt="please-wait-page-load" src="https://github.com/user-attachments/assets/cb4721f7-bfc8-4c3b-9e09-13d92d215e73" />

##  Objective

In this work, we used **APM and Datadog Incident Management tools** to:

- Investigate the root cause of the performance degradation
- Track the issue across services and dependencies
- Use traces and logs to pinpoint the failure
- Walk through managing, documenting, and resolving the incident using Datadog’s integrated tools

---


## 1. Declaring and Classifying the Incident

In this section, we responded to an alert, declared an incident, and began adding context to better understand the impact on users and the business.

---

### Step 1: Responding to an Alert

We started by reviewing the alert that notified us about the performance degradation:

1. Opened the monitor **`store-frontend average latency monitor`**  
   - This monitor is currently in the **ALERT** state.
   - It checks whether the **average latency over the past 1 minute** exceeds **3 seconds**.

2. Upon inspection:
   - The monitor **query** confirms it's tracking average latency above the threshold.
   - The **message** warns that the store-frontend is experiencing **high latency**, which aligns with the issues observed by users loading the app.

---

###  Step 2: Declaring an Incident

In Datadog, incidents can be declared from various locations:
- Clipboard (top navigation)
- Sidebar navigation
- Directly from monitors
- From a dashboard widget

We declared this incident **from the monitor itself**:

1. Clicked **Actions** in the top-right corner of the alert.
2. Selected **Declare Incident**.
3. In the **Declare Incident** modal, we filled in:
   - **Title**: `Latency Issue on Homepage`
   - **Severity**: *Left as Unknown* for now
   - **Incident Commander**: Our own username (or relevant responder)
   - **Attributes**: The tag `service:store-frontend` was auto-suggested from the monitor’s scope

4. Clicked **Declare Incident** to open the Incident Overview.

---

###  Step 3: Adding Context

From the **Incident Overview** page (also accessible via **Service Mgmt > Incidents**), we added more meaningful details:

1. Under **What happened**, set:
   - **Detection Method**: `Monitor`
   - **Impact** then **+ Add**:  
     `Customers are experiencing long loading times for our site.`

> _Note:_ We can manually adjust the **Impact Start Time** if your investigation shows the problem started earlier. For this case, we kept the default.

2. Clicked **Save**.

3. Upon saving, we were prompted to **set the severity level**:
   - Set **Severity** to `SEV-2 (Major issue affecting customers and users)`


![declaring_classifying_incident](https://github.com/user-attachments/assets/8d07cd28-9233-43bd-b3e3-995f11c4b760)

---

> **Tip:** We can customize severity levels for your organization in **Incident Management > Settings**.


> At this point, we have **successfully declared the incident** and added contextual details to begin deeper investigation and resolution.

Next, we'll dive into the root cause and trace the issue through APM.

## 2. Investigating the Incident
Now that the incident has been declared and acknowledged, the next step is to investigate the root cause and begin the remediation process. Using Datadog's APM and Incident Management features, we traced the issue to a specific backend service impacting the app's performance.

###  Step 1: Add an Initial Follow-up Task

1. On the **Incident Overview** page, click the **Remediation** tab.
2. Under **Incident Follow-Ups**, type the task: `Look at app traces` 
3. Click **Create Task**.
4. Click **Assign To** and assign it to a team member.

###  Step 2: Review Related Traces

1. Navigate to **Monitors > Monitor List** and click on the `store-frontend average latency monitor`.
2. Under **Monitor behavior**, set the time range to **Past 30 minutes**.
3. In the graph panel, under **Visualize as**, select **Source Data**.
4. Click on the graph to open the context menu and select **View Related Traces**.
5. In the Traces list view, change the time range to **Past 15 Minutes**.
6. Open a trace and examine the **% Exec Time** column.
   - We noticed that the **advertisements-service** is responsible for a significant portion of the execution time.


![Investigating_incident](https://github.com/user-attachments/assets/9fb8f06a-8e09-4d6a-aa67-e6b73ebdd636)

---

### Step 3: Add the Trace to the Incident

1. Click **Open Full Page** in the upper-right corner of the trace panel.
2. Press **Cmd/Ctrl + Shift + K** to open the **Datadog Clipboard**.
3. Click **Add current page**, then **Export item to…**
4. From the drop-down, select your incident and click **Export**.
   
<img width="1764" height="913" alt="export-trace-to-incident" src="https://github.com/user-attachments/assets/d5a2f7cf-3dfd-475c-8eb3-7754dc0f02a3" />

---

###  Step 4: Complete the Task and Add a New One

1. Return to **Service Mgmt > Incidents List** and select the incident.
2. Under the **Remediation** tab, check off the task **Look at app traces**.
3. Add a new task: `Investigate ad service code `
4. Under the **Overview** tab, update the **Services** attribute to also include `advertisements-service` and `store-frontend`.

![complete_task_add_new_one](https://github.com/user-attachments/assets/568dae5b-f6d7-4182-a778-5ff91a57d8e9)

---

###  Step 5: Fix and Close the Investigation

The issues in the `advertisements-service` and `store-frontend`code were identified and resolved (outside Datadog). Now reflect that in the incident timeline:

1. Back in the **Remediation** tab, check off the task **Investigate ad service code** as complete.

### Result

> After the fix was deployed, we verified that the homepage now loads significantly faster.  
The latency alert has cleared, and user experience has returned to normal.



## 3. Resolving the Incident

We now have the necessary context to state the **root cause** and officially resolve the incident.



### Step 1: Document the Root Cause

1. Navigate back to your **Incident Overview** page in Datadog.
2. Scroll to the **"Why it happened"** section.
3. Add the following root cause explanation: `Debug statements left in the ad service code and an uncaught N+1 query. `
4. Click the  **check button** to save your update.


###  Step 2: Confirm Monitor Recovery

1. In Datadog, go to **Monitors > Monitor List**, and locate the monitor:  
**store-frontend average latency monitor**
2. Wait for the monitor to turn **green (OK status)**, indicating that latency has returned to acceptable levels.
- You may need to **refresh the page** after a few minutes.

###  Step 3: Resolve the Incident

1. Once the monitor remains in **OK status**, return to the **Incident page**.
2. Change the **incident status** from `ACTIVE` to `RESOLVED`.


![resolving_incident](https://github.com/user-attachments/assets/5222f8bf-5f4c-40b0-a6f7-3918b66362d2)

---

> The incident is now officially closed.


## 4. Generating a Postmortem

After resolving an incident, it’s essential to reflect on what happened and ensure the organization can learn from the event. Datadog simplifies this process by automatically generating a postmortem document.

---

###  Step 1: Generate the Postmortem Notebook

1. Once your incident is marked as **RESOLVED**, a **Generate Postmortem** button appears at the top of the incident page.
2. Click **Generate Postmortem** to open the **Generate a Notebook Postmortem** modal.
3. Use the **Datadog Template** option. This automatically populates a Notebook with:
   - A complete **incident timeline**
   - **Links to dashboards, monitors, traces**, and other Datadog resources referenced during the incident
4. Click **Generate**.

![generate_postmortem](https://github.com/user-attachments/assets/13cdb7e0-3927-4e25-b56c-792f38687f77)

---

###  Step 2: Review and Enrich the Postmortem

The generated **Postmortem Notebook** contains timestamped entries and linked resources.

- Review the notebook.
- Add any missing context, technical insights, or next steps that Datadog didn’t capture automatically.

---

###  Results 

Based on this incident, we can consider the following **follow-up actions**:

-  Formalize a process for **managing debug statements**, e.g., using tags like `// DEBUG-START` and `// DEBUG-END`.
-  Add a **latency regression test** for the `advertisements-service` in CI/CD.
-  Review the **effectiveness of the monitor**:
  - Was the detection fast enough?
  - Are the thresholds and alerting conditions ideal?
  - Consider refining or adding **new monitors**.

---

###  Accessing Postmortems Later

- Navigate to: **Dashboards > Notebooks > Notebook List**
- Filter by **Notebook Type → Postmortem** to find past incident reviews.
- Each postmortem can be **downloaded** as PDF or Markdown.


Our generated postmortem notebook:  
**`postmortem-ir-1-latency-issue-on-homepage.md`**

