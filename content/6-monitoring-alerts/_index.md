---
title: "Monitoring & Alerts"
weight: 6
chapter: false
pre: "<b> 6. </b>"
---

For a comprehensive overview of activities within Amazon ECR and to ensure adherence to security policies, leveraging **AWS CloudTrail** and **Amazon CloudWatch** is essential. These two services work in tandem to provide robust monitoring capabilities, helping you track who did what, when, and where within your registry environment.

In this section, you will learn how to:

1.  **Monitor ECR-related API calls with CloudTrail:** CloudTrail automatically records every API activity performed on your AWS account, including interactions with ECR such as creating/deleting repositories, pushing/pulling images, or modifying policies. This provides a detailed historical record, crucial for security auditing and troubleshooting.
2.  **Analyze CloudTrail logs in CloudWatch Logs:** Logs from CloudTrail are directly sent to Amazon CloudWatch Logs. Here, you can search, filter, and analyze these logs to detect suspicious activities, track configuration changes, or verify compliance with access policies.
3.  **Monitor ECR performance metrics with CloudWatch Metrics:** CloudWatch also automatically collects important metrics about the activity and health of your ECR resources, such as repository storage usage, number of images, and push/pull traffic. Monitoring these metrics helps you understand ECR usage levels, performance, and scalability.

Integrating CloudTrail and CloudWatch will provide you with deep insights into all operational aspects of ECR, from managing access to ensuring compliance with the security policies you have established.