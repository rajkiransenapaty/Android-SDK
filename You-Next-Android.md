# Android GitLab CI/CD Pipeline

## Overview

This pipeline automates the Android application build, code quality analysis, artifact generation, and application distribution.

### Pipeline Flow

```
Build
   │
   ▼
Sonar Analysis
   │
   ▼
Build UAT
   │
   ▼
Firebase Release
```

---

# Pipeline Stages

## Stage 1: Build

The **Build** stage prepares the project and generates the Software Bill of Materials (SBOM).

### Activities

- Stops any existing Gradle daemon.
- Executes the CycloneDX Gradle task.
- Generates SBOM files:
  - `bom.json`
  - `bom.xml`
- Uploads both files to Artifactory.

### Purpose

The SBOM contains a complete inventory of all third-party libraries and dependencies used by the application. It is mainly used for:

- Security compliance
- Dependency tracking
- Vulnerability management
- Software license verification

### Verify SBOM Files

Login to **Artifactory** and navigate to the configured repository.

Example location:

```
mobile-android/
    build-number/
        bom.json
        bom.xml
```

---

# Stage 2: Sonar Analysis

This stage performs static code analysis using SonarQube.

The analysis uses the configuration available in:

```
sonar.properties
```

which contains:

- Project Key
- Project Name
- Sonar Server URL
- Source folders

### Verify Sonar Results

Open the SonarQube Web UI.

Search using either:

- Project Key
- Project Name

After the scan completes you can review:

- Bugs
- Vulnerabilities
- Code Smells
- Security Hotspots
- Coverage
- Quality Gate Status

---

# Stage 3: Build UAT

This stage builds the UAT Release APK.

Gradle command used:

```bash
./gradlew assembleUatRelease
```

After a successful build:

- APK is generated.
- APK is uploaded to Artifactory.

### APK Location

```
app/build/outputs/apk/uat/release/
```

### Verify APK Upload

Login to Artifactory.

Navigate to the configured repository.

Verify that the latest APK corresponding to the current build version is available.

---

# Stage 4: Firebase Release

This is the final stage of the pipeline.

The pipeline:

- Downloads the APK from Artifactory.
- Verifies the APK.
- Uploads the APK to Firebase App Distribution.

### Verify Firebase Distribution

Open Firebase Console.

Navigate to:

```
App Distribution
```

Select the Android application.

Verify:

- Latest uploaded build
- Version
- Build Number
- Release Notes (if configured)
- Tester Groups

---

# How to Run the Pipeline

## Step 1

Open the GitLab project.

## Step 2

Navigate to:

```
Build
   ↓
Pipelines
```

## Step 3

Click **Run Pipeline**.

## Step 4

Select the required branch.

Example:

```
develop
feature/*
release/*
```

## Step 5

Provide pipeline variables (if required).

## Step 6

Click **Run Pipeline**.

The pipeline will execute all stages sequentially.

---

# Pipeline Summary

| Stage | Description | Output |
|--------|-------------|--------|
| Build | Generates SBOM | bom.json, bom.xml |
| Sonar Analysis | Static code analysis | SonarQube Report |
| Build UAT | Builds Android APK | UAT APK |
| Firebase Release | Distributes APK | Firebase Build |

---

# Verification Checklist

- ✅ Build completed successfully
- ✅ bom.json uploaded to Artifactory
- ✅ bom.xml uploaded to Artifactory
- ✅ Sonar analysis completed successfully
- ✅ APK uploaded to Artifactory
- ✅ APK available in Firebase App Distribution
