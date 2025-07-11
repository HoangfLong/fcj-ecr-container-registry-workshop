---
title: "GitHub Actions Workflow"
weight: 2
chapter: false
pre: "<b> 4.2. </b>"
---

Trong phần này, chúng ta sẽ **xem xét và phân tích kiến trúc của một file GitHub Actions Workflow** được sử dụng để tự động hóa quá trình build Docker image và đẩy lên Amazon ECR khi có thay đổi trên nhánh `main`.

#### Mục tiêu của phần này

* Hiểu cấu trúc cơ bản của một GitHub Actions Workflow.
* Nắm được các bước để xác thực với AWS, đăng nhập ECR, build và push image.
* Cách workflow sử dụng các GitHub Secrets đã cấu hình để bảo mật thông tin xác thực.

### File Workflow chính: main.yml

File **main.yml** nằm trong thư mục **.github/workflows/** của repository. Đây là nơi định nghĩa toàn bộ luồng CI/CD của bạn.


1.  Nội dung file **.github/workflows/main.yml:**
    
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

#### Giải thích Workflow

- **on: push**: Workflow sẽ chạy mỗi khi có code được đẩy lên nhánh **main**.
- **permissions: contents: write**: Cần quyền này để workflow có thể tạo tag và push tag lên repository.
- **configure aws credentials**: Sử dụng action **aws-actions/configure-aws-credentials@v1** để cấu hình thông tin xác thực AWS từ các GitHub Secrets.
- **login to amazon ECR**: Sử dụng action **aws-actions/amazon-ecr-login@v1** để đăng nhập Docker vào ECR.
- **Automatic Tagging of Releases**: Chạy script **git_update.sh** để tự động tăng phiên bản tag (patch) và tạo tag Git mới.
- **build, tag, and push the image to ECR**: Xây dựng Docker image, gắn tag với phiên bản mới và đẩy image lên ECR.

### Script: git_update.sh

File workflow trên gọi một script shell có tên git_update.sh nằm trong thư mục build/ của repository. Script này chịu trách nhiệm tự động tăng phiên bản (versioning) của ứng dụng bằng cách tạo các Git tag mới (ví dụ: v1.0.0, v1.0.1, v1.0.2).

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

#### Giải thích Script

- **Lấy phiên bản tag hiện tại từ Git**: Sử dụng lệnh **git describe** để kiểm tra tag gần nhất trong repository.
- **Tăng phiên bản major, minor hoặc patch**: Dựa trên tham số truyền vào từ workflow (ví dụ: **-v patch**, **-v minor**, hoặc **-v major**).
- **Tạo Git tag mới**: Gắn tag mới (ví dụ: **v1.2.3**) và đẩy tag đó lên GitHub bằng lệnh **git push origin**.
- **Xuất giá trị tag ra output**: Cho phép các bước trong GitHub Actions sử dụng tag này (ví dụ: dùng cho **IMAGE_TAG** khi build Docker image).

---