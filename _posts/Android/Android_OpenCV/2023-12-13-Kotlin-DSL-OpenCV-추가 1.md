---
title: [Android] Kotlin DSL OpenCV Module 사용
excerpt: Kotlin DSL에 OpenCV Module 추가하기

categories:
  - Android OpenCV
tags:
  - Kotlin DSL

last_modified_at: 2023-12-13T22:55:00-05:00
---

## 1. 환경 구성

### 1) OpenCV 파일 다운로드

<img src="https://i.ibb.co/KNQXQs9/opencv-download-android.png" style="width: 600px" />
- [OpenCV 사이트](https://opencv.org/releases/)에서 원하는 버전의 Android SDK를 다운로드한다.
- Android에서 사용할 것이므로 'Android' SDK를 다운로드한다. 복사해서 사용하는 구조이므로 원하는 경로에 압축 해제하면 된다. ex) `C:\`

<br>

## 2. Native C++ 설정

### 1) 신규 Project 생성

- Project를 생성하기 전의 단계라면 간편하게 적용할 수 있다.
<figure> 
<img src="https://i.ibb.co/wLS4fhq/opencv-new-project-C-android-1.png" style="width: 600px" alt="Native C++ 생성"/> 
<figcaption style="text-align: center;">Project 생성 시 'Native C++'로 지정해야 한다.</figcaption> 
</figure>

<figure> 
<img src="https://i.ibb.co/6mgc7r5/opencv-new-project-C-android-2.png" style="width: 600px" alt="C++ Standard 설정"/> 
<figcaption style="text-align: center;"> 'C++ Standard'는 'Toolchain Default'로 지정하여 생성했다.</figcaption> 
</figure>

### 2) 기존 프로젝트에 Native C++ 적용

- 기존 프로젝트에 적용할 경우 신규 프로젝트의 파일들을 복사하여 적용할 수 있다.
- 프로젝트의 `app\src\main\cpp\` 디렉토리를 개발하던 프로젝트의 `app\src\main` 경로에 복사 후 gradle 파일을 설정한다.

<br>

## 3. OpenCV Module 추가

<figure> 
<img src="https://i.ibb.co/zbvzwGD/opencv-import-module-android.png" style="width: 600px" alt="Import Module"/> 
<figcaption style="text-align: center;">Android Studio의 [File] - [New] - [Import Module]을 선택한다.</figcaption> 
</figure>

<figure> 
<img src="https://i.ibb.co/Ntcqj7Z/opencv-import-module-android-2.png" style="width: 600px" alt="Module 정보 입력"/> 
<figcaption style="text-align: center;">경로, 이름 설정</figcaption> 
</figure>

- 압축 해제한 OpenCV 폴더 내의 `sdk` 폴더를 지정해준다.
- Module name에는 원하는 폴더명을 입력하면 된다. `opencv`, `opencv4`와 같은 이름을 권장하는 것으로 보이며 `opencv`를 사용했다.
- Groovy DSL의 경우 바로 추가가 진행되지만 Kotlin DSL 환경의 프로젝트일 경우 오류가 발생한다. 이를 해결하기 위한 추가 작업이 필요하다.

### 1) setting.gradle.kts (Project Setting)

```kotlin
pluginManagement {
    repositories {
        google()
        mavenCentral()
        gradlePluginPortal()
    }
}
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
    }
}

rootProject.name = "YeonHL"
include(":app")
include(":opencv")
```

- 하단에 `include("opencv")`를 추가했다.

### 2) build.gradle.kts (Project)

```kotlin
plugins {
    id("com.android.application") version "8.1.2" apply false
    id("com.android.library") version "8.1.2" apply false
    id("org.jetbrains.kotlin.android") version "1.8.10" apply false
}
```

- `com.android.library`를 추가했다. 버전은 `com.android.application`과 동일하게 적용했다.
- `org.jetbrains.kotlin.android` 버전의 경우 2023-12-13 기준 Compose를 지원하는 버전을 사용했다.
- `com.android.application`, `com.android.library`의 버전은 OpenCV 오류가 발생하지 않는 버전으로 사용했다.

### 3) build.gradle.kts (App)

```kotlin
plugins {
    id("com.android.application")
    id("org.jetbrains.kotlin.android")
}

android {
    namespace = "com.yeonhl.example"
    compileSdk = 34

    defaultConfig {
        applicationId = "com.yeonhl.example"
        minSdk = 31
        targetSdk = 34
        versionCode = 1
        versionName = "1.0"

        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            isMinifyEnabled = false
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }

    buildFeatures {
        compose = true
    }

    composeOptions {
        kotlinCompilerExtensionVersion = "1.4.3"
    }

    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_17
        targetCompatibility = JavaVersion.VERSION_17
    }
    kotlinOptions {
        jvmTarget = "17"
    }
    externalNativeBuild {
        cmake {
            path = file("src/main/cpp/CMakeLists.txt")
            version = "3.22.1"
        }
    }
    ndkVersion = "26.1.10909125"
}

dependencies {
    // OpenCV
    implementation(project(":opencv"))
    implementation(fileTree(mapOf("dir" to "libs", "include" to listOf("*.jar"))))
}
```

- 기존에 사용하던 Compose를 위한 환경 세팅 및 Java 버전을 그대로 사용했으며 `externalNativeBuild` 등에서 누락된 부분을 추가했다.
- OpenCV gradle 파일의 주석을 참고하여 `dependencies`에 `implementation`을 추가했다.

### 4) build.gradle.kts (OpenCV)

```kotlin
plugins {
    id("com.android.library")
    id("kotlin-android")
}


android {
    namespace = "org.opencv"
    compileSdk = 34

    val openCVersionName = "4.8.0"
    val openCVersionCode = ((4 * 100 + 8) * 100 + 0) * 10 + 0

    println("OpenCV: $openCVersionName " + project.buildscript.sourceFile)

    defaultConfig {
        minSdk = 31

        val versionCode = openCVersionCode
        val versionName = openCVersionName

        externalNativeBuild {
            cmake {
                arguments("-DANDROID_STL=c++_shared")
                targets("opencv_jni_shared")
            }
        }
    }

    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_17
        targetCompatibility = JavaVersion.VERSION_17
    }

    buildFeatures {
        aidl = true
        buildConfig = true
    }

    buildTypes {
        getByName("debug") {
            // controlled by OpenCV CMake scripts
            fun Packaging.() {
                jniLibs.keepDebugSymbols.add("**/*.so")  // controlled by OpenCV CMake scripts
            }
        }
        getByName("release") {
            // controlled by OpenCV CMake scripts
            fun Packaging.() {
                jniLibs.keepDebugSymbols.add("**/*.so")  // controlled by OpenCV CMake scripts
            }
            isMinifyEnabled = false
            proguardFiles(getDefaultProguardFile("proguard-android.txt"), "proguard-rules.txt")
        }
    }
    sourceSets {
        getByName("main") {
            jniLibs.srcDirs("native/libs")
            java.srcDirs("java/src")
            aidl.srcDirs("java/src")
            res.srcDirs("java/res")
            manifest.srcFile("java/AndroidManifest.xml")
        }
    }
    externalNativeBuild {
        cmake {
            path(project.projectDir.toString() + "/libcxx_helper/CMakeLists.txt")
        }
    }
    buildToolsVersion = "34.0.0"
    ndkVersion = "26.1.10909125"
}

dependencies {

}
```

- ChatGPT를 활용하여 기존 OpenCV gradle 파일의 Groovy 문법을 Kotlin 문법으로 변경 후 세부 옵션을 수정했다.
- `buildFeatures`의 `aidl`, `buildConfig`는 OpenCV의 `import` 관련 문제를 해결하기 위해 추가했다.
- `fun Packaging.()`, `jniLibs.keepDebugSymols.add`의 경우 Depreciate 문제로 Android Studio에서 변경 추천하는 문법을 적용했다.
- 이외의 `externalNativeBuild` 등은 누락이 있을 경우 추가하는 방법으로 진행했다.

### 5) Project Structure 설정

- Project Structure에서 'Modules', 'Dependencies'를 설정하여 앞에서 작성한 내용들을 반영했다.

<figure> 
<img src="https://i.ibb.co/RS44w47/opencv-project-structure-android-1.png" style="width: 600px" alt="Modules Properties"/> 
<figcaption style="text-align: center;">Modules의 app Properties 설정</figcaption> 
</figure>
- 'app'과 'opencv'의 'Compile Sdk Version', 'Build Tools version', 'NDK Version', 'Source Compatibility', 'Target Compatibility'는 동일하게 지정했다.

<figure> 
<img src="https://i.ibb.co/z2ZKvg2/opencv-project-structure-android-2.png" style="width: 600px" alt="Modules Default Config"/> 
<figcaption style="text-align: center;">Default Config</figcaption> 
</figure>
- 'opencv'의 'Default Config'는 'Min SDK Version'만 'app'과 동일하게 지정했다.

<figure> 
<img src="https://i.ibb.co/M7mkMkg/opencv-project-structure-android-3.png" style="width: 600px" alt="Dependencies"/> 
<figcaption style="text-align: center;">Dependencies에 opencv 등록 확인</figcaption> 
</figure>
>opencv가 2개 존재할 경우 위 사진과 다른 opencv를 클릭하여 파란 글씨의 opencv 등록 과정을 거치면 된다.
- opencv가 올바르게 등록되면 위 사진처럼 나타난다.

<br>

## 참고

[OpenCV com.android.library](https://forum.opencv.org/t/an-exercise-in-frustration/12964/4)
[OpenCV aidl](https://forum.opencv.org/t/opencv-4-8-0-cannot-resolve-symbol-opencv-engine-interface/14307)
