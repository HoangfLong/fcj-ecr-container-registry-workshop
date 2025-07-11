---
title: "GitHub Actions Workflow"
weight: 2
chapter: false
pre: "<b> 4.2. </b>"
---

In this section, we will **examine and analyze the architecture of a GitHub Actions Workflow file** used to automate the process of building Docker images and pushing them to Amazon ECR when changes are made to the `main` branch.

#### Objectives of this section

* Understand the basic structure of a GitHub Actions Workflow.
* Grasp the steps for authenticating with AWS, logging into ECR, building, and pushing images.
* Learn how the workflow leverages configured GitHub Secrets for secure credential handling.

### The Main Workflow File: main.yml

The **main.yml** file is located in the **.github/workflows/** directory of the repository. This is where your entire CI/CD flow is defined.

1.  Content of the **.github/workflows/main.yml** file:

    ```yaml
    name: CI/CD Pipeline

    on:
      push:
        branches: ["main"]

    permissions:
      contents: write

    jobs:
      build-and-push:
        name: build and push to ECR
        runs-on: ubuntu-latest
        steps:
        - name: checkout
          uses: actions/checkout@v4
          with:
              fetch-depth: 0

        - name: configure aws credentials
          uses: aws-actions/configure-aws-credentials@v1
          with:
            aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
            aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
            aws-region: ${{ secrets.AWS_REGION }}
            
        - name: login to amazon ECR
          id: login-ecr
          uses: aws-actions/amazon-ecr-login@v1

        - name: Automatic Tagging of Releases
          id: increment-git-tag
          run: |
            bash ./build/git_update.sh -v patch

        - name: build, tag, and push the image to ECR
          id: build-image
          env:
            ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
            ECR_REPOSITORY: ${{ secrets.ECR_REPOSITORY_URI }}
            IMAGE_TAG: ${{ steps.increment-git-tag.outputs.git-tag }}
          run: |
            docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .
            docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
    ```

#### Workflow Explanation

* **on: push**: The workflow will execute every time code is pushed to the **main** branch.
* **permissions: contents: write**: This permission is required for the workflow to create and push tags to the repository.
* **configure aws credentials**: Uses the **aws-actions/configure-aws-credentials@v1** action to configure AWS credentials from GitHub Secrets.
* **login to amazon ECR**: Uses the **aws-actions/amazon-ecr-login@v1** action to log Docker into ECR.
* **Automatic Tagging of Releases**: Runs the **git_update.sh** script to automatically increment the patch version and create a new Git tag.
* **build, tag, and push the image to ECR**: Builds the Docker image, tags it with the new version, and pushes the image to ECR.

### Script: git_update.sh

The workflow file above calls a shell script named `git_update.sh` located in the `build/` directory of the repository. This script is responsible for automatically versioning the application by creating new Git tags (e.g., v1.0.0, v1.0.1, v1.0.2).

    VERSION=""

    while getopts v: flag
    do
        case "${flag}" in
            v) VERSION=${OPTARG};;
        esac
    done

    git fetch --tags --prune --unshallow 2>/dev/null
    CURRENT_VERSION=$(git describe --abbrev=0 --tags 2>/dev/null)

    if [[ "$CURRENT_VERSION" == "" ]]; then
        CURRENT_VERSION="v0.0.0"
    fi

    echo "Current Version: $CURRENT_VERSION"

    NUM_PART=${CURRENT_VERSION#v}
    CURRENT_VERSION_PARTS=(${NUM_PART//./ })

    VNUM1=${CURRENT_VERSION_PARTS[0]}
    VNUM2=${CURRENT_VERSION_PARTS[1]}
    VNUM3=${CURRENT_VERSION_PARTS[2]}

    if [[ $VERSION == 'major' ]]; then
        VNUM1=$((VNUM1+1))
        VNUM2=0
        VNUM3=0
    elif [[ $VERSION == 'minor' ]]; then
        VNUM2=$((VNUM2+1))
        VNUM3=0
    elif [[ $VERSION == 'patch' ]]; then
        VNUM3=$((VNUM3+1))
    else
        echo "Invalid version type. Use: -v [major|minor|patch]"
        exit 1
    fi

    NEW_TAG="v$VNUM1.$VNUM2.$VNUM3"
    echo "($VERSION) updating $CURRENT_VERSION to $NEW_TAG"

    GIT_COMMIT=$(git rev-parse HEAD)
    NEEDS_TAG=$(git describe --contains $GIT_COMMIT 2>/dev/null)

    if [ -z "$NEEDS_TAG" ]; then
        echo "Tagged with $NEW_TAG"
        git tag $NEW_TAG
        git push origin $NEW_TAG
    else
        echo "Already a tag on this commit"
    fi

    echo "git-tag=$NEW_TAG" >> $GITHUB_OUTPUT
    exit 0

#### Script Explanation

* **Fetch current Git tag version**: Uses the `git describe` command to check the most recent tag in the repository.
* **Increment major, minor, or patch version**: Based on the parameter passed from the workflow (e.g., `-v patch`, `-v minor`, or `-v major`).
* **Create new Git tag**: Attaches the new tag (e.g., `v1.2.3`) and pushes it to GitHub using the `git push origin` command.
* **Export tag value to output**: Allows subsequent steps in GitHub Actions to utilize this tag (e.g., for the `IMAGE_TAG` when building the Docker image).

---