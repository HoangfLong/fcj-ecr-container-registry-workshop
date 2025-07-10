---
title: "Prerequisites"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

To begin this workshop, you need to prepare your working environment with the necessary accounts, tools, and resources. This will ensure you can smoothly and effectively complete the labs.

### Preparation Steps

* **Prepare AWS Account and Access Permissions:**
    * Ensure you have an **active AWS account**.
    * Access the AWS Console with an **IAM user possessing administrative permissions** (or an equivalent set of permissions to create and manage workshop resources).

* **Install Local Tools:**
    * **Docker Desktop:** Install Docker Desktop on your personal computer. This tool is essential for building Docker images.
    * **AWS Command Line Interface (AWS CLI):** Install and configure the AWS CLI. This tool allows you to interact with AWS services from the command line and is necessary for some verification steps in the workshop.

* **Create an Amazon ECR Repository:**
    * Create a **new Amazon ECR Repository** in the AWS Console with the name `fcj-workshop-app`. This repository will be where your container images are stored throughout the workshop.

* **Prepare GitHub Repository:**
    * **Clone the sample workshop repository** from GitHub into your personal GitHub account. This repository contains the sample application source code and Dockerfile necessary for the CI/CD section.