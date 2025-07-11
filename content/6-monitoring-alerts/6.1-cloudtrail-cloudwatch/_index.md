---
title: "CloudTrail and CloudWatch"
weight: 1
chapter: false
pre: "<b> 6.1. </b>"
---

For a comprehensive overview of activities within Amazon ECR and to ensure adherence to security policies, leveraging **AWS CloudTrail** and **Amazon CloudWatch** is essential. These two services work together to provide robust monitoring capabilities, helping you track who did what, when, and where within your registry environment.

In this section, you'll learn how to:

* Ensure CloudTrail records ECR activity and sends logs to CloudWatch Logs.
* Monitor ECR-related API calls.
* Analyze logs and monitor ECR performance metrics.

---

### Setting up CloudTrail to monitor ECR

1.  Log in and access CloudTrail:
    * Log in to the [AWS Management Console](https://aws.amazon.com/console/).
    * In the search bar, type `CloudTrail` and select the **CloudTrail** service from the results.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/001.png" alt="Search for and select the CloudTrail service" >}}

2.  Check Trail status and decide action:
    * In the CloudTrail dashboard, select **"Trails"** from the left menu.
    * Check if you already have an active Trail that is sending logs to CloudWatch Logs.
        * **If you need to create a new Trail or configure an existing one:** Proceed to **Step 3**.
        * **If the Trail is already correctly configured:** You can skip the Trail configuration steps and go directly to **Step 9**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/002.png" alt="Check the list of Trails in CloudTrail" >}}

3.  Create or edit a Trail to send logs to CloudWatch Logs:
    * **If creating a new Trail:** Click on **"Create trail"**.
    * **If editing an existing Trail:** Click on the Trail's name, then find the **"CloudWatch Logs"** section and click **"Edit"**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/003.png" alt="Start creating or editing a Trail" >}}

4.  Configure Trail details (if creating new):
    * **Trail name:** Give your Trail a name (e.g., `ecr-monitor-trail`).
    * **Storage location:** Choose **"Create new S3 bucket"** and give it a name (or use an existing bucket).
    * **CloudWatch Logs:** **Select "Enabled"**. Choose **"New"** to create a new Log group (e.g., `/aws/cloudtrail/ecr-logs`) or select an existing Log group.
    * (Keep other options as default or skip them).

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/004.png" alt="Configure Trail name, S3 bucket, and CloudWatch Logs" >}}

5.  Configure Trail details (continued):
    * Click **"Next"**.
    * In the **"Choose log events"** section, ensure **"Management events"** is selected.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/005.png" alt="Select 'Management events' for the Trail" >}}

6.  Complete Trail creation:
    * Click **"Next"**.
    * Click **"Create trail"** (if creating new) or **"Save changes"** (if editing).

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/006.png" alt="Complete creating or saving Trail configuration" >}}

---

### Viewing and analyzing ECR logs

7.  Access CloudTrail Event History:
    * In the CloudTrail dashboard, select **"Event history"** from the left menu.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/007.png" alt="Select 'Event history' in CloudTrail" >}}

8.  Filter events by ECR:
    * Click on the filter **"Attribute"**, select **"Event source"**.
    * In the field next to it, type `ecr.amazonaws.com` and press `Enter`.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/008.png" alt="Filter CloudTrail event history by ECR" >}}

9.  Access Log groups in CloudWatch:
    * In the AWS Console search bar, type `CloudWatch` and select the **CloudWatch** service.
    * In the CloudWatch dashboard, select **"Log groups"** under **"Logs"**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/009.png" alt="Access Log groups in CloudWatch" >}}

10. Find the CloudTrail Log group:
    * In the Log group list, find the Log group that CloudTrail is sending logs to (configured in Step 4).
    * Click on the name of that Log group to view the log streams.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/010.png" alt="Select the CloudTrail Log group in CloudWatch Logs" >}}


### Monitoring metrics and creating Dashboards

11. Monitor ECR metrics in CloudWatch Metrics:
    * In the CloudWatch dashboard, select **"Metrics"** from the left menu.
    * In the "All metrics" section, select the namespace **"ECR"** or **"AWS/ECR"**.
    * Choose the ECR metrics you want to view (e.g., `RepositorySize`, `ImageCount`).

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/012.png" alt="View ECR metrics in CloudWatch Metrics" >}}

12. Create a custom Dashboard for ECR (Optional):
    * In the CloudWatch dashboard, select **"Dashboards"**.
    * Click **"Create dashboard"**, name it (ex: `ECR-Operations-Dashboard`), and click **"Create dashboard"**.
    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/014.png" alt="Create custom Dashboard for ECR" >}}

13. Add widgets.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/015.png" alt="Add widgets to Dashboard" >}}

14. Select the metric to add.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/015.png" alt="Select metric for Dashboard" >}}

15. Select ECR.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/016-a.png" alt="Select ECR namespace for Dashboard" >}}

16. Choose your ECR repository.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/016.png" alt="Select ECR repository for Dashboard" >}}

17. Select create widget.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/017.png" alt="Create widget for Dashboard" >}}

    {{< figure src="/fcj-ecr-container-registry-workshop/images/6/6.1/018.png" alt="Final ECR Dashboard view" >}}

---

By setting up AWS CloudTrail to record ECR events and send them to Amazon CloudWatch Logs, you've created a robust monitoring system. Now, you can easily track API activities in ECR, analyze detailed logs using CloudWatch Logs Insights, and visualize key ECR performance metrics through custom CloudWatch Dashboards. This allows you to not only understand who did what and when in your image repositories, but also proactively monitor ECR's status and performance, thereby enhancing both security and operational efficiency.