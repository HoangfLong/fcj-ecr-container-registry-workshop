---
title: "Clean-up Resources"
weight: 7
chapter: false
pre: "<b> 7. </b>"
---

To avoid incurring unnecessary costs and to keep your AWS environment tidy, follow the steps below to delete all resources created or configured during this workshop.

**Note:** Make sure you're in the **correct AWS Region** where the resources were created.

## 1. Delete ECR Repositories

- Go to **Amazon ECR**.
- Delete the **ECR repositories** you created (e.g., `my-test-repo`, `my-brand-new-test-repo-09-07-2340`).

## 2. Delete CloudWatch Dashboards

- Go to **Amazon CloudWatch**.
- Delete the **CloudWatch dashboard** you created (e.g., `ECR-Operations-Dashboard`).

## 3. Delete CloudTrail Trail

- Go to **AWS CloudTrail**.
- Delete the **Trail** you created or configured (e.g., `ecr-monitor-trail`).

## 4. Delete CloudTrail S3 Bucket

- Go to **Amazon S3**.
- Empty the **S3 bucket** that CloudTrail used to store logs.
- Then **delete the S3 bucket**.


## 5. Delete CloudWatch Log Group

- Go to **CloudWatch Logs**.
- Delete the **Log Group** where CloudTrail sent logs (e.g., `/aws/cloudtrail/ecr-logs` or `aws-cloudtrail-logs-fg`).


## 6. Delete IAM Role (Optional)

- Go to **AWS IAM**.
- Delete the **IAM Role** that CloudTrail used to send logs to CloudWatch Logs (usually named starting with `CloudTrail_CloudWatchLogs_Role`).

---