# Creating a new Firebase SDK

Want to create a new SDK in 
[Firebase/firebase-android-sdk](https://github.com/firebase/firebase-android-sdk)?
Read on.

{:toc}

## Repository layout and Gradle

[Firebase/firebase-android-sdk](https://github.com/firebase/firebase-android-sdk)
uses a multi-project Gradle build to organize the different libraries it hosts.
As a consequence, each project/product within this repo is hosted under its own
subdirectory with its respective build file(s).

```bash
firebase-android-sdk
├── buildSrc
├── appcheck
│   └── firebase-appcheck
│   └── firebase-appcheck-playintegrity
├── firebase-annotations
├── firebase-common
│   └── firebase-common.gradle # note the name of the guild file
│   └── ktx
│      └── ktx.gradle.kts # it can also be kts
└── build.gradle # root project build file.
```

Most commonly, SDKs are located as immediate child directories of the root
directory, with the directory name being the exact name of the Maven artifact ID
the library will have once released. e.g. `firebase-common` directory
hosts code for the `com.google.firebase:firebase-common` SDK.

{: .warning }
Note that the build file name for any given SDK is not `build.gradle` or `build.gradle.kts`
but rather mirrors the name of the sdk, e.g.
`firebase-common/firebase-common.gradle` or `firebase-common/firebase-common.gradle.kts`.

All of the core Gradle build logic lives in `buildSrc` and is used by all
SDKs.

SDKs can be grouped together for convenience by placing them in a directory of
choice.

## Creating an SDK

Let's say you want to create an SDK named `firebase-foo`

1.  Create a directory called `firebase-foo`.
1.  Create a file `firebase-foo/firebase-foo.gradle.kts`.
1.  Add `firebase-foo` line to `subprojects.cfg` at the root of the tree.

<details open markdown="block">
  <summary>
    Update `firebase-foo.gradle.kts` with the following content:
  </summary>
  {: .text-delta }
```kotlin
plugins {
  id("firebase-library")
  // Uncomment for Kotlin
  // id("kotlin-android")
}

firebaseLibrary {
    // enable this only if you have tests in `androidTest`.
    testLab.enabled = true
    publishSources = true
    publishJavadoc = true
}

android {
  val targetSdkVersion : Int by rootProject
  val minSdkVersion : Int by rootProject

  compileSdk = targetSdkVersion
  defaultConfig {
    // change this is you have custom needs.
    minSdk = minSdkVersion
    targetSdk = targetSdkVersion
    testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
  }

  testOptions.unitTests.isIncludeAndroidResources = true
}

dependencies {
  implementation(project(":firebase-common"))
  implementation(project(":firebase-components"))
}
```
</details>
