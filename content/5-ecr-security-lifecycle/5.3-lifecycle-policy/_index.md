---
title: "ECR Image Lifecycle Management"
weight: 3
chapter: false
pre: "<b> 5.3. </b>"
---

Security and cost management are two critical factors when working with Docker images in Amazon ECR. By default, ECR retains all your image versions indefinitely, which can lead to an accumulation of unnecessary old images, increasing storage costs and making the repository cluttered.

ECR Lifecycle Policies allow you to automatically manage these images. You can configure rules to delete images based on age, tag status (tagged/untagged), or image count. This helps you maintain clean ECR repositories, keeping only necessary images and optimizing costs.

#### Objectives of this section

* Understand the importance of lifecycle management for Docker images.
* Create and configure ECR lifecycle policies to automatically clean up old images.
* Understand how lifecycle rules work and their impact on your images.

### Overview of Lifecycle Policies in ECR

A lifecycle policy consists of one or more rules. Each rule defines an action (typically deletion) based on specific criteria. Common criteria include:

* **Age:** Delete images older than a certain number of days.
* **Count:** Retain the N most recent images and delete older ones.
* **Tag Status:** Delete untagged images or images with specific tags.

Rules are enforced according to the Rule Priority you define.

### Configuring Lifecycle Policies in ECR

Now we will create a lifecycle policy for our `fcj-workshop-app` repository.

1.  Ensure you are in the ECR dashboard:
    * In the AWS Console search bar, type "ECR" and select **Elastic Container Registry**.
    {{< figure src="/fcj-ecr-container-registry-workshop/images/5/5.3/001.png" alt="Select fcj-workshop-app repository from the list" >}}

2.  Select the `fcj-workshop-app` Repository:
    * From the list of repositories, click on the name of your `fcj-workshop-app` repository.

3.  Navigate to the "Lifecycle policy" tab:
    * On the `fcj-workshop-app` repository details page, click the **Lifecycle policy** tab.
    {{< figure src="/fcj-ecr-container-registry-workshop/images/5/5.3/002.png" alt="Navigate to Lifecycle policy tab" >}}

4.  Click the "Create rule" button:
    * On the "Lifecycle policy" tab, you will see the **Create rule** button. Click it to start defining rules for the policy.
    {{< figure src="/fcj-ecr-container-registry-workshop/images/5/5.3/003.png" alt="Click Create rule button" >}}

    {{% notice note %}}
Before applying the policy, it is recommended to create and test the rule (test rule) to preview which images will be deleted. This helps you avoid unintended data loss.
    {{% /notice %}}

5.  Configure the first rule: Delete Untagged Images:
    * Add a new rule with the following settings:
        * **Rule priority:** `1` (Highest priority)
        * **Rule description:** `Delete untagged images`
        * **Image status:** `Untagged`
        * **Action:** `Expire images`
        * **Since image pushed**: `1` (day)
    {{< figure src="/fcj-ecr-container-registry-workshop/images/5/5.3/003-a.png" alt="Configure rule to delete untagged images" >}}


7.  Configure Rule 2: Keep the 3 most recent tagged images and delete older ones:
    * Click "Create rule" again and configure the second rule with the following settings:
        * **Rule priority:** `2`
        * **Rule description:** `Keep latest 3 tagged images, delete others`
        * **Image status:** `Tagged`
        * **Specify tags for wildcard matching:** **Enter `*`** (This is a **REQUIRED wildcard character** for the rule to apply to ALL tagged images when using the "Image count greater than" criteria. ECR requires you to enter at least one tag pattern.)
        * **Match criteria:** `Image count greater than`
        * **Value:** `3`
        * **Action:** `Expire images`
    {{< figure src="/fcj-ecr-container-registry-workshop/images/5/5.3/003-b.png" alt="Configure rule to keep latest 3 tagged images" >}}

    {{% notice note %}}
Once all rules for the policy have been defined, you should simulate the policy to preview its impact before officially creating it.
    {{% /notice %}}

8.  Push More Images to the Repository to Test the Policy:
    * To have enough data for the lifecycle policy to operate and for you to see clear simulation results, repeat the steps for pushing images to the ECR repository (as done in Section 4.3. [Run and test Pipeline](/4-ci-cd-with-github-actions/4.3-run-pipeline/)).
    * You should push approximately **5 new images** with different tags (e.g., `v0.0.2`, `v0.0.3`, `v0.0.4`, `v0.0.5`, `v0.0.6`). Ensure each push is a new version to create distinct images.

9.  Simulate policy to preview its impact:
    * After **defining all rules** for the policy, you should run a simulation to see which images will be affected (deleted) before actual application. On the policy creation interface:
    * The simulation results will show a list of images that would be deleted if this policy were executed. This helps you avoid accidentally deleting important images.
    * Go back to your ECR repository and select Lifecycle Policy.
    * Choose Actions.
    * Select copy policy to test rules.
    {{< figure src="/fcj-ecr-container-registry-workshop/images/5/5.3/006.png" alt="Simulate lifecycle policy to preview impact" >}}

10. Select the rule you want to test run:
    * Choose run test.
    {{< figure src="/fcj-ecr-container-registry-workshop/images/5/5.3/007.png" alt="Review and create lifecycle policy" >}}

11. Check the test run:

    {{< figure src="/fcj-ecr-container-registry-workshop/images/5/5.3/008.png" alt="Lifecycle policy successfully created" >}}

Implementing a lifecycle policy helps automate the cleanup process, ensuring that your ECR repository is always optimized and contains only the necessary images.

---