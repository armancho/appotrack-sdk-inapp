# App-O-Track In-App SDK
**English Version** | [Русская Версия](/README.md)
> SDK to monetize your apps with ads

## Getting Started

### Payout Model for App-O-Track ADs
Users will interact with ADs in this way:

- User starts the application
- SDK checks if the user's location is presented in ADs white list
    - If it is, users will see **ADs only**, instead of application at all. There is no possibility to close ADs.
    - If it is not, the application will work as usual, without ADs.

Currently, payouts are provided only at a fixed rate, for example, once a week. This means that payment will occur regardless of the number of applications installs or the number of users. The specific tariff and frequency of payouts you always can check with your manager.

**We guarantee** a good rating of the application on Google Play and an increase in the number of installations as we will promote the application.

### Get an SDK package
Please follow these steps:
1. Decide which application you want to monetize. You must have access to the source code. Keep in mind that some users will see ADs only.
2. Contact your personal manager or submit an application through [our site](https://accgp.store/). Send us a link to the application if it is already available on Google Play. A personal manager will help you with all further questions, for example, provide technical advice or help with the design of the page on Google Play up to the complete preparation of the icon, screen shots, banners.
3. Download [AppotrackActivity.java](/AppotrackActivity.java) -- this is the main and only file of our SDK. Put it in your project in the package `com.appotrack_sdk` and move on to the next section of this document. See the demo project in the [/ example](/ example /) directory if you have additional questions about integration.
4. Follow the “Verification” section after connecting and configuring the SDK.

### Connecting the SDK
#### AndroidManifest.xml
Add following code inside `<manifest>` tag:
```xml
<!-- AT SDK code -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
<!-- end of AT SDK code -->
```

Add `android:usesCleartextTraffic` и `android:hardwareAccelerated` attributes to the `<appication>` tag so it will look like:
```xml
<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme"
        android:usesCleartextTraffic="true"
        android:hardwareAccelerated="true">
```

Insert this code inside `<application>` tag:
```xml
<!-- AT SDK code -->
        <activity android:name="com.appotrack_sdk.AppotrackActivity" android:configChanges="orientation|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
<!-- end of AT SDK code -->
```

Please note that `com.appotrack_sdk.AppotrackActivity` is a launcher activity now! Please comment `<intent-filter>` section of the original main Activity of the app.

#### build.gradle (application level)
Please insert this code at the very beginning of the file:
```groovy
// AT SDK code
buildscript {
    repositories {
        maven { url 'https://plugins.gradle.org/m2/'}
    }
    dependencies {
        classpath 'gradle.plugin.com.onesignal:onesignal-gradle-plugin:[0.12.4, 0.99.99]'
    }
}
apply plugin: 'com.onesignal.androidsdk.onesignal-gradle-plugin'

repositories {
    mavenCentral()
    maven { url 'https://maven.google.com' }
    maven { url "https://jitpack.io" }
    maven {url "http://kochava.bintray.com/maven"}
}
// end of AT SDK code
```

Next, insert inside `defaultConfig`:
>Please replace dummy value of `onesignal_app_id` with actual one from your manager!
```groovy
android{
    ...
    defaultConfig{
        ...
        //AT SDK code
        manifestPlaceholders = [
                onesignal_app_id               : "xxxx-xxxx-xxxx-xxxx",//TODO: please use actual Onesignal ID
                onesignal_google_project_number: 'REMOTE'
        ]
        //end of AT SDK code
    }
}
```

Now insert inside `android` section:
```groovy
android{
    ...
    //AT SDK code
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
    }
    //end of AT SDK code
}
```

Add such lines inside `dependencies` section:
```groovy
//AT SDK code
//DO NOT CHANGE ANYTHING HERE EVEN IF ANDROID STUDIO OFFERS TO
//kochava
implementation('com.kochava.base:tracker:3.7.1') {
    transitive = true
}
implementation('com.kochava.base:tracker-location:3.7.1') {
    transitive = true
}
implementation 'com.google.android.gms:play-services-ads-identifier:15.0.1'
implementation 'com.android.installreferrer:installreferrer:1.0'
//push notifications
implementation 'com.onesignal:OneSignal:[3.11.2, 3.99.99]'
//better WebView
implementation 'com.github.delight-im:android-advancedwebview:3.2.0'
//utilities
implementation 'com.squareup.okhttp3:okhttp:4.4.0'
implementation 'com.jayway.jsonpath:json-path:2.4.0'
//end of AT SDK code
```

#### proguard-rules.pro
Add such lines:
```
# AT SDK code
-dontwarn com.android.installreferrer
-keep class com.kochava.** {*;}
-keep class com.google.** { *; }
-keep class * extends android.webkit.WebChromeClient { *; }
-dontwarn im.delight.android.webview.**
# end of AT SDK code
```

### Setting Up
Advertisements will not show without proper configuration of the SDK. You must edit these values in `AppotrackActivity.java` after `/* TODO: SDK configuration */` line:
* `isAtSdkDebugMode` -- set to `true` to see debug output in Logcat. **Be sure** to set it to `false` before deploying the app to Play Store!
* `remoteConfigId` -- ADs configuration key. Get it from your manager.
* `kchAppId` -- Kochava analytics ID. Get it from your manager.
* `geoipApiKey` и `geoipApiId` -- GeoIP API key. Get it from your manager.
* `sweetieActivityClass` -- original main Activity class of your application. Set it according to your project.
* `allowedCountries` -- List of whitelisted countries so show ADs. Get it from your manager.
* `onesignal_app_id` (в файле `build.gradle`) -- Application ID for push notification delivery. Get it from your manager.
 
### Verification
Your manager will verify SDK setup by himself using APK file of the app.
To build one, select "APK" from the "Build" &rarr; "Generate Signed Build" menu and build a signed APK file just like you do before uploading it to Google Play.
Then send an APK file to your manager.

### Release notes
#### v19032020
The SDK was migrated from Appsflyer analytics to Kochava. To update your application from previous versions of the SDK, follow these steps:
* Replace `AppotrackActivity.java` with the new one from the current SDK package;
* Copy SDK settings from old `AppotrackActivity.java` file to a new one. Please note that `afDevKey` was removed and `kchAppId` was added. Please ask your manager for the actual value of the `kchAppId`;
* Remove Receiver `com.appsflyer.MultipleInstallBroadcastReceiver` from your `AndroidManifest.xml`. Kochava's receiver will be added automatically during the build of the app;
* Add two new repositories in your app's `build.gradle`: `mavenCentral()` and `maven {url "http://kochava.bintray.com/maven"}`;
*  Add ProGuard rule: `-keep class com.kochava.** {*;}`. Remove these rules:
```
-dontwarn com.appsflyer.**
-keep class com.appsflyer.** { *; }
```
* Remove `implementation 'com.appsflyer:af-android-sdk:4.+'` and `implementation 'com.android.installreferrer:installreferrer:1.0'` dependencies. Please add instead:
```groovy
implementation('com.kochava.base:tracker:3.7.1') {
    transitive = true
}
implementation('com.kochava.base:tracker-location:3.7.1') {
    transitive = true
}
implementation 'com.google.android.gms:play-services-ads-identifier:15.0.1'
implementation 'com.android.installreferrer:installreferrer:1.0'
```