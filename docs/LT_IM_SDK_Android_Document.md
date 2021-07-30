# LT IM SDK Android Document

---
## Overview

With LT SDK, you can build your own customized application with Call and IM function. This documentary provides a guideline that demonstrates how to build and configure an in-app message and call using LT SDK.

1. At the first beginning, you must create user account/login/password to continue the following process.
2. In order to connect to the LT service please read the [Authentication](#authentication).
3. Support Version:

    Android 5.0 (API level 21) or higher ;</br>
    Java 8 or higher ;</br>
    Gradle 5.4.1 or higher ;</br>
    Support Program language：Java, Kotlin

## Try the sample app

The sample application has the core functions of our LT SDK. Download the app from our GitHub repository to get an idea of what you can build with the SDK and start building in your project.

[Download sample app](https://github.com/LoFTechs/LTSample-Android-Kotlin)

## Getting started

### Import SDK

Step 1. Add MavenCentral to your repositories in your project level build.gradle file:

```java
allprojects {
    repositories {
        mavenCentral()
    }
}
```

Step 2. Add the library as a dependency in your module level build.gradle file:

[![Maven Central](https://img.shields.io/maven-central/v/com.loftechs.sdk/lt.svg?label=maven%20central)](https://search.maven.org/search?q=g:%22com.loftechs.sdk%22%20AND%20a:%22lt%22)

```java
def LTSDK_version = "5.x.y"
implementation "com.loftechs.sdk:lt:$LTSDK_version-SNAPSHOT"
implementation "com.loftechs.sdk:im:$LTSDK_version-SNAPSHOT"

```

Step 3. Grant system permissions n your module level AndroidManifest.xml file:

```java
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

```

## Authentication

### Init SDK

In the beginning, you must initialize **LTSDK** instance by passing the **LTSDKOptions** of your App as an argument to a parameter in the `LTSDK.init()` method. When initializing your App, you need to call `LTSDK.init()` in the onCreate method of the application class.

Note: Before switching users, be sure to call [Clean SDK](#clean-sdk)


```java
LTSDKOptions options = LTSDKOptions.builder()
                .context(this)
                .licenseKey("licenseKey")
                .url("https://xxx.xxx.net")
                .userID(userID)
                .uuid(uuID)
                .build();

LTSDK.init(options).subscribe(new Observer<Boolean>() {
            @Override
            public void onSubscribe(Disposable d) {

            }

            @Override
            public void onNext(Boolean aBoolean) {
                Log.i("CHECK", "SampleApp init:" + aBoolean);
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });
```

#### The definition of LTSDKOptions parameters

| Parameter  | Definition | Necessity | Detail                      |
| :--------- | :--------- | :-------- | :-------------------------- |
| context    | Context    | Requierd  | Android context object      |
| licenseKey | String     | Requierd  | LT ID                       |
| url        | String     | Requierd  | LT authenticate url         |
| userID     | String     | Requierd  | LT unique user ID           |
| uuid       | String     | Requierd  | LT unique user authenticate |

### Start SDK

You can get user's information by calling `LTSDK.getInstance().getUsers()` method.


```java
try {
    LTSDK.getInstance().getUsers().subscribe(new io.reactivex.Observer<LTUsers>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTUsers users) {
            //Get user info
        }

        @Override
        public void onError(Throwable e) {
            //Error
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
} catch (LTSDKNoInitializationException e) {
    e.printStackTrace();
}
```

#### The definition of LTusers parameters

| Parameter   | Definition | Detail                      |
| :---------- | :--------- | :-------------------------- |
| userID      | String     | LT unique user ID           |
| uuid        | String     | LT unique user authenticate |
| phoneNumber | String     | User's phone number          |
| semiUID     | String     | Customized unique key to register |
| deviceID    | String     | LT unique device ID          |
| accountSrc  | String     | LT account source           |

### Get user status

Get the status of other users through their respective phoneNumbers or semiUIDs.

1.query with phonenumbers, use `LTSDK.getInstance().getUserStatuswithPhoneNumber()` method.

```java
List<String> phones = new ArrayList<>();
phones.add("+886912345678");
try {
    LTSDK.getInstance().getUserStatusWithPhoneNumbers(phones)
        .subscribe(new io.reactivex.Observer<List<LTUserStatus>>() {
            @Override
            public void onSubscribe(Disposable d) {

            }

            @Override
            public void onNext(List<LTUserStatus> userStatuses) {
                //Get other user info
            }

            @Override
            public void onError(Throwable e) {
                //Error
                if(e instanceof LTErrorInfo) {
                    int returnCode = ((LTErrorInfo) e).getReturnCode();
                    String errorMessage = ((LTErrorInfo) e).getErrorMessage();
                }
            }

            @Override
            public void onComplete() {

            }
        });
} catch (LTSDKNoInitializationException e) {
    e.printStackTrace();
}
```

2.query with semiUIDs, using `LTSDK.getInstance().getUserStatusWithSemiUIDs()` method.

```java
List<String> semiUIDs = new ArrayList<>();
semiUIDs.add("semiUID1");
try {
    LTSDK.getInstance(). getUserStatusWithSemiUIDs(semiUIDs)
        .subscribe(new io.reactivex.Observer<List<LTUserStatus>>() {
            @Override
            public void onSubscribe(Disposable d) {

            }

            @Override
            public void onNext(List<LTUserStatus> userStatuses) {
                //Get other user info
            }

            @Override
            public void onError(Throwable e) {
                //Error
                if(e instanceof LTErrorInfo) {
                    int returnCode = ((LTErrorInfo) e).getReturnCode();
                    String errorMessage = ((LTErrorInfo) e).getErrorMessage();
                }
            }

            @Override
            public void onComplete() {

            }
        });
} catch (LTSDKNoInitializationException e) {
    e.printStackTrace();
}
```

#### LTUserStatus

| Parameter   | Definition | Detail                    |
| :---------- | :--------- | :------------------------ |
| userID      | String     | user's LT unique ID       |
| email       | String     | user's email              |
| phoneNumber | String     | user's phoneNumber        |
| semiUID     | String     | user's custom ID          |
| corpID      | String     | User's corp ID            |
| brandID     | String     | User's brand ID           |
| canVOIP     | String     | User enable VoIP feature. |
| canIM       | String     | user enable IM feature.   |

### Clean SDK

When your App was logged in with different users or when the return code of `LTSDK.init` from `LTErrorInfo` is 6000, be sure to call `LTSDK.clean`.

```java
LTSDK.clean(context).subscribe(new io.reactivex.Observer<Boolean>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(Boolean aBoolean) {
            //success
        }

        @Override
        public void onError(Throwable e) {
            //Clean SDK error
        }

        @Override
        public void onComplete() {

        }
    });
```

## Firebase Cloud Messaging

### About FCM Messages

Firebase Cloud Messaging sends notifications and messages to devices which have installed your App.

### Prerequisites

- A device running Android 4.0 or higher, and Google Play services 15.0.0 or higher.
- FirebaseMessaging API and Android Studio 1.4 or higher with Gradle.


### Connect your App to Firebase

Adding Firebase to your app involves tasks both in the Firebase console and in your open Android project (for example, you download Firebase config files from the console, then move them into your Android project).

#### Step 1: Create a Firebase project
Before you can add Firebase to your Android app, you need to create a Firebase project to connect to your Android app.

1. In the [Firebase console](https://console.firebase.google.com/u/0/), click Add project, then select or enter a Project name.

![Imgur](images/CreateFirebaseProject-1.png)

2. (Optional) If you are creating a new project, you can edit the Project ID.
3. Click Continue.

![Imgur](images/CreateFirebaseProject-2.png)

4. (Optional) Set up Google Analytics for your project
5. Click Create project.

![Imgur](images/CreateFirebaseProject-3.png)

#### Step 2: Register your app with Firebase
To use Firebase in your Android app, you need to register your app with your Firebase project. Registering your app is often called "adding" your app to your project.
![Imgur](images/AddFirebase-1.png)
![Imgur](images/AddFirebase-2.png)

#### Step 3: Add a Firebase configuration file
  - 3.1 Add the Firebase Android configuration file to your app:
    - 3.1.1 Click Download google-services.json to obtain your Firebase Android config file (google-services.json).
    - 3.1.2 Move your config file into the module (app-level) directory of your app.
  
![Imgur](images/AddFirebase-3.png)

  - 3.2 To enable Firebase products in your app, add the google-services plugin to your Gradle files.
    - 3.2.1 In your root-level (project-level) Gradle file (build.gradle), add rules to include the Google Services Gradle plugin. Check that you have Google's Maven repository, as well.
    - 3.2.2 In your module (app-level) Gradle file (usually app/build.gradle), apply the Google Services Gradle plugin:

```java
buildscript {

  repositories {
    // Check that you have the following line (if not, add it):
    google()  // Google's Maven repository
  }

  dependencies {
    // ...

    // Add the following line:
    classpath 'com.google.gms:google-services:4.3.5'  // Google Services plugin
  }
}

allprojects {
  // ...

  repositories {
    // Check that you have the following line (if not, add it):
    google()  // Google's Maven repository
    // ...
  }
}
```

```java
apply plugin: 'com.android.application'
// Add the following line:
apply plugin: 'com.google.gms.google-services'  // Google Services plugin

android {
  // ...
}
```

#### Step 4: Add Firebase SDKs to your app

  - 4.1 Using the Firebase Android BoM, declare the dependencies for the Firebase products that you want to use in your app. Declare them in your module (app-level) Gradle file (usually app/build.gradle).

```java
dependencies {
    // Import the BoM for the Firebase platform
    implementation platform('com.google.firebase:firebase-bom:26.8.0')

    // Declare the dependencies for the Firebase Cloud Messaging and Analytics libraries
    // When using the BoM, you don't specify versions in Firebase library dependencies
    implementation 'com.google.firebase:firebase-messaging'
    implementation 'com.google.firebase:firebase-analytics'
}
```

  - 4.2 Sync your app to ensure that all dependencies have the necessary versions.


### Generate server key form Firebase

#### Step 1: In the [Firebase console](https://console.firebase.google.com/u/0/), click Project settings

![Imgur](images/ServerKey-1.png)

#### Step 2: Go to Cloud Messaging > Project credentials to obtain your server key

![Imgur](images/ServerKey-2.png)

#### Step 3: Register to LFSDK Console

Register your server key to LFSDK server through the [LFSDK console](https://console-sat.baby.juiker.net/info/notification) as follows:

![Imgur](images/ServerKey-3.png)

### Edit your app manifest

Add the following to your app's manifest:

- A service that extends FirebaseMessagingService. This is required if you want to do any message handling beyond receiving notifications on apps in the background. To receive notifications in foregrounded apps, to receive data payload, to send upstream messages, and so on, you must extend this service.

```java
<service
    android:name=".java.MyFirebaseMessagingService"
    android:exported="false">
    <intent-filter>
        <action android:name="com.google.firebase.MESSAGING_EVENT" />
    </intent-filter>
</service>
```

### Retrieve the current registration token

When you need to retrieve the current token, call `FirebaseMessaging.getInstance().getToken()`

```java
FirebaseMessaging.getInstance().getToken()
    .addOnCompleteListener(new OnCompleteListener<String>() {
        @Override
        public void onComplete(@NonNull Task<String> task) {
          if (!task.isSuccessful()) {
            Log.w(TAG, "Fetching FCM registration token failed", task.getException());
            return;
          }

          // Get new FCM registration token
          String token = task.getResult();

          // Log and toast
          String msg = getString(R.string.msg_token_fmt, token);
          Log.d(TAG, msg);
          Toast.makeText(MainActivity.this, msg, Toast.LENGTH_SHORT).show();
        }
    });

```

### Monitor token generation

The onNewToken callback fires whenever a new token is generated.

```java
/**
 * There are two scenarios when onNewToken is called:
 * 1) When a new token is generated on initial app startup
 * 2) Whenever an existing token is changed
 * Under #2, there are three scenarios when the existing token is changed:
 * A) App is restored to a new device
 * B) User uninstalls/reinstalls the app
 * C) User clears app data
 */
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private static final String TAG = MyFirebaseMessagingService.class.getSimpleName();
    @Override
    public void onNewToken(String token) {
        Log.d(TAG, "Refreshed token: " + token);

        // If you want to send messages to this application instance or
        // manage this apps subscriptions on the server side, send the
        // FCM registration token to your app server.
        sendRegistrationToServer(token);
    }
}

```

### Update FCM Token To LT Server

Update this refreshedToken to your app server.

```java
try {
    String key = "update_key";
    boolean cleanOld = true;   //true: clean old key
    LTSDK.getInstance().updateNotificationKey(key, cleanOld).subscribe(new io.reactivex.Observer<LTResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTResponse response) {
            // reponse for server
        }

        @Override
        public void onError(Throwable e) {
            //Error
        }

        @Override
        public void onComplete() {

        }
    });
} catch (LTSDKNoInitializationException e) {
    e.printStackTrace();
}

```

#### Request parameters description

| Parameter   | Definition | Detail                    |
| :---------- | :--------- | :------------------------ |
| key      | String     | FCM API Token       |
| cleanOld       | boolean     | Clean the tokens of previous devices that login using the same userID             |

#### Response parameters description - **LTResponse**

| Parameter   | Definition | Detail                    |
| :---------- | :--------- | :------------------------ |
| returnCode      | int     | Please refer to [Appendix A. Other Error Code List](#appendix-a-other-error-code-list)       |
| returnMsg       | String     | result message           |

### Handling messages

To receive messages, use a service that extends FirebaseMessagingService. Your service should override the onMessageReceived and onDeletedMessages callbacks. It should handle any message within 20 seconds of receipt (10 seconds on Android Marshmallow). The time window may be shorter depending on OS delays incurred ahead of calling onMessageReceived. After that time, various OS behaviors such as Android O's background execution limits may interfere with your ability to complete your work. For more information see our overview on message priority.
onMessageReceived is provided for most message types, with the following exceptions:

- Notification messages delivered when your app is in the background. In this case, the notification is delivered to the device’s system tray. A user tap on a notification opens the app launcher by default.
- Messages with both notification and data payload, when received in the background. In this case, the notification is delivered to the device’s system tray, and the data payload is delivered in the extras of the intent of your launcher Activity.
In summary:

| App state   | Notification | Data    | Both |
| :---------- | :--------- | :------------- |:---------------|
| Foreground  | onMessageReceived  | onMessageReceived |onMessageReceived|
| Background        | System tray     | onMessageReceived | Notification: system tray Data: in extras of the intent.|

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private static final String TAG = MyFirebaseMessagingService.class.getSimpleName();

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        super.onMessageReceived(remoteMessage);
        Log.d(TAG, "From: " + remoteMessage.getFrom());
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        ...
    }
}
```

## Start IM

請使用 `LTSDK.getInstance().getIMManager` 來得到 **LTIMManager** 物件後，可利用 **LTIMManager** 物件來操作連線相關服務。在呼叫 IM API 我們使用 RX 訂閱的方式取的結果，可參閱 [RX](https://github.com/ReactiveX/RxJava) 使用方式。並在每次呼叫 IM 服務時都需帶入**transID**，此**transID**表示此 API 服務的專屬 ID。

### Connect

在使用 IM 相關的各式 API 之前，請務必先呼叫 `LTIMManager.connect`。並且可設定 IMReceiver 來收到 IM 的 event，可參考 [Receive Event](#receive-event)。在呼叫 `LTIMManager.connect` 後，可從 **LTCallbackResultListener** 中得到連線的結果。連線成功後即可以開始使用 IM API 的服務。

```java
try {
    LTIMManager imManager = LTSDK.getInstance().getIMManager(userID);
    imManager.setManagerListener(new IMReceiver());
    imManager.connect(new LTCallbackResultListener<Boolean>() {
        @Override
        public void onResult(Boolean result) {
            // connect success
        }

        @Override
        public void onError(LTErrorInfo errorInfo) {

        }
    });
} catch (ClassNotFoundException e) {
    Log.d(TAG, "ClassNotFoundException:" + e.getMessage());
} catch (LTSDKNoInitializationException e) {
    Log.d(TAG, "LTSDKNoInitializationException:" + e.getMessage());
}
```

### Disconnect

若無需再使用 IM 服務，可呼叫 `LTIMManager.disconnect` 來中斷 IM 服務。強烈建議進入背景時呼叫斷線，可以減少不必要的背景操作。

```java
try {
    LTIMManager imManager = LTSDK.getInstance().getIMManager(userID);
    imManager.disconnect(new LTCallbackResultListener<Boolean>() {
        @Override
        public void onResult(Boolean result) {
            // disconnect success
        }

        @Override
        public void onError(LTErrorInfo errorInfo) {

        }
    });
} catch (ClassNotFoundException e) {
    Log.d(TAG, "ClassNotFoundException:" + e.getMessage());
} catch (LTSDKNoInitializationException e) {
    Log.d(TAG, "LTSDKNoInitializationException:" + e.getMessage());
}
```

### Detect Connection

如需偵測連線服務是否正常，可呼叫 `LTIMManager.isConnected`

```java
booelan isConnected = imManager.isConnected();
```

## Channel

Channel is a delivered path of messages. If you don't have any channel, you should create a channel before sending messages.

Obtain **LTChannelHelper** from **LTIMManager** and call channel funtions.

```java
LTIMManager imManager = LTSDK.getInstance().getIMManager(userID);
LTChannelHelper channelHelper = imManager.getChannelHelper();
// Call channel functions...
```

### Create Single Channel

一對一聊天室為兩用戶間傳遞訊息的聊天室, 此聊天室為固定專屬於兩用戶之間的聊天室。固定專屬的聊天室，無需再操作刪除或退出的功能，並且一對一聊天室中的成員皆可行使相同權限，無需再設定成員角色在**LTMemberModel**中。**LTMemberModel**是在設定成員相關資訊，在建立聊天室前，必須先組成**LTMemberModel**物件，再帶入`LTIMManager.getChannelHelper().createSingleChannel()`來建立一對一聊天室。

```java
LTMemberModel memberModel =LTMemberModel.builder()
        .userID("userID")
        .chNickname("Paul")
        .build();

imManager.getChannelHelper().createSingleChannel("transID", memberModel)
    .subscribe(new io.reactivex.Observer<LTCreateChannelResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTCreateChannelResponse createChannelResponse) {
            //get LTCreateChannelResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

### Create Group Channel

Group Channel 為多人聊天室，每次建立的多人聊天室都是唯一的。一但聊天室解散後，此聊天室就不存在。在多人聊天室中的成員可以設定不同的權限。權限可由**LTMemberModel**設定，並帶入到 `LTIMManager.getChannelHelper().createGroupChannel`。除此之外多人聊天室與一對一聊天室的差異，需自定義聊天室的專屬 ID 以及聊天室顯示的名稱。可參考一下的範例。

```java
Set<LTMemberModel> memberModelSet = new HashSet<>();
LTMemberModel memberModel =LTMemberModel.builder()
        .userID("userIDA")
        .chNickname("Paul")
        .roleID(LTChannelRole.PARTICIPANT)
        .build();
memberModelSet.add(memberModel);

memberModel =LTMemberModel.builder()
        .userID("userIDB")
        .chNickname("Hugo")
        .roleID(LTChannelRole.PARTICIPANT)
        .build();
memberModelSet.add(memberModel);
imManager.getChannelHelper().createGroupChannel("transID", "chID", "First Group Room", memberModelSet)
    .subscribe(new io.reactivex.Observer<LTCreateChannelResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTCreateChannelResponse createChannelResponse) {
            //get LTCreateChannelResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

### Set Channel

建立完 Channel 之後，可以更改 Channel 相關設定，包含以下：

#### Channel Preference

**Channel Preference** 意指 user 本身在聊天室中專屬的設定值，這個設定值只有 user 本身可以取的。每次設定成功後，將會回傳目前所有的在此聊天室的專屬設定值(Channel Preference)。

- **Mute** : 設定聊天室靜音，如需關掉聊天室的通知請設定 true，否則請設定成 false。

```java
imManager.getChannelHelper().setChannelMute("transID", "chID", true)
    .subscribe(new io.reactivex.Observer<LTChannelPreferenceResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTChannelPreferenceResponse channelPreferenceResponse) {
            //get LTChannelPreferenceResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

- **RingTone** : 設定聊天室通知鈴聲，可自行設定鈴聲同步方式，如鈴聲的路徑或是對應的資源檔 ID。

```java
imManager.getChannelHelper().setChannelRingTone("transID","chID","ringTonePath")
    .subscribe(new io.reactivex.Observer<LTChannelPreferenceResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTChannelPreferenceResponse channelPreferenceResponse) {
            //get LTChannelPreferenceResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

- **Nickname** : 設定聊天室專屬暱稱，此暱稱會同步給聊天室成員顯示清單中。

```java
imManager.getChannelHelper().setChannelUserNickname("transID","chID","tree")
    .subscribe(new io.reactivex.Observer<LTChannelPreferenceResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTChannelPreferenceResponse channelPreferenceResponse) {
            //get LTChannelPreferenceResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

#### Channel Profile

**Channel Profile** : 意指聊天室本身的設定，所有成員將會取得相同的資訊。若有成員設定成功後，將會收到當次收到的設定值，並可從 [Receiver Event - Channel](#channel-1) 收他其他成員的更改內容。

- **Subject** :設定聊天室名稱

```java
imManager.getChannelHelper().setChannelSubject("transID","chID","New Subject")
    .subscribe(new io.reactivex.Observer<LTChannelProfileResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTChannelProfileResponse channelProfileResponse) {
            //get LTChannelProfileResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

- **Avatar** : 聊天室的圖示

設定聊天室的圖示

```java
Uri imageUri = Uri.parse(Environment.getExternalStorageDirectory() + "/Download/image.jpg");
imManager.getChannelHelper().setChannelAvatar("transID","chID",imageUri)
    .subscribe(new io.reactivex.Observer<LTChannelProfileFileResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTChannelProfileFileResponse channelProfileFileResponse) {
            //get LTChannelProfileFileResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

刪除聊天室的圖示

```java

LTFileInfo fileinfo = channelProfileResponse.getChannelProfile().get("profileImageFileInfo");

imManager.getChannelHelper().deleteChannelAvatar("transID","chID",fileinfo)
    .subscribe(new io.reactivex.Observer<LTChannelProfileFileResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTChannelProfileFileResponse channelProfileFileResponse) {
            //get LTChannelProfileFileResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

- **CustomAttr** : 設定聊天室的客製化需求，可自訂聊天室屬性，使用 bit 概念彈性擴增聊天室顯示需求。

```java
int chat_highline = 1<<0;
int chat_encrypt= 1<<1;
int customAttr = chat_highline | chat_encrypt;
imManager.getChannelHelper().setChannelCustomAttr("transID","chID", customAttr)
    .subscribe(new io.reactivex.Observer<LTChannelProfileResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTChannelProfileResponse channelProfileResponse) {
            //get LTChannelProfileResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

### Get Channel

此章節將介紹如何主動取的聊天室相關的資訊。

#### Channel Information

取得聊天室設定資訊可呼叫`LTIMManager.getChannelHelper().queryChannel()`，利用帶入不同的參數取得指定的聊天室資訊。參數中可設定是否優先取的聊天室成員的資訊，若設定成 false，也可從 [Get Member](#get-member) 取得成員資訊。另外聊天室的資料大，所以請務必使用批次的機制來避免一次大量的資料，建議的批次數量為每一批 30 筆。

- 取得單一聊天室呼叫方式：

```java
imManager.getChannelHelper().queryChannel("transID", "chID", true)
    .subscribe(new io.reactivex.Observer<LTQueryChannelsResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTQueryChannelsResponse queryChannelsResponse) {
            //get LTQueryChannelsResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

- 取得特定形態的聊天室呼叫方式：

```java
int batchCount = 30;
imManager.getChannelHelper().queryChannelList( "transID", Collections.singletonList(LTChannelType.GROUP), true, batchCount)).subscribe(new io.reactivex.Observer<LTQueryChannelsResponse>() {
    @Override
    public void onSubscribe(Disposable d) {

    }

    @Override
    public void onNext(LTQueryChannelsResponse queryChannelsResponse) {
         //get LTQueryChannelsResponse result
    }

    @Override
    public void onError(Throwable e) {
        if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
    }

    @Override
    public void onComplete() {

    }
});
```

- 取得特定時間點之後有更動的聊天室呼叫方式：

```java
imManager.getChannelHelper().queryChannelList("transID", 1608797454000L, true, 30))
    .subscribe(new io.reactivex.Observer<LTQueryChannelsResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTQueryChannelsResponse queryChannelsResponse) {
           //get LTQueryChannelsResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

#### Channel Read Time

取得所有聊天室的讀取時間，可利用此讀取時間來計算未讀的數量以及相關未讀功能。可是使用`LTIMManager.getChannelHelper(). queryChannelsReadTime()`，搭配**lastChID**以及**count**的參數。**lastChID**可帶入前一次取的最後一間聊天室 ID 來取的下一批次的聊天室讀取時間，若是第一批取得時請帶入`null`表示從第一間聊天室撈取。

```java
imManager.getChannelHelper().queryChannelsReadTime("transID", "lastChID", 100))
    .subscribe(new io.reactivex.Observer<LTQueryChannelsReadTimeResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTQueryChannelsReadTimeResponse queryChannelsReadTimeResponse) {
            //get LTQueryChannelsReadTimeResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

#### Unread Channels

一次取得有未讀聊天室的未讀數量與總未讀數。

```java
imManager.getChannelHelper().queryUnreadChannels("transID"))
    .subscribe(new io.reactivex.Observer<LTQueryUnreadChannelsResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTQueryUnreadChannelsResponse queryUnreadChannelsResponse) {
            //get LTQueryUnreadChannelsResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

### Delete Channel

當多人聊天室有解散的需求時，並擁有解散聊天室的權限時可呼叫`LTIMManager.getChannelHelper().dismissChannel()`。

```java
imManager.getChannelHelper().dismissChannel("transID","chID")
    .subscribe(new io.reactivex.Observer<LTDismissChannelResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTDismissChannelResponse dismissChannelResponse) {
            //get LTDismissChannelResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

## Channel Member

Obtain **LTChannelHelper** from **LTIMManager** and call channel member funtions.

```java
LTIMManager imManager = LTSDK.getInstance().getIMManager(userID);
LTChannelHelper channelHelper = imManager.getChannelHelper();
// Call channel member functions...
```

### Invite Member

若擁有邀請成員的權限，可呼叫`LTIMManager.getChannelHelper().inviteMembers()`來邀請其他成員進入聊天室。其中可使用**LTJoinMethod**設定加入的方式來統計加入方式，若無需使用可帶入`null`。

```java
LTMemberModel memberModel =LTMemberModel.builder()
    .userID("userIDA")
    .chNickname("Paul")
    .roleID(LTChannelRole.MODERATOR)
    .build();
memberModelSet.add(memberModel);

memberModel =LTMemberModel.builder()
    .userID("userIDB")
    .chNickname("Hugo")
    .roleID(LTChannelRole.MODERATOR)
    .build();
memberModelSet.add(memberModel);

imManager.getChannelHelper().inviteMembers("transID","chID", memberModelSet, LTJoinMethod.NORMAL)
    .subscribe(new io.reactivex.Observer<LTInviteMemberResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTInviteMemberResponse inviteMemberResponse) {
            //get LTInviteMemberResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

### Kick Member

若擁有踢人的權限，可呼叫`LTIMManager.getChannelHelper().inviteMembers()`將指定成員踢出聊天室。

```java
Set<LTMemberModel> memberModelSet = new HashSet<>();
LTMemberModel memberModel =LTMemberModel.builder()
    .userID("userIDA")
    .build();
memberModelSet.add(memberModel);

memberModel =LTMemberModel.builder()
    .userID("userIDB")
    .build();
memberModelSet.add(memberModel);

imManager.getChannelHelper().kickMembers("transID","chID", memberModelSet)
    .subscribe(new io.reactivex.Observer<LTKickMemberResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTKickMemberResponse kickMemberResponse) {
            //get LTKickMemberResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

### Join

和 Invite 的差異在於 Invite 由他人邀請加入，Join 則可以主動加入聊天室。若此聊天室擁有主動加入的權限，可呼叫`LTIMManager.getChannelHelper().joinChannel()`加入此聊天室。Join 如同 Invite 可帶入**LTJoinMethod**設定的方式來統計並可搭配誰給予的加入資訊**byWho**，若無需要則可帶入`null`。

```java
imManager.getChannelHelper().joinChannel("transID","chID", LTJoinMethod.NORMAL, "byWho")
    .subscribe(new io.reactivex.Observer<LTJoinChannelResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTJoinChannelResponse joinChannelResponse) {
            //get LTChannelProfileResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

### Leave

若擁有離開的權限，則可呼叫`LTIMManager.getChannelHelper().leaveChannel()`自行離開聊天室。

```java
imManager.getChannelHelper().leaveChannel("transID","chID")
    .subscribe(new io.reactivex.Observer<LTLeaveChannelResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTLeaveChannelResponse leaveChannelResponse) {
            //get LTLeaveChannelResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

### Set Member

若擁有更改成員角色的權限，則可呼叫`LTIMManager.getChannelHelper().setMemberRole()`指定成員的角色。一般最常使用的情境，當管理者要離開聊天室時，需先將指定的成員設定成管理者的身份再呼叫離開聊天室。

```java
imManager.getChannelHelper().setMemberRole("transID","chID", "userIDA", LTChannelRole.MODERATOR)
    .subscribe(new io.reactivex.Observer<LTMemberRoleResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTMemberRoleResponse memberRoleResponse) {
            //get LTMemberRoleResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

### Get Member

#### Member Information

在獲取成員清單會根據你的角色權限不同，而有不一樣的結果。例如角色權限越高，能讀取的角色範圍越多。可使用`LTIMManager.getChannelHelper().queryChannelMembersByChID()`來取得單一聊天室清單。這裡建議使用分批撈取的方式，**lastUserID**為上一批最後的 userID，當為第一次撈取時請帶入`null`。

```java
imManager.getChannelHelper().queryChannelMembersByChID("transID","chID", "lastUserID", 100)
    .subscribe(new io.reactivex.Observer<LTQueryChannelMembersResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTQueryChannelMembersResponse queryChannelMembersResponse) {
            //get LTChannelProfileResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

#### Member Read Info

如需取的聊天室其他成員讀取狀態，我們可以利用`LTIMManager.getChannelHelper().queryChannelReadInfo()`來取的單一聊天室的成員已讀時間。可利用此成員的已讀時間來計算已傳送訊息的已讀狀態。

```java
imManager.getChannelHelper().queryChannelReadInfo("transID","chID")
    .subscribe(new io.reactivex.Observer<LTQueryChannelReadInfoResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTQueryChannelReadInfoResponse queryChannelReadInfoResponse) {
            //get LTQueryChannelReadInfoResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

## Message

Obtain **LTMessageHelper** from **LTIMManager** and call message funtions.

```java
LTIMManager imManager = LTSDK.getInstance().getIMManager(userID);
LTMessageHelper messageHelper = imManager.getMessageHelper();
// Call message functions...
```

### Send Message

次章節主要介紹 LTIMSDK 支援的訊息傳送方式以及訊息種類。

#### Messages

以下為 LTIMSDK 可支援的訊息種類，再搭配後面章節的傳送方式 [Send Message](#send-message), [Send Scheduled Messages](#send-scheduled-messages), [Send Broadcast Messages](#send-broadcast-messages)。

- Text Message 一般文字訊息。避免訊息傳送資料量過大，請限制字數在 8000 以內。文字訊息同時支援標記(tag)訊息，可以利用標記訊息 指定的聊天室成員。標記成員可使用**LTTagUser**設定標記的成員內容，搭配**LTTagUser**中的**start**與**length**可彈性替換 **msgContent**中顯示標記的方式。

```java
List<LTTagUser> tagUsers = new ArrayList<>();
LTTagUser tagUsers = LTTagUser.builder()
    .userID("userIDA")
    .start(0)
    .length(4)
    .build();

LTTextMessage.builder()
    .transID(transID)
    .chID(chID)
    .chType(chType)
    .msgContent("@Paul, How are you?")
    .tagUsers(tagUsers)
    .build();

```

- Sticker Message 貼圖訊息。此貼圖訊息可搭配 LT 專屬貼圖也可以替換成自定義貼圖。如用自訂貼圖可使用**msgContent**設定貼圖的取得方式，例如貼圖檔案路徑。

```java
LTStickerMessage stickMessage = LTStickerMessage.builder()
    .transID(transID)
    .chID(chID)
    .chType(chType)
    .msgContent("690d1a03-b9c5-ae30-046b-24936dc97c46,3013,2")
    .build();
```

- Image Message 圖片訊息。若使用**LTImageMessage**來傳送圖片訊息，將會傳送圖片訊息到 LT 設定的雲端空間。若想使用指定雲端空間，則可使用**LTCustomMessage**客制傳送的方式。在**LTImageMessage**中可傳送原圖路徑以及縮圖路徑，必須使用手機本地端的檔案。另外提供**extInfo**欄位，可添加附加資訊，例如圖片的寬高，可使用在圖片顯示上。

```java
Uri imageUri = Uri.parse(Environment.getExternalStorageDirectory() + File.separator + "Download/ef05f74a-90f7-4f69-94cb-d588fa435bae.png");
Uri thUri = Uri.parse(Environment.getExternalStorageDirectory() + File.separator + "Download/ef05f74a-90f7-4f69-94cb-d588fa435bae.png");

Map<String, Object> extInfoMap = new HashMap<>();
extInfoMap.put("width", 360);
extInfoMap.put("height", 422);

LTImageMessage imageMessage = LTImageMessage.builder()
    .transID(transID)
    .chID(chID)
    .chType(chType)
    .imageUri(imageUri)
    .thumbnailUri(thUri)
    .extInfo(extInfoMap)
    .displayFileName("android.png")
    .build();
```

- Video Message 影像訊息。若使用**LTVideoMessage**來傳送影像訊息，將會傳送影像訊息到 LT 設定的雲端空間。若想使用指定雲端空間，則可使用**LTCustomMessage**客制傳送的方式。在**LTVideoMessage**中可傳送影像路徑以及縮圖路徑，必須使用手機本地端的檔案。另外提供**extInfo**欄位，可添加附加資訊，例如影像的寬高，可使用在圖片顯示上。

```java
Uri videoUri = Uri.parse(Environment.getExternalStorageDirectory() + File.separator + "Download/test.mp4");
Uri thumbnailUri = Uri.parse(Environment.getExternalStorageDirectory() + File.separator + "Download/images.jpeg");

Map<String, Object> extInfoMap = new HashMap<>();
extInfoMap.put("width", 360);
extInfoMap.put("height", 422);

LTVideoMessage videoMessage = LTVideoMessage.builder()
    .transID(transID)
    .chID(chID)
    .chType(chType)
    .videoUri(videoUri)
    .thumbnailUri(thumbnailUri)
    .extInfo(extInfoMap)
    .displayFileName("android.mp4")
    .build();
```

- Voice Message 語音訊息。若使用**LTVoiceMessage**來傳送語音訊息，將會傳送語音訊息到 LT 設定的雲端空間。若想使用指定雲端空間，則可使用**LTCustomMessage**客制傳送的方式。在**LTVoiceMessage**中可傳送語音路徑，必須使用手機本地端的檔案。另外提供**extInfo**欄位，可添加附加資訊，例如語音長度，可使用在圖片顯示上。

```java
Uri voiceUri = Uri.parse(Environment.getExternalStorageDirectory() + File.separator + "Download/27dc2d02-a0df-47d9-9443-f8678356e25e.aac");

Map<String, Object> extInfoMap = new HashMap<>();
extInfoMap.put("duration", "00:05:30");

LTVoiceMessage voiceMessage = LTVoiceMessage.builder()
    .transID(transID)
    .chID(chID)
    .chType(chType)
    .voiceUri(voiceUri)
    .extInfo(extInfoMap)
    .displayFileName("voice.aac")
    .build();
```

- Location Message 位置訊息。可搭配[Google Map](https://developers.google.com/maps/documentation/android-sdk/map)取的定位資訊，並將資料組出**LTLocation**參數帶入**LTLocationMessage**傳送。

```java
LTLocation location = LTLocation.builder()
        .address("300台灣新竹市東區關新路27號號 17 樓 之 1")
        .latitude(24.784781264526057)
        .longitude(121.01793609559535)
        .build();
LTLocationMessage locationMessage = LTLocationMessage.builder()
        .transID(transID)
        .chID(chID)
        .chType(chType)
        .location(location)
        .build();
```

- Contact Message 聯絡人訊息。若使用**LTContactMessage**來傳送聯絡人訊息，將會傳送聯絡人訊息到 LT 設定的雲端空間。若想使用指定雲端空間，則可使用**LTCustomMessage**客制傳送的方式。在**LTContactMessage**中可傳送聯絡人檔案路徑以及縮圖路徑，必須使用手機本地端的檔案。另外提供**extInfo**欄位，可添加附加資訊，例如聯絡人名稱，可使用在圖片顯示上。

```java
Uri contactUri = Uri.parse(Environment.getExternalStorageDirectory() + File.separator + "Download/27dc2d02-a0df-47d9-9443-f8678356e25e.vcf");
Uri thumbnailUri = Uri.parse(Environment.getExternalStorageDirectory() + File.separator + "Download/images.jpeg");

Map<String, Object> extInfoMap = new HashMap<>();
extInfoMap.put("displayName", "Ann");

LTContactMessage.builder()
    .transID(transID)
    .chID(chID)
    .chType(chType)
    .contactUri(contactUri) // vcard
    .thumbnailUri(thumbnailUri)
    .displayFileName(Ann.vcf)
    .extInfo(extInfoMap)
    .build());
```

- Document Message 各類格式檔案訊息。若使用**LTDocumentMessage**來傳送檔案訊息，將會傳送檔案訊息到 LT 設定的雲端空間。若想使用指定雲端空間，則可使用**LTCustomMessage**客制傳送的方式。在**LTDocumentMessage**中可傳送檔案路徑，必須使用手機本地端的檔案。另外提供**extInfo**欄位，可添加附加資訊，例如檔案大小，可使用在圖片顯示上。

```java
Uri documentUri = Uri.parse(Environment.getExternalStorageDirectory() + File.separator + "Download/27dc2d02-a0df-47d9-9443-f8678356e25e.doc");

Map<String, Object> extInfoMap = new HashMap<>();
extInfoMap.put("fileSize", "5MB");

LTDocumentMessage.builder()
    .transID(transID)
    .chID(chID)
    .chType(chType)
    .fileUri(documentUri)
    .displayFileName(Document.doc)
    .extInfo(extInfoMap)
    .build();
```

- Customed Message 客制化訊息。如果以上的訊息總類無法達到使用的需求，可使用**LTCustomMessage**來傳送客製化訊息。可以利用 Json 格式帶入**msgContent**並搭配**LTMessageAttribute**設定訊息的行為模式。

**LTMessageAttribute**

| Parameter | Description |
| --- | --- |
| SAVE_MSG | The message would be stored by the server. |
| SEND_ONLINE_USER | The message status would be synced to other online members who has the same channel. |
| SEND_IN_CHANNEL_USER | The message status would be synced to other online member who are also happened to be in the same channel. |
| SEND_NOTIFICATION | The message would stimulate notification. |
| ADD_UNREAD_COUNT | The message would show “read/unread”. |
| UPDATE_CHANNEL_LAST_MSG | The message would be updated to appear in the chatroom list. |

```java
int attributes = LTMessageAttribute.SAVE_MSG | LTMessageAttribute.SEND_NOTIFICATION;

LTCustomMessage.builder()
    .transID(transID)
    .chID(chID)
    .chType(chType)
    .msgContent("{\"title\":\"titleA\",\"body\":\"body!!\",\"note\":\"note\"}")
    .msgCategory("msg_custom")
    .attributes(attributes)
    .extInfo(extInfoMap)
    .build());
```

#### Relpy Messages

回覆訊息可指定各類[Messages](#messages)訊息(LT:母訊息)進行回覆，並且也可回覆各類[Messages](#messages)訊息(LT:子訊息)。母訊息的指定方式是利用**LTReplyMessage**帶入各類子訊息中，**LTReplyMessage**設定的方式將會以母訊息傳送成功後收到的資訊帶入指定參數。另外需將母訊息收到的**msgID**帶入到子訊息的**parentMsgID**，當子訊息設定完成後即可利用[Send Message](#send-message)傳送。以下為圖片訊息與文字訊息交錯示範為例子。

- 圖片訊息(母訊息) 回覆 文字訊息(子訊息)

```java
LTReplyMessage replyMessage = LTReplyMessage.builder()
    .msgID("cec95048-9e62-11ea-9c63-599b39045450")
    .msgType(LTMessageType.TYPE_TEXT)
    .sendTime(1590054366458L)
    .senderID("senderUserID")
    .senderNickname("Ann")
    .build();

Uri imageUri = Uri.parse(Environment.getExternalStorageDirectory() + File.separator + "Download/ef05f74a-90f7-4f69-94cb-d588fa435bae.png");
Uri thUri = Uri.parse(Environment.getExternalStorageDirectory() + File.separator + "Download/ef05f74a-90f7-4f69-94cb-d588fa435bae.png");

Map<String, Object> extInfoMap = new HashMap<>();
extInfoMap.put("width", 360);
extInfoMap.put("height", 422);

LTImageMessage imageMessage = LTImageMessage.builder()
    .transID(transID)
    .chID(chID)
    .chType(chType)
    .imageUri(imageUri)
    .thumbnailUri(thUri)
    .extInfo(extInfoMap)
    .displayFileName("android.png")
    .replyMessage(replyMessage)
    .parentMsgID("cec95048-9e62-11ea-9c63-599b39045450")
    .build();

```

- 文字訊息(子訊息) 回覆 圖片訊息(母訊息)

```java
LTReplyMessage replyMessage = LTReplyMessage.builder()
    .msgID("9a3a8afa-06f8-11eb-9c63-1a2e0f0676dd")
    .msgType(LTMessageType.TYPE_IMAGE)
    .sendTime(1601895037417L)
    .senderID("senderUserID")
    .senderNickname("Ann")
    .fileInfo(fileInfo)
    .thumbnailFileInfo(thumbnailFileInfo)
    .build();

LTTextMessage textMessage = LTTextMessage.builder()
    .transID(transID)
    .chID(chID)
    .chType(chType)
    .msgContent("1234")
    .replyMessage(replyMessage)
    .parentMsgID("07a9dd94-d5f7-11ea-9c63-1a2d5b06220d")
    .build();

```

#### Send Message

如需傳送各類訊息[Messages](#messages)以及回覆訊息[Relpy Messages](#reply_messages)，可使用`LTIMManager.getMessageHelper().sendMessage()`來傳送指定的訊息。傳送成功後會收到**LTSendMessageResponse**的結果，其中**LTMessage**可轉成各類訊息[Messages](#messages)的物件，並取得其專屬的資訊。可參考以下範例。

```java
LTTextMessage textMessage = LTTextMessage.builder()
    .transID(transID)
    .chID(chID)
    .chType(chType)
    .msgContent("1234")
    .build();

imManager.getMessageHelper().sendMessage(textMessage)
    .subscribe(new io.reactivex.Observer<LTSendMessageResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTSendMessageResponse response) {

            // 無檔案格式(文字訊息)，收到表示傳送成功。
            // get LTSendMessageResponse result
            String msgContent = ((LTTextMessage) message).getMsgContent();

            // 檔案格式(圖片訊息)，收到需確認狀態。
            // get LTFileMessageResponse result
            if (response instanceof LTFileMessageResponse) {
                LTFileMessageResponse fileMessageResponse = (LTFileMessageResponse) response;
                // 傳檔狀態
                if (fileMessageResponse.getFileMessageStatus() == LTFileMessageStatus.STATUS_FILE) {
                    for (LTFileTransferResult result : fileMessageResponse.getFileTransferResults()) {
                        LTFileType fileType = result.getFileType();
                        LTStorageManager.StorageStatus status = result.getStatus();
                        long loadingBytes = result.getLoadingBytes();
                        long totalLength = result.getTotalLength();
                    }
                //  訊息傳送成功狀態
                } else if(fileMessageResponse.getFileMessageStatus() == LTFileMessageStatus.STATUS_MESSAGE) {
                    //原圖
                    LTFileInfo fileInfo = ((LTImageMessage) message).getFileInfo();
                    //縮圖
                    LTFileInfo thumbnailFileInfo = ((LTImageMessage) message).getThumbnailFileInfo();
                }
            }
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

### Send Broadcast Messages

[Send Message](#send-message)可發送一則各類訊息到指定對方或聊天室，若要一次發送多則訊息則可使用`LTIMManager.getMessageHelper().sendBroadcastMessages()`傳送至多個使用者或多間聊天室。廣播訊息後所收到的**LTBroadcastMessageResponse**結果，為廣播行為是否成功，傳送出的行為將會在[Receive Event-Message](#message-2)收到廣播的訊息。

```java
LTTextMessage textMessage = LTTextMessage.builder()
    .transID(transIDA)
    .msgContent("message")
    .build();
LTStickerMessage stickMessage = LTStickerMessage.builder()
    .transID(transIDB)
    .msgContent("690d1a03-b9c5-ae30-046b-24936dc97c46,3013,2")
    .build();

Uri imageUri = Uri.parse(Environment.getExternalStorageDirectory() + File.separator + "Download/ef05f74a-90f7-4f69-94cb-d588fa435bae.png");
Uri thUri = Uri.parse(Environment.getExternalStorageDirectory() + File.separator + "Download/ef05f74a-90f7-4f69-94cb-d588fa435bae.png");
Map<String, Object> extInfoMap = new HashMap<>();
extInfoMap.put("width", 360);
extInfoMap.put("height", 422);

LTImageMessage imageMessage = LTImageMessage.builder()
    .transID(transIDC)
    .imageUri(imageUri)
    .thumbnailUri(thUri)
    .extInfo(extInfoMap)
    .displayFileName("android.png")
    .build();

List<LTMessage> messages = new ArrayList<>();
messages.add(textMessage);
messages.add(stickMessage);
messages.add(imageMessage);

List<String> chIDs = new ArrayList<>();
chIDs.add("chID");

imManager.getMessageHelper().sendBroadcastMessages(transIDE, chIDs, messages)
	.subscribe(new io.reactivex.Observer<LTBroadcastMessageResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTBroadcastMessageResponse messageResponse) {
            //get LTBroadcastMessageResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

### Send Scheduled Messages

如需將傳送的訊息設定指定的時間送達到使用者或聊天室時，請使用`LTIMManager.getMessageHelper().sendScheduledMessages()`傳送預約訊息並帶入指定的`timeToSend`，為預約的時間戳記。`timeToSend`請務必設定超過 1 分鐘並且在 30 天以內的時間。當指定的時間到達時，傳送出的訊息將會在[Receive Event-Message](#message-2)收到，並且同時預約者將會收到預約訊息確認送出的訓令。

```java
LTTextMessage textMessage = LTTextMessage.builder()
    .transID(transID)
    .msgContent("message")
    .build();
LTStickerMessage stickMessage = LTStickerMessage.builder()
    .transID(transID)
    .msgContent("690d1a03-b9c5-ae30-046b-24936dc97c46,3013,2")
    .build();

Uri imageUri = Uri.parse(Environment.getExternalStorageDirectory() + File.separator + "Download/ef05f74a-90f7-4f69-94cb-d588fa435bae.png");
Uri thUri = Uri.parse(Environment.getExternalStorageDirectory() + File.separator + "Download/ef05f74a-90f7-4f69-94cb-d588fa435bae.png");
Map<String, Object> extInfoMap = new HashMap<>();
extInfoMap.put("width", 360);
extInfoMap.put("height", 422);

LTImageMessage imageMessage = LTImageMessage.builder()
    .transID(transID)
    .imageUri(imageUri)
    .thumbnailUri(thUri)
    .extInfo(extInfoMap)
    .displayFileName("android.png")
    .build();

List<LTMessage> messages = new ArrayList<>();
messages.add(textMessage);
messages.add(stickMessage);
messages.add(imageMessage);

List<String> chIDs = new ArrayList<>();
chIDs.add("chID");

imManager.getMessageHelper().sendScheduledMessages(transID, chIDs, messages, timeToSend)
    .subscribe(new io.reactivex.Observer<LTScheduledMessageResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTScheduledMessageResponse messageResponse) {
            //get LTScheduledMessageResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

### Send Forward Messages

如需將已傳送的訊息，轉傳至其他使用者或聊天室時，可使用`LTIMManager.getMessageHelper().sendForwardMessages()`將各類訊息轉傳。轉傳訊息不需要組出訊息格式，只需要帶入轉傳訊息的`msgID`即可。轉傳訊息後所收到的`LTForwardMessageResponse`結果，為轉傳行為是否成功，傳送出的行為將會在[Receive Event-Message](#message-2)收到轉傳的訊息。

```java
List<String> chIDs = new ArrayList<>();
chIDs.add("chID");

List<String> msgIDs = new ArrayList<>();
msgIDs.add("msgIDA");
msgIDs.add("msgIDB");

imManager.getMessageHelper().sendForwardMessages(transID, chIDs, msgIDs)
	.subscribe(new io.reactivex.Observer<LTForwardMessageResponse>() {
	    @Override
	    public void onSubscribe(Disposable d) {

	    }

	    @Override
	    public void onNext(LTForwardMessageResponse messageResponse) {
	        //get LTForwardMessageResponse result
	    }

	    @Override
	    public void onError(Throwable e) {
	        if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
	    }

	    @Override
	    public void onComplete() {

	    }
	});
```

### Get Message

#### Message

取得訊息資訊可呼叫`LTIMManager.getMessageHelper().queryMessage()`，利用帶入不同的參數取得不同的訊息資訊。參數中可指定特定的聊天室或是所有的聊天室，請利用`chID`來指定特定聊天室，否則請帶入`null`。撈取訊息方式使用指定的時間點並設定往前撈取或是往後撈取的方式，請搭配`markTS`以及`afterN`設定。`afterN`可設定正數即表示指定時間點往新的訊息撈取，負數則表示往舊的訊息撈取。建議設定範圍為正負 30，務必不要超過正負 100 以外。

```java
long markTS = System.currentTimeMillis();
int afterN = -30;
imManager.getMessageHelper().queryMessage(transID, chID, markTS, afterN))
    .subscribe(new io.reactivex.Observer<LTQueryMessageResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTQueryMessageResponse queryMessageResponse) {
            //get LTQueryMessageResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

#### File

如需取得各式檔案類型必須使用**LTStorageManager**來取得檔案，可呼叫`LTStorageManager.execute`來執行取檔。可將**LTFileInfo**帶入到**LTStorageAction**的參數中。另外**LTFileInfo**則可由**LTSendMessageResponse**取得。

The define method of LTStorageAction

| Method | Description |
| :-- | :-- |
| createDownloadFileAction() | 取得下載檔案 |
| createDownloadFileLinkAction() | 取得下載連結 |
| createUploadFileAction() | 上傳檔案 |
| createDeleteFileAction() | 刪除檔案 |


```java
LTMessage message = LTSendMessageResponse.getMessage();


int expireMinute =30;

//原圖
LTFileInfo fileInfo = ((LTImageMessage) message).getFileInfo();
File file = new File("/output/file/path");
LTStorageAction action = LTStorageAction.createDownloadFileAction(fileInfo, file);

//縮圖
LTFileInfo thumbnailFileInfo = ((LTImageMessage) message).getThumbnailFileInfo();
File thumbnailFile = new File("/output/thumbnailFile/path");
LTStorageAction actionThumbnail = LTStorageAction.createDownloadFileAction(thumbnailFileInfo, thumbnailFile);

List<LTStorageAction> actions = new ArrayList<>();
actions.add(action);
actions.add(actionThumbnail);

LTStorageManager storageManager = null;
try {
    storageManager = LTSDK.getInstance().getStorageManager("userID");
    storageManager.execute(actions)
        .subscribe(new io.reactivex.Observer<List<LTStorageResult>>() {
            @Override
            public void onSubscribe(Disposable d) {

            }

            @Override
            public void onNext(List<LTStorageResult> storageResults) {
                for (LTStorageResult result : storageResults) {
                    String id = result.getId();
                    LTStorageManager.StorageStatus status = result.getStatus();
                    long loadingBytes = result.getLoadingBytes();
                    long totalLength = result.getTotalLength();
                }
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });
} catch (ClassNotFoundException e) {
    e.printStackTrace();
} catch (LTNoPermissionException e) {
    e.printStackTrace();
} catch (LTSDKNoInitializationException e) {
    e.printStackTrace();
}
```

### Read Message

此章節在介紹如何實作已讀功能

#### Set Read

設定已讀請使用`LTIMManager.getMessageHelper().markRead()`來送出已讀狀態。已讀的設定方式是將需標示的已讀訊息的`sendTime`帶入參數，需注意的是若設定此則訊息已讀時，會同時將此則訊息以前的訊息都是視同已讀狀態。使用情境，當每次進入聊天室時或是正在聊天室中收到即時訊息時可送出當下最後一則訊息的`sendTime`，視同此訊息以前的訊息都已讀取，不需再針對每一則訊息設定已讀狀態。同時也可從[Receive Event-Message](#message-2)收到他人的已讀狀態。

```java
long sendTime = 1608797454000L //標示已讀訊息的sendTime
imManager.getMessageHelper().markRead(transID, chID, sendTime))
    .subscribe(new Observer<LTMarkReadResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTMarkReadResponse markReadResponse) {
    	    //get LTMarkReadResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

#### Get Read

從[Set Read](#set-read)從了解如何設定自己的已讀資訊，接下來要來介紹如何取得他人的已讀資訊。在聊天室其他成員設定[Set Read](#set-read)後，可從[Receive Event-Message](#message-2)收到此成員的已讀狀態。並從收到的`LTMarkReadResponse`得知此成員的最後讀取的訊息時間，得而計算此成員在此聊天室的已讀狀態。若成員整傳送送已讀狀態，同時自己不在線上無法收到已讀狀態時，需當重新連線並回到聊天室時可呼叫[Member Read Info](#member-read-info)來取得聊天室成員的已讀狀態。</br>

除了使用每個成員的已讀資訊外，我們還提供針對每一則訊息的已讀狀態，可以針對每一則訊息來取的訊息的已讀資訊。

-   MessageReadCount 可帶多筆的訊息查詢每一則訊息的已讀數量。

```java
List<String> msgIDs = new ArrayList<>();
msgIDs.add("msgIDA");
msgIDs.add("msgIDB");

imManager.getMessageHelper().queryMessageReadCount(transID, msgIDs))
    .subscribe(new Observer<LTQueryMessageReadCountResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTQueryMessageReadCountResponse queryMessageReadCountResponse) {
    	  //get LTQueryMessageReadCountResponse result
        }

        @Override
        public void onError(Throwable e) {
    	  if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

-   MessageReadUsers 可帶入指定訊息查詢此訊息的已讀清單。避免已讀清單資料量大，提供使用分批撈取的方式，`lastReadTime`可帶入上一批最後一個使用者的讀取時間，若是第一次請帶入`null`。

```java
imManager.getMessageHelper().queryMessageReadUsers(transID, msgID, lastReadTime, count))
    .subscribe(new Observer<LTQueryMessageReadUsersResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTQueryMessageReadUsersResponse queryMessageReadUsersResponse) {
        //get LTQueryMessageReadUsersResponse result
        }

        @Override
        public void onError(Throwable e) {
    	  if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

### Delete Message

本章節介紹關於訊息刪除相關的功能。

#### Delete a messages

刪除多筆指定訊息，只會刪除自己在伺服器上的訊息，其他成員的訊息仍然存在。

```java
List<String> msgIDs = new ArrayList<>();
msgIDs.add("msgIDA");
msgIDs.add("msgIDB");

imManager.getMessageHelper().deleteMessages(transID, msgIDs))
    .subscribe(new Observer<LTDeleteMessagesResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTDeleteMessagesResponse deleteMessagesResponse) {
    	  //get LTDeleteMessagesResponse result
        }

        @Override
        public void onError(Throwable e) {
    	  if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
	})
```

#### Delete channel messages

刪除指定聊天室的所有訊息，只會刪除自己在伺服器上的訊息，其他成員的訊息仍然存在。

```java
imManager.getMessageHelper().deleteChannelMessages(transID, chID))
    .subscribe(new Observer<LTDeleteChannelMessageResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTDeleteChannelMessageResponse deleteChannelMessageResponse) {
        //get LTDeleteChannelMessageResponse result
        }

        @Override
        public void onError(Throwable e) {
    	  if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

#### Delete all messages

刪除使用者的所有訊息，只會刪除自己在伺服器上的訊息，其他成員的訊息仍然存在。

```java
imManager.getMessageHelper().deleteAllMessages(transID))
    .subscribe(new Observer<LTDeleteAllMessagesResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTDeleteAllMessagesResponse deleteAllMessagesResponse) {
    	//get LTDeleteAllMessagesResponse result
        }

        @Override
        public void onError(Throwable e) {
    	  if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

### Recall messages

回收訊息功能，和刪除功能最大的差別在於，一但回收訊息後此聊天室的成員皆無法再取得此筆訊息。回收訊息目前設定只能回收使用者自己傳送的訊息，並提供可設定是否需要顯示回收系統訊息，可利用`silentMode`來設定是否需要顯示回收系統訊息。若設定`silentMode`為 true，此聊天室除了收到回收的訓令之外，原本的訊息將會被刪除。若希望可以顯示回收系統訊息，請設定`silentMode`為 false，此聊天室除了收到回收的訓令之外，原本的訊息將會保留，並提供回收狀態在此訊息的資訊中。

```java
imManager.getMessageHelper().recallMessages(transID, messageIDList, false))
    .subscribe(new Observer<LTRecallMessagesResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTRecallMessagesResponse recallMessagesResponse) {
        //get LTRecallMessagesResponse result
        }

        @Override
        public void onError(Throwable e) {
    	  if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

## User

使用者設定值，包含全域使用者暱稱、頭像資訊以及通知設定值...等。

```java
LTIMManager imManager = LTSDK.getInstance().getIMManager(userID);
LTUserHelper userHelper = imManager.getUserHelper();
// Call user functions...
```

### Set User

本章節介紹使用者本身的設定。

#### Set User Notification Setting

-   Notification Token 設定使用者每個裝置的專屬通知 Token。獲取 Token 方式請參考[Firebase Cloud Messaging](#firebase-cloud-messaging)

```java
imManager.getUserHelper().setUserPushToken(transID, key))
    .subscribe(new Observer<LTUserPushTokenResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTUserPushTokenResponse userPushTokenResponse) {
    	//get LTUserPushTokenResponse result
        }

        @Override
        public void onError(Throwable e) {
    	  if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

-   Notification Display 設定通知使否顯示發訊者名稱與訊息內容。

| Parameter     | Description                          |
| :------------ | :----------------------------------- |
| hidingSender  | 若為 false，則通知將不會有發訊者名稱 |
| hidingContent | 若為 false，則將不會有通知訊息內容   |

```java
boolean hidingSender = true;
boolean hidingContent = true;

imManager.getUserHelper().SetUserDeviceNotifyPreview(transID, hidingSender, hidingContent))
    .subscribe(new Observer<LTUserDeviceNotifyPreviewResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTUserDeviceNotifyPreviewResponse userDeviceNotifyPreviewResponse) {
            //get LTUserDeviceNotifyPreviewResponse result
        }

        @Override
        public void onError(Throwable e) {
            if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

-   Notification Sound 設定通知聲音音效資訊。

| Parameter | Description                             |
| :-------- | :-------------------------------------- |
| soundType | 這裡為音效型態，安卓請設定１為通知音效  |
| soundID   | 聲音音效資訊，可設定音效路徑或是音效 ID |

```java
int soundType = 1;
String soundID = "xxxxx.mp3";

imManager.getUserHelper().setDeviceNotifySound(transID, soundType, soundID))
    .subscribe(new Observer<LTUserDeviceNotifySoundResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTUserDeviceNotifySoundResponse userDeviceNotifySoundResponse) {
        //get LTUserDeviceNotifySoundResponse result
        }

        @Override
        public void onError(Throwable e) {
    	  if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

-   Notification Mute 設定通知靜音與勿擾模式。

| Parameter | Description |
| :-- | :-- |
| muteAll | 若為 true，則不會收到通知訊息 |
| time | 勿擾模式，請設定時間戳記，在此設定的時間之前將不會收到任何通知。另外需注意 muteAll 若設定為 true，勿擾模式將無作用。若無需使用請設定`null` |

```java
boolean muteAll = true;
Long time = null;

imManager.getUserHelper().setUserDeviceMute(transID, muteAll, time))
    .subscribe(new Observer<LTUserDeviceMuteResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTUserDeviceMuteResponse userDeviceMuteResponse) {
    	  //get LTUserDeviceMuteResponse result
        }

        @Override
        public void onError(Throwable e) {
    	  if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

#### Set User Profile

更改使用者本身的設定。若設定成功後，除了本身收到設定的結果外，所有聊天室的成員皆會收到使用者更改的設定內容。

-   User Nickname 使用者全域的暱稱

```java
imManager.getUserHelper().setUserNickname(transID, nickname)
    .subscribe(new Observer<LTSetUserProfileResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTSetUserProfileResponse setUserProfileResponse) {
        //get LTSetUserProfileResponse result
        }

        @Override
        public void onError(Throwable e) {
    	  if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

-   User Avatar 使用者全域的大頭照

設定大頭照

```java
Uri avatarUri = Uri.parse(Environment.getExternalStorageDirectory() + "/Download/image.jpg");

imManager.getUserHelper().setUserAvatar(transID, avatarUri)
    .subscribe(new Observer<LTUserProfileFileResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTUserProfileFileResponse userProfileFileResponse) {

            // 傳檔狀態
            if(userProfileFileResponse.getFileMessageStatus() == LTFileMessageStatus.STATUS_FILE) {
                LTFileTransferResult result = userProfileFileResponse.getFileTransferResults().get(0);
                LTFileType fileType = result.getFileType();
                LTStorageManager.StorageStatus status = result.getStatus();
                long loadingBytes = result.getLoadingBytes();
                long totalLength = result.getTotalLength();
            }
            //  訊息傳送成功狀態
            else if(userProfileFileResponse.getFileMessageStatus() == LTFileMessageStatus.STATUS_MESSAGE) {
                // get LTUserProfileFileResponse result (extends LTSetUserProfileResponse)
            }
        }

        @Override
        public void onError(Throwable e) {
    	  if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

刪除大頭照

```java

LTFileInfo fileInfo = setUserProfileResponse.getUserProfile().get("profileImageFileInfo");

imManager.getUserHelper().deleteUserAvatar(transID, fileInfo)
    .subscribe(new Observer<LTUserProfileFileResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTUserProfileFileResponse userProfileFileResponse) {
            //  檔案刪除成功狀態
            if(userProfileFileResponse.getFileMessageStatus() == LTFileMessageStatus.STATUS_MESSAGE) {
                // get LTUserProfileFileResponse result (extends LTSetUserProfileResponse)
            }
        }

        @Override
        public void onError(Throwable e) {
    	  if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });

```

### Get User

本章節介紹如何取得使用者本身的設定。

#### Get User Notification Setting

取得此裝置的通知相關的設定值。

```java
imManager.getUserHelper().queryDeviceNotify(transID))
    .subscribe(new Observer<LTQueryUserDeviceNotifyResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTQueryUserDeviceNotifyResponse queryUserDeviceNotifyResponse) {
    	// get LTQueryUserDeviceNotifyResponse result
        }

        @Override
        public void onError(Throwable e) {
    	  if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

#### Get User Profile

取得此使用者相關資訊，可選擇使用`userID`或者是用`phoneNumber`來查詢使用者資訊。提醒注意的部分，當使用`phoneNumber`請務必搭配`brandID`查詢，資料才會正確。

```java
List<String> userIDs = new ArrayList<>();
userIDs("userIDA");
userIDs("userIDB");

List<String> phoneNumbers = new ArrayList<>();
userIDs("phoneNumber1");
userIDs("phoneNumber2");

String brandID = "ltbrand";

imManager.getUserHelper().queryUserProfile(transID, userIDs, phoneNumbers, brandID))
    .subscribe(new Observer<LTQueryUserProfileResponse>() {
        @Override
        public void onSubscribe(Disposable d) {

        }

        @Override
        public void onNext(LTQueryUserProfileResponse queryUserProfileResponse) {
    	// get LTQueryUserProfileResponse result
        }

        @Override
        public void onError(Throwable e) {
    	  if(e instanceof LTErrorInfo) {
                int returnCode = ((LTErrorInfo) e).getReturnCode();
                String errorMessage = ((LTErrorInfo) e).getErrorMessage();
            }
        }

        @Override
        public void onComplete() {

        }
    });
```

## Receive Event

當設定連線時，請務必先註冊**LTIMManagerListener**，可呼叫`imManager.setManagerListener`來獲取**LTIMManagerListener**中的事件。提醒注意本裝置送出的的訊息，只會當下的回應收到，不會利用**LTIMManagerListener**再次收到。

```java
imManager.setManagerListener(new LTIMManagerListener() {

    //Common
    @Override
    public void onConnected(String userID) {
    }

    @Override
    public void onDisconnected(String userID) {
    }

    @Override
    public void onIncomingMessage(String toUserID, LTMessageResponse messageResponse) {
    }

    //Channel
    @Override
    public void onIncomingCreateChannel(String toUserID, LTCreateChannelResponse createChannelResponse) {
    }

    @Override
    public void onIncomingDismissChannel(String toUserID, LTDismissChannelResponse dismissChannelResponse) {
    }

    @Override
    public void onIncomingChannelPreference(String toUserID, LTChannelPreferenceResponse setChannelPreferenceResponse) {
    }

    @Override
    public void onIncomingChannelProfile(String toUserID, LTChannelProfileResponse setChannelProfileResponse) {
    }

    //Channel Member
    @Override
    public void onIncomingJoinChannel(String toUserID, LTJoinChannelResponse joinChannelResponse) {
    }

    @Override
    public void onIncomingInviteMember(String toUserID, LTInviteMemberResponse inviteMemberResponse) {
    }

    @Override
    public void onIncomingKickMember(String toUserID, LTKickMemberResponse kickMemberResponse) {
    }

    @Override
    public void onIncomingLeaveChannel(String toUserID, LTLeaveChannelResponse leaveChannelResponse) {
    }

    @Override
    public void onIncomingMemberRole(String toUserID, LTMemberRoleResponse setMemberRoleResponse) {
    }

    //Message
    @Override
    public void onIncomingSendMessage(String toUserID, LTSendMessageResponse sendMessageResponse) {
    }

    @Override
    public void onIncomingScheduledMessage(String toUserID, LTScheduledMessageResponse scheduledMessageResponse) {
    }

    @Override
    public void onIncomingScheduledInDueTimeMessage(String toUserID, LTScheduledInDueTimeMessageResponse scheduledInDueTimeMessageResponse) {
    }

    @Override
    public void onIncomingMarkRead(String toUserID, LTMarkReadResponse markReadResponse) {
    }

    @Override
    public void onIncomingDeleteAllMessages(String toUserID, LTDeleteAllMessagesResponse deleteAllMessagesResponse) {
    }

    @Override
    public void onIncomingDeleteChannelMessage(String toUserID, LTDeleteChannelMessageResponse deleteChannelMessageResponse) {
    }

    @Override
    public void onIncomingDeleteMessages(String toUserID, LTDeleteMessagesResponse deleteMessagesResponse) {
    }

    @Override
    public void onIncomingRecallMessage(String toUserID, LTRecallMessagesResponse recallMessagesResponse) {
    }

    //User
    @Override
    public void onIncomingSetUserProfile(String toUserID, LTSetUserProfileResponse setUserProfileResponse) {
    }

    @Override
    public void onIncomingModifyUserProfile(String toUserID, LTModifyUserProfileResponse modifyUserProfileResponse) {
    }
});
```

### Common

| Method              | Description        |
| :------------------ | :----------------- |
| onConnected()       | 已與伺服器連線     |
| onDisconnected()    | 已與伺服器中斷連線 |
| onIncomingMessage() | 一般訓令事件       |

#### LTMessageResponse

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| transID | String | The client's message ID in UUID format |
| chID | String | The channel ID |
| chType | LTChannelType | ChannelType are declared as Enum type, which represents different kind of Chatroom |
| msgContent | String | The message content |
| msgCategory | String | The specific message category. |
| msgID | String | The IM server generated message ID in UUID(type 1) format. |
| msgType | [LTMessageType](#appendix-b-message-type-list) | The message content by message format. |
| senderID | String | The user ID who send the message. |
| senderNickname | String | The user nickname who send the message. |
| senderProfileID | String | The user profileID who send the message. |
| sendTime | long | The message's sent time. |
| encrypted | boolean | The value is true while the message content is encrypted in database. otherwise, the value is false. |
| isRead | boolean | Whether the message is read by the receiver. |
| extInfo | String | The additional information. |
| recallStatus | [LTRecallStatus](#ltrecallstatus) | It includes the following info: Mode, Time and the one who asks Recall message. |

### Channel

| Method | Description |
| :-- | :-- |
| onIncomingCreateChannel() | 收到聊天室建立的事件，可呼叫[Channel Information](#channel-information)來同步新建立的聊天室資訊 |
| onIncomingDismissChannel() | 收到聊天室解散的事件 |
| onIncomingChannelPreference() | 收到聊天室中專屬的設定值。一般來說會再多裝置設定時，收到同一個使用者更改的設定 |
| onIncomingChannelProfile() | 收到聊天室共同設定 |

#### LTCreateChannelResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| members | Set<[LTMemberProfile](#ltmemberprofile)> | The member's info. |
| count | int | The count for create members |

#### LTDismissChannelResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

#### LTChannelPreferenceResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| channelPreference | [LTChannelPreference](#ltchannelpreference) | The `channelPreference` contains changed information, |

#### LTChannelProfileResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| channelProfile | Map\<String, Object\> | The `channelProfile` contains changed information, including `subject` and `profileImageFileInfo`. |

### Channel Member

| Method                   | Description                          |
| :----------------------- | :----------------------------------- |
| onIncomingJoinChannel()  | 收到聊天室其他成員加入的事件         |
| onIncomingInviteMember() | 收到聊天室其他成員與自己被邀請的事件 |
| onIncomingKickMember()   | 收到聊天室其他成員與自己被踢除的事件 |
| onIncomingLeaveChannel() | 收到聊天室其他成員離開的事件         |
| onIncomingMemberRole()   | 收到聊天室成員角色改變的事件         |

#### LTJoinChannelResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| members | Set\<[LTMemberProfile](#ltmemberprofile)\> | The member's info. |

#### LTInviteMemberResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| members | Set<[LTMemberProfile](#ltmemberprofile)> | The member's info. |
| count | int | The count for invite members |

#### LTKickMemberResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| members | Set<[LTMemberProfile](#ltmemberprofile)> | The member's info. |
| count | int | The count for kick members |
| reason | int | The reason for kick members, default is 0 |
| memberCount | int | The count for channel members |
| channelUpdate | int | The update tag, that is bit value. Bit0 is member role. Bit1 is channel preference. Bit2 is member changed. |

#### LTLeaveChannelResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| members | Set<[LTMemberProfile](#ltmemberprofile)> | The member's info. |
| channelUpdate | int | The update tag, that is bit value. Bit0 is member role. Bit1 is channel preference. Bit2 is member changed. |
| memberCount | int | The count for channel members |

#### LTMemberRoleResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| roleID | [LTChannelRole](#ltchannelrole) | The one's role ID in the channel. |
| memberUserID | String | The user ID who role changed. |

### Message

| Method | Description |
| :-- | :-- |
| onIncomingSendMessage() | 收到成員傳送的訊息事件 |
| onIncomingScheduledMessage() | 收到多裝置的同一個使用者傳送的預約訊息 |
| onIncomingScheduledInDueTimeMessage() | 收到自己發送的預約訊息已傳送出的事件 |
| onIncomingMarkRead() | 收到成員的已讀事件 |
| onIncomingDeleteAllMessages() | 收到多裝置的同一個使用者刪除所有訊息的事件 |
| onIncomingDeleteChannelMessage() | 收到多裝置的同一個使用者刪除聊天室訊息的事件 |
| onIncomingDeleteMessages() | 收到多裝置的同一個使用者刪除指定訊息的事件 |
| onIncomingRecallMessage() | 收到成員回收訊息的事件 |

#### LTSendMessageResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| message | [LTMessage](#ltmessage) | 呼叫 [Send Message](#send-message)、[Send Broadcast Messages](#send-broadcast-messages) 以及 [Send Scheduled Messages](#send-scheduled-messages) 傳送出去的訊息 |

#### LTScheduledMessageResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| chIDs | NSArray | The channel ID array. |
| messages | NSArray | The array of message ([LTMessage](#ltmessage)) which needed to be schedule. |
| timeToSend | long long | The time when the message will send out in millisecond. |

#### LTScheduledInDueTimeMessageResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

#### LTMarkReadResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

#### LTDeleteAllMessagesResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

#### LTDeleteChannelMessagesResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

#### LTDeleteMessagesResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

#### LTRecallMessageResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

| Parameter   | Definition | Description        |
| :---------- | :--------- | :----------------- |
| recallMsgID | String     | 被回收訊息的 msgID |

### User

| Method | Description |
| :-- | :-- |
| onIncomingSetUserProfile() | 收到多裝置的同一個使用者更改本身個人資料的事件 |
| onIncomingModifyUserProfile() | 收到聊天室成員之中更改資料的事件 |

#### LTSetUserProfileResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| userProfile | Map\<String, Object\> | 當次更改的[User Profile](#user-profile)值 |

#### LTModifyUserProfileResponse

繼承 **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| userProfiles | List\<Map\<String, Object\>\> | 聊天室其他成員更改的 [User Profile](#user-profile) 值 |

**<span id="user-profile">User Profile</span>** : 為 Map 格式如下，當下有被更動的才會取得。

| key            | Definition | Description        |
| :------------- | :--------- | :----------------- |
| userID         | String     | 使用者 ID          |
| nickname       | String     | 使用者暱稱         |
| profileImageFileInfo | LTFileInfo(#ltfileinfo)    | 使者者 Avatar 資訊 |

## Appendix A. Other Error Code List

While requesting **failed**, you will get **ErrorResponse** which includes **returnCode** and **returnMessage** The details are as below.

| ReturnCode | Description                                                  |
| :--------: | :----------------------------------------------------------- |
|     1      | Wrong User Account or Password                               |
|     2      | Wrong Function                                               |
|     3      | Error happened in database                                   |
|     4      | PHP Error                                                    |
|     5      | Wrong EndPoint                                               |
|     6      | Error happened in Data Format                                |
|     7      | The function is unavailable                                  |
|     8      | Wrong number format                                          |
|    1000    | Unknown error                                                |
|    1001    | Not supported format                                         |
|    1002    | Not supported command                                        |
|    1003    | IM database server access error                              |
|    1004    | Request too many times for the API                           |
|    1008    | Schedule time error                                          |
|    1101    | Channel ID's format error                                    |
|    1102    | Channel ID is already exist                                  |
|    1103    | Channel type is not supported                                |
|    1104    | Channel type is not matched                                  |
|    1105    | Channel ID is not exist                                      |
|    1106    | UserID is not in the channel                                 |
|    1107    | The user is without privilege                                |
|    1108    | The number of members in the channel is below the limit      |
|    1109    | No user to kick                                              |
|    1110    | No user to ban                                               |
|    1111    | No channel preference to set                                 |
|    1112    | No user profile to set                                       |
|    1113    | The message is not belong to the user                        |
|    1114    | One of the members is not in the channel                     |
|    1115    | The number of members in the channel is over the upper limit |
|    1116    | The user is not invited in the channel                       |
|    1117    | The user is not in the channel                               |
|    1118    | Message not exist                                            |
|    1119    | Message is already sent or exist                             |
|    1120    | No user privilege to set                                     |
|    1121    | Time expire                                                  |
|    1122    | More than the number of voting                               |
|    6000    | Not current user                                             |

## Appendix B. Message Type List

Y: Yes <br> N: No <br> O: Option <br>

| msgType | insertMessage | insertLastMessage | sendXMPP | sendNotification | addUnreadCount | Comment |
| :-: | :-: | :-: | :-: | :-: | :-: | :-- |
| 1 | Y | Y | Y | Y | Y | Text message |
| 2 | Y | Y | Y | Y | Y | Sticker message |
| 3 | Y | Y | Y | Y | Y | Picture message |
| 4 | Y | Y | Y | Y | Y | Video message |
| 5 | Y | Y | Y | Y | Y | Voice message |
| 6 | Y | Y | Y | Y | Y | Contact message |
| 7 | Y | Y | Y | Y | Y | Location message |
| 8 | Y | Y | Y | Y | Y | File message |
| 101 | Y | Y | Y | Y | Y | Voting's Text Content |
| 102 | Y | N | Y | Y | Y | Voting's Sticker Content |
| 103 | Y | N | Y | Y | Y | Voting's Picture Content |
| 104 | Y | N | Y | Y | Y | Voting's Video Content |
| 105 | Y | N | Y | Y | Y | Voting's Voice Content |
| 106 | Y | N | Y | Y | Y | Voting's Contact Content |
| 107 | Y | N | Y | Y | Y | Voting's Location Content |
| 108 | Y | N | Y | Y | Y | Voting's File Content |
| 201 | N | N | N | N | N | Send scheduled message |
| 202 | Y | N | Y | Y | N | Scheduled message is sent |
| 203 | N | N | N | N | N | Send scheduled voting message |
| 204 | Y | N | Y | Y | N | Scheduled voting message is sent |
| 205 | Y | N | Y | Y | N | Send scheduled message |
| 206 | N | N | N | N | N | Scheduled message is sent |
| 221 | Y | Y | Y | N | N | user take a screenshot |
| 222 | Y | Y | Y | N | N | user make a copy of message |
| 223 | Y | Y | Y | N | N | user transfer messages to others |
| 224 | Y | Y | Y | N | N | user saved the channel's message, image, video, voice or file |
| 1001 | Y | N | Y | Y | - | create a channel |
| 1002 | Y | N | Y | N | N | dismiss a channel |
| 1003 | Y | N | Y | N | N | leave a channel |
| 1004 | Y | N | Y | Y | N | invite members to join a channel |
| 1005 | Y | N | Y | N | N | respond a channel invitation |
| 1006 | Y | N | Y | N | N | kick a member out of a channel |
| 1007 | Y | N | Y | N | N | ban a member |
| 1008 | Y | N | Y | N | N | change channel's subject |
| 1009 | Y | N | Y | N | N | change channel's profile image id |
| 1010 | Y | N | Y | N | N | change members' affiliation and role |
| 1011 | Y | N | Y | N | N | change user's preference of a channel |
| 1012 | Y | N | Y | N | N | change user's profile image id and nick name |
| 1013 | Y | N | Y | N | N | change user's profile image id and nick name in a specific channel |
| 1015 | Y | N | Y | N | N | change a channel's background image id |
| 1017 | Y | N | Y | N | N | create a channel |
| 1018 | Y | N | Y | N | N | change members' affiliation and role |
| 1101 | - | N | Y | N | N | mark read of a specific channel |
| 1102 | Y | N | Y | N | N | delete a message |
| 1103 | Y | N | Y | N | N | delete a channel's all messages |
| 1104 | Y | N | Y | N | N | recall a message |
| 1105 | Y | N | Y | N | N | delete user's all channels' all messages |
| 1106 | N | N | Y | N | N | push channel active user |
| 1201 | Y | N | Y | N | N | create a public news channel |
| 1202 | Y | N | Y | Y | Y | create a public news message of the specific channel |
| 1203 | Y | N | Y | N | N | create a corporation news channel |
| 1204 | Y | N | Y | Y | Y | create a corporation news message of the specifi corporation channel |
| 2001 | Y | N | Y | Y | N | incoming call notification |
| 2002 | Y | N | Y | Y | N | call cancel notification |
| 2003 | Y | N | Y | N | N | send CDR (call log) message |
| 3000 | O | O | O | O | O | Send customized message with attribute |
| 3001 | Y | N | Y | N | N | Customized message without notification and insert last message |
| 3002 | Y | Y | Y | Y | N | Custom message |

## Appendix C. Object List

### LTChannelRole

| Enum                     | Description                              |
| :----------------------- | :--------------------------------------- |
| LTChannelRoleNone        | Number 0, None member                    |
| LTChannelRoleOutcast     | Number 1, Outcast member                 |
| LTChannelRoleInvieted    | Number 2, Visited and not to join member |
| LTChannelRoleParticipant | Number 4, General member                 |
| LTChannelRoleModerator   | Number 8, Moderator member               |
| LTChannelRoleAdmin       | Number 16, admin member                  |

### LTRecallStatus

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| silentMode | boolean | The server will response True or False accordingly. If True, the server will delete both message content and system log. If False, the server will delete message content but response system log. |
| recallTime | long | The timestamp when the message is recalled |
| recallBy | String | The userID who recall the message |
| senderNickname | String | The user nickName who recall the message |

### LTMemberProfile

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| profileImageFileInfo | LTFileInfo(#ltfileinfo) | The user's profile info. |
| nickname | String | The user nickname. |
| userID | String | One of queried userID. UserID, provided by LT, is an unique code for each user. |
| phoneNumber | String | The phone number corresponds to the parameter **userID** behind. |

### LTChannelPreference

| Parameter  | Definition | Description                                |
| :--------- | :--------- | :----------------------------------------- |
| ringToneID | String     | The ringTone ID in channel for the user.   |
| nickname   | String     | The user's nickname in cahnnel.            |
| isMute     | String     | The sound is mute in channel for the user. |

### LTMessage

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| transID | String | The client's message ID in UUID(type 1) format. |
| chID | String | The channel ID. |
| chType | LTChannelType | The channel type. |
| replyMessage | [LTReplyMessage](#ltreplymessage) | The object of the Mother message. |
| parentMsgID | String | The message ID of the Mother message. |

### LTThumbnailFileMessage

繼承 **[LTFileMessage](#ltfilemessage)**

| Parameter       | Definition            | Description                  |
| :-------------- | :-------------------- | :--------------------------- |
| thumbnailFileInfo | LTFileInfo          | The server return thumbnail file info.|

### LTFileMessage

繼承 **[LTMessage](#ltmessage)**

| Parameter       | Definition            | Description                  |
| :-------------- | :-------------------- | :--------------------------- |
| displayFileName | String                | The display name for file.   |
| extInfo         | Map\<String, Object\> | Set custom info              |
| fileInfo        | LTFileInfo            | The server return file info. |

### LTReplyMessage

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| senderID | String | The sender ID of the Mother message. |
| senderNickname | String | The sender ID’s nickname of the Mother message. |
| msgID | String | The message ID of the Mother message. |
| msgType | LTChannelType | The message types of the Mother message. |
| sendTime | Long | The send time of the Mother message. |
| displayText | String | The display content of the Mother message. |
| extInfo | Map\<String, Object\> | The ext info of the Mother message. |
| tagUsers | List\<LTTagUser\> | The tag user of the Mother message(LTTextMessage only). |
| fileInfo | LTFileInfo | The file info of the Mother message(LTFileMessage only). |
| thumbnailFileInfo | LTFileInfo | The thumbnail file info of the Mother message(LTFileMessage only). |
| location | LTLocation | The location info of the Mother message(LTLocationMessage only). |

### LTTextMessage

繼承 **[LTMessage](#ltmessage)**

| Parameter  | Definition            | Description      |
| :--------- | :-------------------- | :--------------- |
| msgContent | String                | 訊息內容         |
| extInfo    | Map\<String, Object\> | 可輸入自定義資訊 |
| tagUsers   | List\<LTTagUser\>     | 標記的成員       |

### LTStickerMessage

繼承 **[LTMessage](#ltmessage)**

| Parameter  | Definition            | Description      |
| :--------- | :-------------------- | :--------------- |
| msgContent | String                | 貼圖內容         |
| extInfo    | Map\<String, Object\> | 可輸入自定義資訊 |

### LTImageMessage

繼承 **[LTThumbnailFileMessage](#ltthumbnailfilemessage)**

| Parameter    | Definition | Description |
| :----------- | :--------- | :---------- |
| imageUri     | Uri        | 原圖路徑    |
| thumbnailUri | Uri        | 縮圖路徑    |

### LTVideoMessage

繼承 **[LTThumbnailFileMessage](#ltthumbnailfilemessage)**

| Parameter    | Definition | Description |
| :----------- | :--------- | :---------- |
| videoUri     | Uri        | 影片路徑    |
| thumbnailUri | Uri        | 縮圖路徑    |

### LTVoiceMessage

繼承 **[LTFileMessage](#ltfilemessage)**

| Parameter | Definition | Description |
| :-------- | :--------- | :---------- |
| voiceUri  | Uri        | 語音檔路徑  |

### LTContactMessage

繼承 **[LTThumbnailFileMessage](#ltthumbnailfilemessage)**

| Parameter    | Definition | Description    |
| :----------- | :--------- | :------------- |
| contactUri   | Uri        | 聯絡人檔案路徑 |
| thumbnailUri | Uri        | 縮圖路徑       |

### LTLocationMessage

繼承 **[LTMessage](#ltmessage)**

| Parameter | Definition            | Description      |
| :-------- | :-------------------- | :--------------- |
| location  | LTLocation            | 地圖資訊         |
| extInfo   | Map\<String, Object\> | 可輸入自定義資訊 |

### LTDocumentMessage

繼承 **[LTFileMessage](#ltfilemessage)**

| Parameter | Definition | Description  |
| :-------- | :--------- | :----------- |
| fileUri   | Uri        | 文件檔案資訊 |

### LTCustomMessage

繼承 **[LTMessage](#ltmessage)**

| Parameter   | Definition            | Description      |
| :---------- | :-------------------- | :--------------- |
| msgContent  | String                | 訊息內容         |
| msgCategory | String                | 訊息分類         |
| extInfo     | Map\<String, Object\> | 可輸入自定義資訊 |
| attributes  | Long                  | 訊息的行為模式   |

### LTFileInfo

| Parameter            | Definition | Description      |
| :------------------- | :--------- | :--------------- |
| isExist             |  BOOL   | 檔案是否存在        |
| filename             | String     | 檔案名稱         |
| remoteFilePath       | String     | 檔案遠端路徑     |
| fileContentType      | String     | 檔案類型         |
| fileSize             | Long       | 檔案大小         |
| storageDomain        | String     | LT 雲端 domain   |
| storageID            | String     | LT 雲端 ID       |
| endpointName         | String     | LT 雲端 endpoint |

### LTLocation

| Parameter | Definition | Description |
| :-------- | :--------- | :---------- |
| address   | String     | 位置地址    |
| latitude  | double     | 位置緯度    |
| longitude | double     | 位置經度    |

### LTTagUser

| Parameter | Definition | Description       |
| :-------- | :--------- | :---------------- |
| start     | long       | 標記位置起始      |
| length    | long       | 標記位置長度      |
| userID    | String     | 標記成員的 UserID |
