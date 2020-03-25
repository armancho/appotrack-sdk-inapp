# App-O-Track In-App SDK
**Русская Версия** | [English Version](/README_en.md)
> SDK для монетизации ваших приложений с помощью рекламы внутри них

[Заявка на подключение](https://accgp.store/sdk)


## Начало работы

### Модель выплат за рекламу App-O-Track
Реклама в приложении будет показана так:

- Пользователь запускает приложение
- SDK проверяет, находится ли пользователь в стране, в которой разрешён показ рекламы
    - Если да, то реклама будет показана **вместо** приложения. В ваше приложение пользователь войти не сможет.
    - Если нет -- приложение работает как обычно.

В данный момент оплата рекламы предусмотрена только по фиксированному тарифу, например раз в неделю. Это значит что оплата будет происходить независимо от числа установок приложения и количества пользователей. Конкретный тариф и частоту выплат уточняйте у вашего менеджера.

**Мы гарантируем** хороший рейтинг приложения на Google Play и увеличение числа установок так как мы сами будем продвигать ваше приложение.

### Получение SDK
Пожалуйста, выполните следующие действия:
1. Определитесь с тем в какое приложение вы хотите встроить рекламу. У вас должен быть доступ к исходным кодам. Учитывайте, что часть пользователей ничего кроме рекламы не увидят.
2. Свяжитесь с вашим персональным менеджером или подайте заявку через [наш сайт](https://accgp.store/sdk). Если ваше приложение уже доступно в Google Play -- отправьте нам ссылку на него. Персональный менеджер поможет вам во всех дальнейших вопросах, например предоставит техническую консультацию или поможет с оформлением страницы в Google Play вплоть до полной подготовки иконки, снимков экрана, баннеров.
3. Скачайте [AppotrackActivity.java](/AppotrackActivity.java) -- это главный и единственный файл нашего SDK. Поместите его в ваш проект в пакет `com.appotrack_sdk` и переходите к следующему разделу. Если у вас появятся дополнительные вопросы по интеграции, то посмотрите демо-проект в директории [/example](example).
4. После подключения и настройки следуйте разделу "Проверка работы".

### Подключение
#### AndroidManifest.xml
Добавьте следующие разрешения внутрь тега `<manifest>`:
```xml
<!-- AT SDK code -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
<!-- end of AT SDK code -->
```

Добавьте атрибуты `android:usesCleartextTraffic` и `android:hardwareAccelerated` в тег `<application>` чтобы он выглядел примерно так:
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

Внутри тега `<application>` вставьте объявление `AppotrackActivity`:
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

Обратите внимание, что `com.appotrack_sdk.AppotrackActivity` стало главным Activity вашего приложения! Пожалуйста, закомментируйте `<intent-filter>` изначального главного Activity вашего приложения.

#### build.gradle (уровня приложения)
Вставьте в самом начале файла следующий код:
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

Далее вставьте внутри секции `defaultConfig`:
>Обратите внимание что актуальное значение `onesignal_app_id` вы должны получить от своего менеджера!
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

Далее вставьте внутри секции `android`:
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

В секцию `dependencies` добавьте такие зависимости:
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
Добавьте следующие строки:
```
# AT SDK code
-dontwarn com.android.installreferrer
-keep class com.kochava.** {*;}
-keep class com.google.** { *; }
-keep class * extends android.webkit.WebChromeClient { *; }
-dontwarn im.delight.android.webview.**
# end of AT SDK code
```

### Настройка
Реклама не будет работать пока вы не настроите приложение. Основные настройки находятся в файле `AppotrackActivity.java` после строчки `/* TODO: SDK configuration */`. Вы можете настроить:
* `isAtSdkDebugMode` -- установите в `true` чтобы видеть подробный отладочный вывод в Logcat. **Обязательно** установите это значение в `false` прежде чем отправлять приложение в Play Store!
* `remoteConfigId` -- ключ конфигурации рекламы. Получите его у менеджера.
* `kchAppId` -- идентификатор приложения в аналитической системе Kochava. Получите его у менеджера.
* `geoipApiKey` и `geoipApiId` -- ключи к GeoIP API. Получите их у менеджера.
* `sweetieActivityClass` -- класс главного Activity вашего приложения. Установите здесь правильное значение согласно вашего кода.
* `allowedCountries` -- список географий с которых показ рекламы будет разрешён. Получите его у менеджера.
* `onesignal_app_id` (в файле `build.gradle`) -- ID приложения в системе доставки push-уведомлений Onesignal. Получите его у менеджера.
 
### Проверка работы
Ваш менеджер вручную проверит правильность подключения SDK (без предоставления исходных кодов проекта). Для этого в меню Build &rarr; Generate Signed Build выберите "APK" и соберите APK файл с подписью, такой, какой вы будете отправлять в Play Store для публикации.
После успешной сборки передайте файл вашему менеджеру для проверки.

### Замечания к выпускам
#### v19032020
Выполнен переход с системы аналитики Appsflyer на Kochava. Чтобы обновить своё приложение с предыдущих версий SDK выполните следующие действия:
*  Замените имеющийся файл `AppotrackActivity.java` на актуальный;
*  Перенесите настройки SDK из старого файла в новый. Обратите внимание что параметр `afDevKey` был упразднён, и добавлен новый `kchAppId`. Его значение уточняйте у вашего менеджера;
*  В манифесте вашего приложения (`AndroidManifest.xml`) удалите Receiver `com.appsflyer.MultipleInstallBroadcastReceiver`. Новый Receiver добавлять не нужно, он будет добавлен на этапе сборки автоматически;
*  В `build.gradle` уровня приложения необходимо добавить два новых репозитория: `mavenCentral()` и `maven {url "http://kochava.bintray.com/maven"}`;
*  Добавьте ProGuard правило: `-keep class com.kochava.** {*;}`. Удалите правила:
```
-dontwarn com.appsflyer.**
-keep class com.appsflyer.** { *; }
```
*  Зависимости `implementation 'com.appsflyer:af-android-sdk:4.+'` и `implementation 'com.android.installreferrer:installreferrer:1.0'` необходимо убрать и добавить: 
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