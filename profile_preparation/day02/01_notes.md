### Day 2: Build Systems, Configuration Management, and Continuous Integration

This day’s focus will be on the **build processes** (especially Android and Windows build systems), **configuration management** (like branching and environment setups), and **Continuous Integration (CI/CD)** for managing and testing complex multi-platform code.

---

## 1. Build Systems

### Android Build System
The Android build system, based on **Gradle**, is highly modular and handles dependencies, configurations, and builds for different Android environments.

#### Key Concepts in Android Build Systems
- **Gradle**: The main build tool for Android, it compiles, links, and packages Android apps.
- **AndroidManifest.xml**: A key file that defines application metadata, permissions, and other configurations.
- **Build Variants**: Gradle allows you to create build variants, typically combinations of build types (like `debug` and `release`) and product flavors.
  
#### Example Gradle Build Script
Here’s a simple example of a `build.gradle` file for an Android project:
```groovy
android {
    compileSdkVersion 33

    defaultConfig {
        applicationId "com.example.myapp"
        minSdkVersion 21
        targetSdkVersion 33
        versionCode 1
        versionName "1.0"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    implementation 'com.android.support:appcompat-v7:28.0.0'
    testImplementation 'junit:junit:4.12'
}
```

#### Android NDK (Native Development Kit)
For testing roles focused on low-level Android (like GPU compilers), the **NDK** is crucial. The NDK allows C/C++ code integration with Java, ideal for performance-sensitive tasks.

1. **CMake with NDK**: CMake is used for building C/C++ code, and it integrates with Android Studio.
2. **Example CMakeLists.txt for NDK**:
   ```cmake
   cmake_minimum_required(VERSION 3.4.1)
   add_library(native-lib SHARED src/main/cpp/native-lib.cpp)
   find_library(log-lib log)
   target_link_libraries(native-lib ${log-lib})
   ```

### Windows Build System
The **Windows build system** typically relies on **MSBuild** (Microsoft Build Engine) and sometimes **Visual Studio** for larger projects. MSBuild is used to automate the build process, especially in environments that require compiling C++ or C# code.

#### Example MSBuild Command
1. **Building a Project**:
   ```shell
   MSBuild.exe myproject.sln /p:Configuration=Release
   ```

2. **CMake on Windows**:
   CMake is widely used across platforms and works well with Windows.
   ```cmake
   cmake_minimum_required(VERSION 3.10)
   project(MyProject)

   add_executable(main main.cpp)
   ```

### Tips for Qualcomm’s Build Systems Focus
- Qualcomm's GPU compilers likely require you to handle large cross-platform builds. Understanding build scripts and the CMake build system will help you configure, manage, and troubleshoot Android and Windows builds.
- **Focus Areas**: Learn to manage dependencies, set up platform-specific configurations, and handle versioning in build scripts.

---

## 2. Configuration Management

Configuration management is crucial for large codebases like those in Qualcomm’s GPU compiler projects. It ensures consistency across development, testing, and production environments.

### Branching Strategies
1. **Feature Branching**: Each feature has its branch, isolated from the mainline. Once completed and tested, it merges back into the main branch.
2. **Git Flow**: A popular strategy with two main branches (`main` and `develop`) and supporting branches for features, releases, and hotfixes.
   - `main` branch: Always contains the stable, production-ready code.
   - `develop` branch: Contains the latest development code, where new features are merged.

#### Example Git Commands
```shell
# Create a new branch for a feature
git checkout -b feature/new-gpu-optimization

# Merge feature branch back into develop
git checkout develop
git merge feature/new-gpu-optimization
```

### Merging and Tagging
1. **Merging**: When merging branches, always check for conflicts and resolve them. Use `git merge` to merge branches or `git rebase` to keep a cleaner history.
2. **Tagging**: Tags mark specific commits, often for releases.
   ```shell
   git tag -a v1.0 -m "Release version 1.0"
   git push origin v1.0
   ```

### Environment Configurations Across Platforms
In multi-platform setups (like Windows, Linux, and Android):
- **Environment Variables**: Set platform-specific paths or settings via environment variables.
  ```bash
  export ANDROID_HOME=/path/to/android/sdk
  ```
- **Platform-Specific Code Segments**: Use preprocessor directives to handle platform-specific code.
  ```cpp
  #ifdef _WIN32
  // Windows-specific code
  #else
  // Linux/Android code
  #endif
  ```

### Quick Revision for Configuration Management
- **Branching**: Use feature branching for isolated work.
- **Merging**: Always check for conflicts, use `rebase` for a cleaner history.
- **Environment Configs**: Rely on environment variables and preprocessor directives for multi-platform support.

---

## 3. Continuous Integration (CI/CD)

CI/CD ensures that code changes are tested and deployed automatically, making it critical for complex, multi-platform environments like Qualcomm’s.

### Key Components of CI/CD
1. **Source Control Integration**: CI pipelines trigger builds upon commits or pull requests. For example, changes in a branch can automatically trigger a set of tests and build processes.
2. **Automated Testing**: Running test suites as part of the CI pipeline helps catch issues early.
3. **Artifact Management**: Store build artifacts (e.g., compiled binaries, logs) to streamline testing across platforms.
4. **Deployment**: CI/CD automates the deployment process to ensure that new code can be released consistently.

### Example CI/CD Workflow
1. **On Commit**:
   - Run tests to validate the commit.
   - If successful, initiate a build and create an artifact.
2. **On Pull Request (PR)**:
   - Trigger integration tests to ensure compatibility.
   - Require all tests to pass before merging the PR.

### Sample CI/CD Pipeline for Multi-Platform Build
Here’s an example of a CI/CD pipeline configuration using YAML (common in CI systems like GitLab CI, GitHub Actions, or Jenkins):
```yaml
stages:
  - build
  - test

build:
  stage: build
  script:
    - echo "Building on Linux..."
    - cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
    - cmake --build build
    - echo "Building on Windows..."
    - MSBuild.exe myproject.sln /p:Configuration=Release

test:
  stage: test
  script:
    - echo "Running Tests"
    - pytest tests/  # Run tests in Linux/Windows environments
```

### Tips for Qualcomm’s CI/CD Environment
- **Platform-Specific Pipelines**: Qualcomm’s multi-platform requirements mean you might set up different jobs for Windows, Android, and Linux.
- **Automated Testing**: Use test automation scripts to run GPU-specific tests. A typical example could involve launching a VM or container to simulate the required environment and run the tests.
- **Artifact Management**: CI/CD setups often store build artifacts to be shared between teams or stages.

### Common Tools Used
1. **Jenkins**: Open-source CI server for creating custom pipelines with plugins.
2. **GitLab CI/CD**: Integrated with GitLab, known for powerful CI pipelines and easy setup.
3. **GitHub Actions**: CI/CD built into GitHub, offering automated testing workflows.

---

### Revision Summary for Interview Preparation

**1. Build Systems**:
   - **Android**: Know Gradle, the NDK, and how to work with build variants.
   - **Windows**: Understand MSBuild and CMake for compiling Windows applications.
   - **Scripts**: Practice writing sample Gradle, Make, and CMake scripts to manage dependencies and configurations.

**2. Configuration Management**:
   - **Branching**: Familiarize with Git Flow or feature branching, merging, and tagging.
   - **Environment Configuration**: Understand platform-specific configurations using environment variables and conditional code segments.
   - **Git Commands**: Practice creating branches, merging, and resolving conflicts.

**3. Continuous Integration**:
   - **Multi-Platform CI Pipelines**: Set up CI/CD workflows for Linux, Windows, and Android builds.
   - **Automated Testing**: Incorporate testing scripts into CI/CD to catch bugs before merging.
   - **Common Tools**: Familiarize yourself with Jenkins, GitLab CI/CD, and GitHub Actions, as well as YAML configuration for CI/CD scripts.

---
