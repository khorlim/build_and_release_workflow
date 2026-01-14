# Example Usage for iOS Build and Upload Workflow

This document explains how to use the reusable `ios_build_and_upload_apphost.yml` workflow.

## Overview

The workflow is a **reusable workflow** that can be called from other workflows. It:
1. Builds an iOS IPA file using Flutter
2. Uploads it to AppHost
3. Sends a Telegram notification with the install link

## Setup Required Secrets

Before using this workflow, you need to configure the following secrets in your GitHub repository:

### 1. GitHub Secrets Setup

Go to your repository → Settings → Secrets and variables → Actions → New repository secret

#### iOS Code Signing Secrets

- **`IOS_CERTIFICATE_P12`**: Base64-encoded .p12 certificate file
  ```bash
  # To encode your certificate:
  base64 -i your_certificate.p12 | pbcopy
  # Then paste the output as the secret value
  ```

- **`IOS_CERTIFICATE_PASSWORD`**: Password for the .p12 certificate

- **`IOS_MOBILEPROVISION`**: Base64-encoded .mobileprovision file
  ```bash
  # To encode your provisioning profile:
  base64 -i your_profile.mobileprovision | pbcopy
  # Then paste the output as the secret value
  ```

#### Telegram Secrets

- **`TELEGRAM_BOT_TOKEN`**: Your Telegram bot token (get from @BotFather)
- **`TELEGRAM_CHAT_ID`**: Your Telegram chat/group ID
- **`TELEGRAM_TOPIC_ID`**: Topic/thread ID (optional, use `0` if not using topics)

#### AppHost Secrets

- **`APPHOST_USER_ID`**: Your AppHost user ID
- **`APPHOST_APP_ID`**: Your AppHost app ID
- **`APPHOST_KEY`**: Your AppHost API key
- **`APPHOST_IOS_BUNDLE_IDENTIFIER`**: Your iOS bundle identifier (e.g., `com.example.myapp`)

## Example Workflow File

Create a workflow file (e.g., `.github/workflows/build_ios.yml`) that calls the reusable workflow:

```yaml
name: Build and Upload iOS

on:
  push:
    branches:
      - main
    paths:
      - 'ios/**'
      - 'lib/**'
      - 'pubspec.yaml'
  
  workflow_dispatch:

jobs:
  build-and-upload:
    uses: ./.github/workflows/ios_build_and_upload_apphost.yml
    secrets:
      SUBMODULE_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      CERTIFICATE_P12: ${{ secrets.IOS_CERTIFICATE_P12 }}
      CERTIFICATE_PASSWORD: ${{ secrets.IOS_CERTIFICATE_PASSWORD }}
      MOBILEPROVISION: ${{ secrets.IOS_MOBILEPROVISION }}
      TELEGRAMBOT_TOKEN: ${{ secrets.TELEGRAM_BOT_TOKEN }}
      TELEGRAM_CHATID: ${{ secrets.TELEGRAM_CHAT_ID }}
      TELEGRAM_TOPICID: ${{ secrets.TELEGRAM_TOPIC_ID }}
      APPHOST_USER_ID: ${{ secrets.APPHOST_USER_ID }}
      APPHOST_APP_ID: ${{ secrets.APPHOST_APP_ID }}
      APPHOST_KEY: ${{ secrets.APPHOST_KEY }}
      APPHOST_IOS_BUNDLE_IDENTIFIER: ${{ secrets.APPHOST_IOS_BUNDLE_IDENTIFIER }}
```

## How It Works

1. **Trigger**: The workflow is triggered by your calling workflow (e.g., on push to main branch)

2. **Build Process**:
   - Checks out the code and submodules
   - Sets up Flutter
   - Installs iOS certificates and provisioning profiles
   - Builds the iOS IPA file

3. **Upload Process**:
   - Extracts version and app name from `pubspec.yaml`
   - Finds the built IPA file
   - Fetches upload URL from AppHost API
   - Uploads the IPA file
   - Gets the install URL

4. **Notification**:
   - Sends a Telegram message with:
     - App name (from `pubspec.yaml`)
     - Version (from `pubspec.yaml`)
     - Install link (from AppHost)

## Requirements

- Your project must have a `pubspec.yaml` file with:
  - `name:` field (for app name)
  - `version:` field (for version)
- Your project must have an `ios/ExportOptions.ci.plist` file for IPA export
- All required secrets must be configured in GitHub

## Notes

- The workflow runs on `macos-latest` runners
- Flutter version is set to `3.32.5` (modify in the workflow if needed)
- The IPA file is built in release mode
- Certificates and provisioning profiles are cleaned up after the build

