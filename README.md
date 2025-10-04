# GitHub Actions Workflows

This repository contains GitHub Actions workflows for automating various tasks related to Flutter and web projects.

## Workflows

| 🚀 **Workflow Name**                | 📂 **File**                              | 📝 **Description**                                      |
|------------------------------------|------------------------------------------|--------------------------------------------------------|
| 🦋 **Flutter Apk Build On Push**   | `flutter/build-app-artifacts.yaml`       | Builds Flutter Android APKs and App Bundles.          |
| 📱 **Google Play Production Release** | `flutter/prod-release-to-stores.yaml`    | Builds and releases a Flutter app to Google Play.     |
| 🌐 **Auto Release**                | `web/simple-web-to-github-pages.yml`     | Deploys a web project to GitHub Pages.                |

### Flutter Workflows

#### 1. Flutter Apk Build On Push
- **File**: `flutter/build-app-artifacts.yaml`
- **Description**: Builds Flutter Android APKs and App Bundles on push or manual trigger.
- **Key Features**:
  - Caches dependencies and Android SDK.
  - Sets up Java and Flutter environments.
  - Builds release and debug APKs.
  - Builds release App Bundles.
  - Uploads artifacts for APKs and App Bundles.

#### 2. Google Play Production Release
- **File**: `flutter/prod-release-to-stores.yaml`
- **Description**: Builds and releases a Flutter app to Google Play on push or manual trigger.
- **Key Features**:
  - Caches dependencies and build folders.
  - Sets up Java, Flutter, and Ruby environments.
  - Builds APKs and App Bundles.
  - Releases to Google Play using Fastlane.
  - Creates GitHub releases with artifacts.

### Web Workflows

#### 1. Auto Release
- **File**: `web/simple-web-to-github-pages.yml`
- **Description**: Automates the deployment of a web project to GitHub Pages on push.
- **Key Features**:
  - Installs dependencies and builds the project.
  - Deploys the build output to GitHub Pages.

---

Each workflow is designed to streamline the development and deployment processes for Flutter and web projects. Ensure that the required secrets and environment variables are configured in your repository settings before using these workflows.