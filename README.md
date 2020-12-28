# Opspresso Builder

[![GitHub Actions status](https://github.com/opspresso/action-builder/workflows/Build-Push/badge.svg)](https://github.com/opspresso/action-builder/actions)
[![GitHub Releases](https://img.shields.io/github/release/opspresso/action-builder.svg)](https://github.com/opspresso/action-builder/releases)

## Usage

```yaml
name: Opspresso Builder

on: push

jobs:
  builder:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@master
        with:
          fetch-depth: 1

      - name: Bump Version
        uses: opspresso/action-builder@master
        with:
          args: --version

      - name: Commit & Push to GitHub
        uses: opspresso/action-builder@master
        with:
          args: --commit
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          MESSAGE_PATH: ./target/commit_message

      - name: Publish to AWS S3
        uses: opspresso/action-builder@master
        with:
          args: --publish
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: "us-east-1"
          FROM_PATH: "./target/publish"
          DEST_PATH: "s3://your_bucket_name/path/"
          OPTIONS: "--acl public-read"

      - name: Release to GitHub
        uses: opspresso/action-builder@master
        with:
          args: --release
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          TARGET_COMMITISH: main
          TAG_NAME: "v0.0.1"

      - name: Docker Build & Push to Docker Hub
        uses: opspresso/action-builder@master
        with:
          args: --docker
        env:
          USERNAME: ${{ secrets.DOCKER_USERNAME }}
          PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
          DOCKERFILE: "Dockerfile"
          IMAGE_NAME: "USERNAME/IMAGE_NAME"
          TAG_NAME: "v0.0.1"
          LATEST: "true"

      - name: Docker Build & Push to GitHub Package
        uses: opspresso/action-builder@master
        with:
          args: --docker
        env:
          USERNAME: ${{ secrets.GITHUB_USERNAME }}
          PASSWORD: ${{ secrets.GH_PERSONAL_TOKEN }}
          REGISTRY: "docker.pkg.github.com"
          DOCKERFILE: "Dockerfile"
          IMAGE_NAME: "IMAGE_NAME"
          TAG_NAME: "v0.0.1"
          LATEST: "true"

      - name: Docker Build & Push to AWS ECR
        uses: opspresso/action-builder@master
        with:
          args: --ecr
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          IMAGE_URI: "xxxx.dkr.ecr.us-east-1.amazonaws.com/IMAGE_NAME"
          DOCKERFILE: "Dockerfile.aws"
          TAG_NAME: "v0.0.1"
          LATEST: "true"

      - name: Post to Slack
        uses: opspresso/action-builder@master
        with:
          args: --slack
        env:
          SLACK_TOKEN: ${{ secrets.SLACK_TOKEN }}
          JSON_PATH: ./target/slack_message.json
```

## More details

* <https://github.com/marketplace/actions/aws-s3-sync>
* <https://github.com/marketplace/actions/release-to-github>
* <https://github.com/marketplace/actions/docker-push>
