---
title: "Configure IAM Roles and Policies"
weight: 1
chapter: false
pre: "<b> 3.1. </b>"
---

To ensure our CI/CD pipeline can securely interact with Amazon ECR, we need to configure the appropriate access permissions in AWS IAM. This step involves creating an ECR Repository if one doesn't already exist, then creating an IAM User and assigning the necessary policies.

#### Objectives of this section

* Create the `fcj-workshop-app` ECR Repository if it doesn't already exist.
* Retrieve the ECR Repository URI.
* Create an IAM User with ECR access.
* Attach the necessary IAM policies to the IAM User.

### Create and Retrieve ECR Repository URI

We need an ECR Repository to store our container images. If you haven't created it in the "Preparation" section, follow these steps. Afterward, we will retrieve this repository's URI.

1.  **Log in to the AWS Console:** Go to [https://aws.amazon.com/console/](https://aws.amazon.com/console/) and sign in.

2.  **Navigate to the ECR service:** In the search bar, type "ECR" and select **Elastic Container Registry**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/001.png" alt="Search and select the ECR service in AWS Console">}}

3.  **Check and Create Repository:**
    * In the ECR dashboard, select **Repositories** in the left navigation pane.
    * **If you DO NOT have** a repository named `fcj-workshop-app` yet, follow these steps to create one:
        * Click the **Create repository** button.
        * For **Visibility settings**, choose **Private**.
        * In the **Repository name** field, enter `fcj-workshop-app`.
        * Click the **Create repository** button at the bottom of the page.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/002.png" alt="Create new ECR Repository">}}

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/0002.png" alt="Create ECR Repository">}}

    * **If you ALREADY HAVE** the `fcj-workshop-app` repository from the preparation section, proceed to the next step.

4.  **Select your Repository and Copy the URI:**
    * From the list of repositories, find and click on the `fcj-workshop-app` repository name.
    * On the repository details page, you will see the repository **URI**. Copy this URI. It will be similar to: `123456789012.dkr.ecr.ap-southeast-1.amazonaws.com/fcj-workshop-app` (where `123456789012` is your AWS account ID and `ap-southeast-1` is your Region).

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/003.png" alt="Select fcj-workshop-app repository and copy URI" >}}

    {{% notice note %}}
    Keep this URI handy. We will need to use it in later GitHub Actions configuration steps to specify the repository where the image will be pushed.
    {{% /notice %}}

### Create IAM Policy for ECR Access

Instead of using a pre-existing policy, we will create a custom IAM Policy via the console to gain more granular control over the permissions the CI/CD user will have on ECR.

5.  **Navigate to the IAM service:** In the AWS Console search bar, type "IAM" and select **IAM**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/004.png" alt="Search and select the IAM service in AWS Console">}}

6.  **Create a new Policy:**
    * In the IAM dashboard, select **Policies** in the left navigation pane.
    * Click the **Create policy** button.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/005.png" alt="IAM dashboard and Create policy button">}}

7.  **Choose service and actions:**
    * On the **Specify permissions** page, select the **Visual editor** tab.
    * For **Service**, search for and select **Elastic Container Registry**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/011.png" alt="Select ECR service in Visual Editor">}}

    * In the **Actions allowed** section, expand **Write** and select the following actions:
        * `CompleteLayerUpload`
        * `InitiateLayerUpload`
        * `PutImage`
        * `UploadLayerPart`
    * Expand **Read** and select the following actions:
        * `BatchCheckLayerAvailability`
        * `BatchGetImage`
        * `DescribeImages`
        * `GetDownloadUrlForLayer`
    * In the additional **Access level** section, find and select the `GetAuthorizationToken` action.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/012.png" alt="Select necessary ECR actions">}}

    * In the **Resources** section, choose **Specific**.
    * For **repository**, click **Add ARN**.
    * Enter the following information:
        * **Region:** Select your AWS Region (e.g., `ap-southeast-1`).
        * **Account ID:** Enter your AWS account ID.
        * **Repository name:** Enter `fcj-workshop-app`.
    * Click **Add ARN**.
    * For the `GetAuthorizationToken` resource (if this option is separate), leave it as **Any** or add a separate line for this action with `Resource: *` if necessary. (Typically, `GetAuthorizationToken` is not restricted to a specific repository resource but rather by account/region).

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/013.png" alt="Configure specific resources for ECR">}}

    {{% notice note %}}
    The selected permissions (`ecr:*` for specific actions) allow the user to perform image push and pull operations.
    `ecr:GetAuthorizationToken`: Allows the user to obtain an authentication token to log Docker into ECR.
    `Resource`: We specify that this policy applies only to your `fcj-workshop-app` repository, adhering to the principle of least privilege.
    {{% /notice %}}

8.  **Add Tags (Optional):** You can add tags for easier management. Click **Next: Review policy**.

9.  **Review and Create Policy:**
    * For **Policy name**, enter `fcj-ecr-ci-cd-policy`.
    * (Optional) Enter a **Description**. Enter `policy for CI-CD`.
    * Click the **Create policy** button.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/015.png" alt="Name and review IAM Policy">}}

    {{% notice tip %}}
    Creating a custom policy like this allows you more precise control over the permissions granted to a user or role. This is a better practice than assigning broader managed policies for CI/CD purposes.
    {{% /notice %}}

### Create IAM User and Attach Policy for CI/CD

Now we will create an IAM User and attach the custom policy `fcj-ecr-ci-cd-policy` we just created to this user.

10. **Navigate to the IAM service:** Ensure you are in the IAM dashboard.

11. **Create a new User:**
    * In the IAM dashboard, select **Users** in the left navigation pane.
    * Click the **Create user** button.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/016.png" alt="IAM dashboard and Create user button">}}

    * For **User name**, enter `fcj-ecr-ci-cd-user`.
    * Choose **Provide user access to the AWS Management Console** for the CI/CD user.
    * Click **Next**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/017.png" alt="Set up user name and access options" >}}

12. **Set permissions:**
    * On the **Set permissions** page, select **Attach policies directly**.
    * In the search box, type `fcj-ecr-ci-cd-policy` (the name of the policy you just created) and select this policy.
    * Click **Next**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/018.png" alt="Attach custom policy fcj-ecr-ci-cd-policy">}}

13. **Review and Create User:**
    * On the **Review and create** page, review the settings.
    * Click the **Create user** button.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/019.png" alt="Review information and create user">}}

14. **Save Access Key:**
    * After the user is created, you will see a success message. Click **View user** or go back to the user list and click on the `fcj-ecr-ci-cd-user` you just created.
    * Navigate to the **Security credentials** tab.
    * In the **Access keys** section, click **Create access key**.
    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/020.png" alt="Create Access Key for IAM User">}}
15. Choose **Third-party service** as the use case.
    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/021.png" alt="Save Access Key ID and Secret Access Key" >}}
    * Check the confirmation box and click **Next**.
16. (Optional) Enter a description for the Access Key (e.g., `for-github-actions`).
    * Click **Done**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/022.png" alt="Access Key ID and Secret Access Key" >}}

    {{% notice warning %}}
**The Secret Access Key is displayed ONLY ONCE immediately after creation.** Make sure you copy both the **Access Key ID** and **Secret Access Key** and store them in a secure place. You will not be able to view this Secret Access Key again after closing this window. We will need to use them to configure GitHub Actions in later sections.
    {{% /notice %}}

    {{% notice tip %}}
In a real production environment, using an IAM Role with OpenID Connect (OIDC) is the more recommended method for granting permissions to CI/CD. This approach avoids the need to store long-lived Access Keys, enhancing security. However, for simplicity in this workshop, we will use Access Keys.
    {{% /notice %}}

---