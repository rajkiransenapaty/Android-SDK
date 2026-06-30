# Android CI/CD Pipeline Documentation

## Overview

This GitLab CI/CD pipeline automates the complete Android application build, security scanning, artifact generation, and release process.

### Pipeline Flow

```
Source Code
     │
     ▼
Initialize
     │
     ▼
Dependency Resolution
     │
     ▼
Build
     │
     ▼
CycloneDX SBOM Generation
     │
     ▼
SonarQube Analysis
     │
     ▼
APK Packaging
     │
     ▼
Upload APK & SBOM to Artifactory
     │
     ▼
Firebase App Distribution
```

---

# Pipeline Stages

## 1. Initialize

This stage prepares the build environment.

Activities performed:

- Downloads project source code.
- Configures Java.
- Configures Android SDK.
- Configures Gradle.
- Loads required environment variables.

---

## 2. Dependency Resolution

Gradle downloads all project dependencies from the configured repositories.

Repositories include:

- Internal Artifactory
- Maven repositories (configured in project)

---

## 3. Build Stage

The application is compiled using Gradle.

Example command

```bash
./gradlew clean assemble<BuildVariant>
```

Output:

- Compiled APK
- Build logs

---

## 4. CycloneDX SBOM Generation

After the build completes successfully, the CycloneDX Gradle plugin generates a Software Bill of Materials (SBOM).

Generated files

- bom.json
- bom.xml

Purpose

- Dependency inventory
- Security compliance
- Vulnerability scanning
- License compliance

### Artifactory Location

The generated SBOM files are uploaded to Artifactory.

Example

```
<Artifactory Repository>/<Application Name>/<Build Number>/sbom/

├── bom.json
└── bom.xml
```

Developers can download both files directly from Artifactory after the pipeline completes.

---

## 5. SonarQube Analysis

After SBOM generation, the pipeline executes SonarQube static code analysis.

The project configuration is taken from

```
sonar.properties
```

which contains

- Project Key
- Project Name
- Sonar Server URL
- Source directories
- Coverage configuration

### Verify Sonar Analysis

Open SonarQube Web UI.

Search using the configured

```
sonar.projectKey
```

or

```
sonar.projectName
```

You can verify

- Code Quality
- Bugs
- Vulnerabilities
- Security Hotspots
- Code Smells
- Coverage
- Duplications

---

## 6. APK Packaging

Once Sonar analysis succeeds, Gradle generates the APK.

Output example

```
app-release.apk
```

---

## 7. Upload to Artifactory

The generated APK and SBOM files are uploaded to Artifactory.

Uploaded files

```
APK
bom.json
bom.xml
```

Example structure

```
Application/

Build-105/

app-release.apk
bom.json
bom.xml
```

Developers can verify the uploaded artifacts by navigating to the corresponding repository and build folder in Artifactory.

---

## 8. Firebase App Distribution

The final stage distributes the APK to Firebase App Distribution.

This allows testers to download and install the latest application.

The pipeline automatically uploads the APK to the configured Firebase project.

### Verify Release

Login to Firebase Console.

Navigate to

```
App Distribution
```

Select the Android application.

Verify

- Latest uploaded build
- Release notes
- Build version
- Distribution status
- Testers/Groups

---

# How to Run the Pipeline

## Step 1

Open the GitLab project.

## Step 2

Navigate to

```
Build → Pipelines
```

## Step 3

Click

```
Run Pipeline
```

## Step 4

Select the required branch.

## Step 5

Provide pipeline variables (if applicable).

## Step 6

Click

```
Run Pipeline
```

The pipeline will execute all configured stages sequentially.

---

# Pipeline Outputs

| Stage | Output |
|--------|--------|
| Build | APK |
| CycloneDX | bom.json, bom.xml |
| SonarQube | Code Quality Report |
| Artifactory | APK + SBOM |
| Firebase | Distributed APK |

---

# Verification Checklist

✔ Pipeline completed successfully

✔ SonarQube analysis available

✔ APK uploaded to Artifactory

✔ bom.json uploaded

✔ bom.xml uploaded

✔ Firebase distribution successful
