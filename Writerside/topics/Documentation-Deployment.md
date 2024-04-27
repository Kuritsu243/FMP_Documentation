# Documentation Deployment
<show-structure depth="2" />

## Introduction

Writerside works by translating the markdown files for each page to HTML format. In doing so, it is only accessible via a web-server. 
When previewing my documentation, that you are currently reading, it is hosted locally, as shown in the following image:

![Screenshot showing WriterSide running a web-server on localhost](localhost.png){thumbnail="true"}

## Research &amp; Reading Documentation

To build and publish the documentation, WriterSide requires a public web-server if I'd like anyone to have access to this documentation. Luckily, there is a whole section
of WriterSide's documentation that [explains the variety of deployments the software can make use of](https://www.jetbrains.com/help/writerside/build-and-publish.html). The one
that caught my eye was [the page for GitHub.](https://www.jetbrains.com/help/writerside/deploy-docs-to-github-pages.html)
This documentation states that it can automatically be deployed to GitHub and hosted on GitHub Pages through the use of their feature, [actions.](https://github.com/features/actions)

## GitHub actions and workflows

WriterSide provides a YAML format file that can be read by GitHub, to automate the building and deployment process to GitHub pages:

```yaml
name: Build documentation

on:
  push:
    branches:
      - main
      - master
  workflow_dispatch:

permissions:
  id-token: write
  pages: write

env:
  INSTANCE: 'Writerside/hi'
  ARTIFACT: 'webHelpHI2-all.zip'
  DOCKER_VERSION: '233.14938'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Build docs using Writerside Docker builder
        uses: JetBrains/writerside-github-action@v4
        with:
          instance: ${{ env.INSTANCE }}
          artifact: ${{ env.ARTIFACT }}
          docker-version: ${{ env.DOCKER_VERSION }}

      - name: Save artifact with build results
        uses: actions/upload-artifact@v4
        with:
          name: docs
          path: |
            artifacts/${{ env.ARTIFACT }}
            artifacts/report.json
          retention-days: 7
  test:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Download artifacts
        uses: actions/download-artifact@v4
        with:
          name: docs
          path: artifacts

      - name: Test documentation
        uses: JetBrains/writerside-checker-action@v1
        with:
          instance: ${{ env.INSTANCE }}
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    needs: [build, test]
    runs-on: ubuntu-latest
    steps:
      - name: Download artifacts
        uses: actions/download-artifact@v4
        with:
          name: docs

      - name: Unzip artifact
        run: unzip -O UTF-8 -qq '${{ env.ARTIFACT }}' -d dir

      - name: Setup Pages
        uses: actions/configure-pages@v4

      - name: Package and upload Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: dir

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
{collapsible="true" collapsed-title="WriterSide Workflow .YML"}

After setting this up, whenever I push my documentation to GitHub via Git CLI, the following workflow will be run:

![Screenshot showing my past workflow runs on GitHub](github_workflow.png){thumbnail="true"}

This is extremely useful, as I can also track the phases of deployment that the action is currently in:

![Screenshot showing the actions process](actions_build_test_deploy.png){thumbnail="true"}