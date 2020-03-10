---
layout: single
title: Flutter Tip - fcm
category: flutter
tag: [flutter, android]
comments: true
toc: true
toc_label: "목차"
toc_icon: "list"
---

# 문제

파이어베이스의 클라우드 메세지 기능인 FCM을 플러터에서 사용할 때 안드로이드에서 동작하지 않는 문제가 있습니다.

참고: [https://pub.dev/packages/firebase_messaging](https://pub.dev/packages/firebase_messaging)

## 단순 모듈 설치

아래처럼 이벤트 핸들러를 놓아본들 알림을 받으면 앱이 죽어버립니다.

```dart
_firebaseMessaging.configure(
    // onBackgroundMessage: Platform.isIOS ? null : _backgroundMessageHandler,
    onMessage: (Map<String, dynamic> message) {
        print('AppPushs onMessage : $message');
        return;
    },
    onResume: (Map<String, dynamic> message) {
        print('AppPushs onResume : $message');
        return;
    },
    onLaunch: (Map<String, dynamic> message) {
        print('AppPushs onLaunch : $message');
        return;
    },
);
```

## 공식페이지 가이드

공식페이지 대로 Application.java 라는 파일을 만들고 진행할 경우

GeneratedPluginRegistrant.registerWith (registry) 라는 에러메세지가 출력되며 실행 자체가 안됩니다.

# 해결

## java 파일 개선

출처: [https://stackoverflow.com/questions/59446933/pluginregistry-cannot-be-converted-to-flutterengine](https://stackoverflow.com/questions/59446933/pluginregistry-cannot-be-converted-to-flutterengine)

위의 내용 처럼 FirebaseCloudMessagingPluginRegistrant.java 라는 것을 추가해서 해결할 수 있습니다.

**AndroidManifest.xml**  
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="Your Package"> // CHANGE THIS

    <application
        android:name=".Application"
        android:label="" // YOUR NAME APP
        android:icon="@mipmap/ic_launcher">
        <activity
            android:name=".MainActivity"
            android:launchMode="singleTop"
            android:theme="@style/LaunchTheme"
            android:configChanges="orientation|keyboardHidden|keyboard|screenSize|smallestScreenSize|locale|layoutDirection|fontScale|screenLayout|density|uiMode"
            android:hardwareAccelerated="true"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        <!-- BEGIN: Firebase Cloud Messaging -->    
            <intent-filter>
                <action android:name="FLUTTER_NOTIFICATION_CLICK" />
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
        <!-- END: Firebase Cloud Messaging -->    
        </activity>
        <meta-data
            android:name="flutterEmbedding"
            android:value="2" />
    </application>
</manifest>
```

**Application.java**  
```java
package YOUR PACKAGE HERE;

import io.flutter.app.FlutterApplication;
import io.flutter.plugin.common.PluginRegistry;
import io.flutter.plugin.common.PluginRegistry.PluginRegistrantCallback;
import io.flutter.plugins.firebasemessaging.FlutterFirebaseMessagingService;

public class Application extends FlutterApplication implements PluginRegistrantCallback {

  @Override
  public void onCreate() {
    super.onCreate();
    FlutterFirebaseMessagingService.setPluginRegistrant(this);
  }

  @Override
  public void registerWith(PluginRegistry registry) {
    FirebaseCloudMessagingPluginRegistrant.registerWith(registry);
  }
}
```

**FirebaseCloudMessagingPluginRegistrant.java**  
```java
package YOUR PACKAGE HERE;

import io.flutter.plugin.common.PluginRegistry;
import io.flutter.plugins.firebasemessaging.FirebaseMessagingPlugin;

public final class FirebaseCloudMessagingPluginRegistrant{
  public static void registerWith(PluginRegistry registry) {
    if (alreadyRegisteredWith(registry)) {
      return;
    }
    FirebaseMessagingPlugin.registerWith(registry.registrarFor("io.flutter.plugins.firebasemessaging.FirebaseMessagingPlugin"));
  }

  private static boolean alreadyRegisteredWith(PluginRegistry registry) {
    final String key = FirebaseCloudMessagingPluginRegistrant.class.getCanonicalName();
    if (registry.hasPlugin(key)) {
      return true;
    }
    registry.registrarFor(key);
    return false;
  }
}
```

## kotlin 파일로 개선

프로젝트를 kotlin으로 만드신 분들은 아래처럼 변경하시면 됩니다.

**Application.kt**  
```kotlin
package YOUR_PACKAGE_HERE

import io.flutter.app.FlutterApplication
import io.flutter.plugin.common.PluginRegistry
import io.flutter.plugin.common.PluginRegistry.PluginRegistrantCallback
import io.flutter.plugins.firebasemessaging.FlutterFirebaseMessagingService

public class Application: FlutterApplication(), PluginRegistrantCallback {
  override fun onCreate() {
    super.onCreate()
    FlutterFirebaseMessagingService.setPluginRegistrant(this)
  }

  override fun registerWith(registry: PluginRegistry) {
    FirebaseCloudMessagingPluginRegistrant.registerWith(registry)
  }
}
```

**FirebaseCloudMessagingPluginRegistrant.kt**  
```kotlin
package YOUR_PACKAGE_HERE

import io.flutter.plugin.common.PluginRegistry
import io.flutter.plugins.firebasemessaging.FirebaseMessagingPlugin

class FirebaseCloudMessagingPluginRegistrant {
  companion object {
    fun registerWith(registry: PluginRegistry) {
      if (alreadyRegisteredWith(registry)) {
        return;
      }
      FirebaseMessagingPlugin.registerWith(registry.registrarFor("io.flutter.plugins.firebasemessaging.FirebaseMessagingPlugin"))
    }

    fun alreadyRegisteredWith(registry: PluginRegistry): Boolean {
      val key = FirebaseCloudMessagingPluginRegistrant::class.java.name
      if (registry.hasPlugin(key)) {
        return true
      }
      registry.registrarFor(key)
      return false
    }
  }
}
```

출처: [https://stackoverflow.com/a/60531819](https://stackoverflow.com/a/60531819)
