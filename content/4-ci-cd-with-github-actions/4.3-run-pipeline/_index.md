---
title: "Run and Test the Pipeline"
weight: 4
chapter: false
pre: "<b> 4.3. </b>"
---

Now that you have configured GitHub Repository Secrets and understood the GitHub Actions Workflow, we will trigger the CI/CD pipeline to verify that everything is working as expected.

#### Objectives of this section

* Trigger the CI/CD pipeline by pushing changes to GitHub.
* Monitor the workflow run on the GitHub Actions interface.
* Verify the results: check for the newly created Git tag and the Docker image on Amazon ECR.

### Triggering the CI/CD Pipeline

Your CI/CD pipeline is configured to automatically run every time code is pushed to the `main` branch. To trigger the pipeline, we will make a small change and push it to the repository.

1.  **Make a small change:**
    * Open the `aws-container-security-workshop` project folder on your local machine.
    * Open the file `fcj\src\static\js\app.js` (or any other file, e.g., add a comment to a source code file).
    * Add the text `v1.0.0` to line **56** in the `src\static\js\app.js` file.
    * Save the file.
    {{< figure src="/fcj-ecr-container-registry-workshop/images/4/4.3/0011.png" alt="Access folder" >}}

2.  **Push changes to GitHub:**
    * Open Terminal/Command Prompt (ensure you are in the project directory).
    * Add the changes to the staging area:
        ```bash
        git add .
        ```
    * Create a commit with a clear message:
        ```bash
        git commit -m "Trigger CI/CD pipeline for testing"
        ```
    * Push this commit to the `main` branch of your GitHub repository:
        ```bash
        git push origin main
        ```

    {{% notice note %}}
Immediately after you push code to the `main` branch, GitHub Actions will automatically detect the change and start running the `CI/CD Pipeline` workflow you configured.
    {{% /notice %}}

### Monitoring the Pipeline Run

Now, navigate to GitHub and monitor the pipeline's progress.

3.  **Access GitHub Actions:**
    * Open your browser and navigate to your repository on GitHub.
    * Click on the **Actions** tab.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/4/4.3/001.png" alt="Access Actions tab on GitHub" >}}
4.  **View Workflow Status:**
    * You will see a workflow named `CI/CD Pipeline` with an `In progress` or `Queued` status.
    * Click on the name of the workflow that just ran (based on the commit message you just created) to view details.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/4/4.3/002.png" alt="List of workflow runs on GitHub Actions" >}}

5.  **Check Job Steps:**
    * On the workflow details page, you will see the `build-and-push` job. Click on it to see the specific steps being executed.
    * Monitor the output logs of each step to ensure they complete successfully. You will see steps like `checkout`, `configure aws credentials`, `login to amazon ECR`, `Automatic Tagging of Releases`, and `build, tag, and push the image to ECR` running sequentially.
    * Ensure that all steps have a green checkmark (success). If any step fails, review the logs to identify the cause of the error.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/4/4.3/003.png" alt="Details of steps in a GitHub Actions job" >}}
    {{< figure src="/fcj-ecr-container-registry-workshop/images/4/4.3/003-b.png" alt="Details of steps in a GitHub Actions job" >}}

### Verifying Pipeline Results

After the workflow successfully completes, we need to verify that the expected results have been generated.

6.  **Check New Git Tag on GitHub:**
    * Return to the main page of your repository on GitHub.
    * Click on **"Tags"** or **"Releases"** (usually located in the right sidebar or under the "Code" section).
    * You should see a new Git tag created with an incremented version (e.g., `v0.0.1