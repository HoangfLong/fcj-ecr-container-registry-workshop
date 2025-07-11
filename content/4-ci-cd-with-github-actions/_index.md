---
title: "CI/CD with GitHub Actions"
weight: 4
chapter: false
pre: "<b> 4. </b>"
---

In this section, we will set up a Continuous Integration/Continuous Delivery (CI/CD) pipeline using GitHub Actions. This pipeline will automate the process of building Docker images from your source code and pushing them to Amazon ECR whenever changes are pushed to GitHub.

#### Objectives of this section

* Understand how GitHub Actions functions as a CI/CD tool.
* Configure GitHub Repository Secrets to securely store AWS credentials.
* Build a GitHub Actions Workflow to automate the image build and push process to ECR.
* Verify the CI/CD pipeline functions correctly.