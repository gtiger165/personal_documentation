# personal_documentation

Personal notes about flutter

## Coding Documentation

### SharedPrefUtil

Example helper class for shared preferences library (For android)

```dart
import "../utils/shared_pref_util.dart"

// class helper untuk local cache / SharedPreferences
class SharedPrefUtil {
  // Contoh inisiasi key untuk shared preferences
  static const String _keyPrefs = "key_preference";

  ....

  // keyword Params bisa berupa tipe data, model/data class, dan lain-lain
  static Future<void> setPreferencesValue(Params params) async {
    // Setiap pembuatan method helper diperlukan line berikut
    final SharedPreferences _prefs = await SharedPreferences.getInstance();
    // fungsi setter preferences dari bawaan library shared preferences
    _prefs.setParams(_keyPrefs, params);
  }

  static Future<Params> getPreferencesValue() async {
    final SharedPreferences _prefs = await SharedPreferences.getInstance();
    // fungsi setter preferences dari bawaan library shared preferences
    return await _prefs.getType(_keyPrefs);
  }

  ....
}

// sample usage
// set value
SharedPrefUtil.setPreferencesValue(Params);

// get value
SharedPrefUtil.getPreferencesValue().then((value) {
   // lakukan apa yang diperlukan seperti inisiasi ke
   // tipe data yang diinginkan dan lain-lain
   ....
});
```

### Flutter Build Apk

Add this parameter when build apk/appbundle to reduce it size.<br>
source: https://stackoverflow.com/questions/55536637/how-to-build-signed-apk-from-android-studio-for-flutter <br>

```bash
flutter build apk --target-platform android-arm,android-arm64,android-x64 --split-per-abi
```

### FlutterFire Problem

On firebase documentation flutter, recommended installation using firebase cli (flutterfire). There gonna be problem on permission, so when you want to configure firebase on flutter project, use this command: <br>

```bash
sudo flutterfire configure
```

source: https://stackoverflow.com/questions/38067572/firebase-tools-error-eacces-permission-denied <br>
https://github.com/invertase/flutterfire_cli/issues/27#issuecomment-1002965081 <br>

### Android 12 Exported Policy

New policy from google play store, on android 12 required exported on activity, receiver, service, and provider in AndroidManifest.xml. <br>
source : <br>

- https://stackoverflow.com/questions/69287478/androidexported-added-but-still-getting-error-apps-targeting-android-12-and-hig <br>
- https://stackoverflow.com/questions/68554294/androidexported-needs-to-be-explicitly-specified-for-activity-apps-targeting<br>

### Jitsi Flutter IOS Build problem

Few problem when build on ios is Podfile configuration, try this Podfile and few step on source:

```ruby
# Uncomment this line to define a global platform for your project
platform :ios, '11.0'

# CocoaPods analytics sends network stats synchronously affecting flutter build latency.
ENV['COCOAPODS_DISABLE_STATS'] = 'true'

project 'Runner', {
  'Debug' => :debug,
  'Profile' => :release,
  'Release' => :release,
}

def flutter_root
  generated_xcode_build_settings_path = File.expand_path(File.join('..', 'Flutter', 'Generated.xcconfig'), __FILE__)
  unless File.exist?(generated_xcode_build_settings_path)
    raise "#{generated_xcode_build_settings_path} must exist. If you're running pod install manually, make sure flutter pub get is executed first"
  end

  File.foreach(generated_xcode_build_settings_path) do |line|
    matches = line.match(/FLUTTER_ROOT\=(.*)/)
    return matches[1].strip if matches
  end
  raise "FLUTTER_ROOT not found in #{generated_xcode_build_settings_path}. Try deleting Generated.xcconfig, then run flutter pub get"
end

require File.expand_path(File.join('packages', 'flutter_tools', 'bin', 'podhelper'), flutter_root)

flutter_ios_podfile_setup

target 'Runner' do
  use_frameworks!
  use_modular_headers!

  flutter_install_all_ios_pods File.dirname(File.realpath(__FILE__))
end

post_install do |installer|
  installer.pods_project.targets.each do |target|
    flutter_additional_ios_build_settings(target)
    target.build_configurations.each do |config|
      config.build_settings["EXCLUDED_ARCHS[sdk=iphonesimulator*]"] = "arm64"
      config.build_settings.delete 'IPHONEOS_DEPLOYMENT_TARGET'
      config.build_settings['ENABLE_BITCODE'] = 'NO'
    end
  end
end
```

Source : <br>

- https://milanpanchal24.medium.com/xcode-12-building-for-ios-simulator-but-linking-in-object-file-built-for-ios-file-for-8c0cc28ec832 <br>
- https://giters.com/gunschu/jitsi_meet/issues/309 <br>
- https://stackoverflow.com/questions/69246089/couldnt-build-the-application-for-the-simulator-error-launching-application-on <br>
- https://stackoverflow.com/questions/54704207/the-ios-simulator-deployment-targets-is-set-to-7-0-but-the-range-of-supported-d <br>
- https://stackoverflow.com/questions/65008533/iphoneos-deployment-target-is-set-to-8-0-xcode-12-flutter <br>

### Research On Firebase Cloud Messaging (FCM)

Short story is in current job, i got assign on FCM problem for delay a few seconds. Short explanation <b>caused by the unrealistic heartbeat intervals in Firebase Cloud Messaging</b>. Luckly there some alternatives that i can try later, thats called Pushy(https://pushy.me). Also **priority configuration message** also part of it. <br>
Source : <br>

- https://stackoverflow.com/questions/38725622/android-delay-in-receiving-message-in-fcmonmessagereceived
- http://eladnava.com/google-cloud-messaging-extremely-unreliable/
- https://pushy.me/
- https://github.com/pushy-me/pushy-demo-flutter

### Flutter Local Notifications & FCM Issue

Okay, so i face some issues. On android 12 i tried to test notification through FCM. There problem caused by null value? After a long pain hours i've finally found some solutions. Problem caused by null notification icons so it need no initialize manually on android studio with add -> image asset menu. Few setup settings on flutter local notification plugin.<br>

```dart

    ...

    var androidSettings = const AndroidInitializationSettings("app_icon");
    var iosSettings = IOSInitializationSettings(
      onDidReceiveLocalNotification: (id, title, body, paylod) {},
    );
    var initializeSettings =
        InitializationSettings(android: androidSettings, iOS: iosSettings);
    // * FlutterLocalNotificationsPlugin
    _plugin.initialize(initializeSettings);

    ...

```

####

On FCM issue sometimes it need to call getToken method before listen on firebase foreground message.

```dart
   ...

   FirebaseMessaging.instance.getToken();
   FirebaseMessaging.onMessage.listen((event) {
     // do something
   });

  ...
```

Source : <br>

- https://github.com/MaikuB/flutter_local_notifications/issues/220
- https://stackoverflow.com/questions/60641097/flutter-local-notification-error-int-java-lang-integer-intvalue-on-a-null
- https://github.com/FirebaseExtended/flutterfire/issues/6011
  <br>

### Custom Notification Sound

So i face another issue in local notification, which is about custom notification sound doesn't work because channel ID in notification are a same one. Cuz it depends on channel ID, so however u changed Notification Details settings it will be read based on first settings in the same channel ID. So here's an clear step to add notification sound in Flutter :

1. Adding custom sound in each module, let's say in:
   - android : `android/app/src/main/res/raw/(your_sound_file.mp3)`
   - IOS : `ios/(your_sound_file.aiff)`
   - Note : in android only support on .mp3 extension while IOS is .aiff extension
2. In android add `keep.xml` file
   ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <resources xmlns:tools="http://schemas.android.com/tools"
      tools:keep="@raw/(custom_sound_filename)"/>
   ```
   PS : When define your sound filename no need to add extension of the file, ex. 'morning_alarm.mp3' then define it 'morning_alarm'
3. Next is to set raw resource file on notification settings. Here's an example :

   ```dart
     Future<void> showNotificationCustomSound({
      int? id,
      String? title,
      String? body,
      String? payload,
      required String customSoundFile,
    }) async {
      final androidDetail = AndroidNotificationDetails(
        "${channelId}_${id.nullOrEmpty()}", // Here u set an unique channel ID
        channelName,
        sound: RawResourceAndroidNotificationSound(customSoundFile), // here's ur customSoundFile
        priority: Priority.high,
      );
      final DarwinNotificationDetails darwinNotificationDetails =
          DarwinNotificationDetails(sound: '$customSoundFile.aiff');
      final NotificationDetails notificationDetails = NotificationDetails(
        android: androidDetail,
        iOS: darwinNotificationDetails,
      );

      await flutterLocalNotificationsPlugin.show(
        id.nullOrEmpty(),
        title,
        body,
        notificationDetails,
      );
    }
   ```

   PS : either on each method in local notifications has a same params which is NotificationDetails class that need to pass.

Source :

- [Github Issue](https://github.com/MaikuB/flutter_local_notifications/issues/563#issuecomment-609614118)
