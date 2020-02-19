---
layout: single
title: Flutter Tip - mergeDexDebug
category: flutter
tag: [flutter, android]
comments: true
toc: true
toc_label: "목차"
toc_icon: "list"
---

# 문제

빌드할 때 나올 수 있는 에러입니다.

```text
* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output. Run with --scan to get full insights.

* Get more help at https://help.gradle.org

BUILD FAILED in 6s
[!] The shrinker may have failed to optimize the Java bytecode.
    To disable the shrinker, pass the `--no-shrink` flag to this command.
    To learn more, see: https://developer.android.com/studio/build/shrink-code
Gradle task assembleDebug failed with exit code 1
Exited (sigterm)
```

# 원인

위의 참고: [https://developer.android.com/studio/build/multidex](https://developer.android.com/studio/build/multidex)

minSdkVersion 버전이 20 보다 작을 때 생기는 문제랍니다.

# 해결

multidex 관련 내용 2줄을 추가하면 해결됩니다.

android/app/build.gradle

```text
// ..
    defaultConfig {
        // ..
        multiDexEnabled true
    }
}

dependencies {
    // ..
    implementation 'com.android.support:multidex:1.0.3'
}
```
