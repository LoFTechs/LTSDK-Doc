# LT Meet SDK Android Document

---

Version: 5.1.9</br> Date: 2021/07/04

---

## Overview

With LT SDK, you can build your own customized application with Call, IM and Meet function. This documentary provides a guideline that demonstrates how to build and configure an in-app message and call using LT SDK.

1. Before start, you must create user account/login/password to continue the following process if you want to host private meeting. Public meeting allows you to invite anyone to join without user account.
2. In order to connect to the LT service please read the [Authentication](#authentication).
3. Support Version:

    Android 5.0 (API level 21) or higher ;</br>
    Java 8 or higher ;</br>
    Gradle 5.4.1 or higher ;</br>
    Support Program language：Java, Kotlin

4. The API's each parameter is described as the format:**(Necessity, DataType)**. The first character indicates the necessity of this parameter, and the second is the data type of the parameter.

    - **DataType(s)**: String, Number, Bool
        - **String**: String represents the string data type.
        - **Number**: Number represents the numeric data type.
        - **Bool**: Boolean represents the Boolean data type.
    Whether it is Request or Response's values, if the parameter is not provided, the default value of the parameter is based on the data type as follows:
        - **String**: indicates that the default value of this parameter is empty string.
        - **Number**: indicates that the default value of this parameter is 0.
        - **Bool**: Indicates that the default value of this parameter is false.

## Try the sample app

The sample application has the core functions of our LT SDK. Download the app from our GitHub repository to get an idea of what you can build with the SDK and start building in your project.

[Download sample app](https://github.com/LoFTechs/LTSample-Android-Java)

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
// Least requirement for Meet only applications
implementation "com.loftechs.sdk:lt:$LTSDK_version-SNAPSHOT"
implementation "com.loftechs.sdk:meet:$LTSDK_version-SNAPSHOT"

```

Step 3. Grant system permissions in your app level AndroidManifest.xml:

```java
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" android:requestLegacyExternalStorage="true" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" android:requestLegacyExternalStorage="true" />
    <uses-permission android:name="android.permission.VIBRATE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
```

## Public meeting (easy mode)

### Create meeting without user authentication

Use variables from app/gradle.properties to host a public meeting allowing users to join via only url link. Host can still use password to lock the meeting room.

```java
LTMeetSDK meetSDK = LTMeetSDK.getInstance();

LTMeetPrivate meetInfo = new LTMeetPrivate(
                    subject, 0, 3600
            );

meetInfo.chID = "the channel ID you created using LTIMSDK";
            
LTMeetSDKInitParam initParams = new LTMeetSDKInitParam(
                    BuildConfig.License_Key,
                    BuildConfig.Auth_API,
                    BuildConfig.Public_Meet_Domain, //public
                    "not used here",
                    "not used here"
            );

meetSDK.initialize(null);

//init SDK and create meeting
meetSDK.createMeeting(this, initParams, meetInfo, this).subscribe(handleMeetSDK(meetInfo));

```

### For participants 

```java
// mostly same as above, but specify meetID invited from the host
meetInfo.meetID = getMeetID();

meetSDK.enterMeeting(this, initParams, meetInfo, this).subscribe(handleMeetSDK(meetInfo));

```

## Private Meeting (secure mode)

### Create meeting with user authentication

To use private mode, you must leverage IM\_SDK to establish channels beforehand, either 1-to-1 channel or a group channel or a corporate channel. ChannelID is a required param. Specify channelID and each participant will be checked whether he is a valid member.

```java
LTMeetSDK meetSDK = LTMeetSDK.getInstance();

LTMeetPrivate meetInfo = new LTMeetPrivate(
                    subject, 0, 3600
            );

meetInfo.chID = "the channel ID you created using LTIMSDK";
                        
//With LTSDK you'll able to create the first user and login the first user. See sample app for more details.            

AccountEntity firstAccount = AccountPreferenceHelper.getInstance().geFirstAccount();

            LTMeetSDKInitParam initParams = new LTMeetSDKInitParam(
                    BuildConfig.License_Key,
                    BuildConfig.Auth_API,
                    BuildConfig.Private_Meet_Domain, //private
                    BuildConfig.IM_API,
                    firstAccount.getAccount()
            );

            UserEntity user = new UserEntity();
            user.setUserID(firstAccount.getUserID());
            user.setUuid(firstAccount.getUuid());

meetSDK.initialize(user);

meetSDK.createMeeting(this, initParams, meetInfo, this).subscribe(handleMeetSDK(meetInfo));

```

### For participants 

```java
// mostly same as above, but specify meetID invited from the host
// meetInfo is an instance of LTMeetPrivate

meetInfo.meetID = getMeetID();

meetSDK.enterMeeting(this, initParams, meetInfo, this).subscribe(handleMeetSDK(meetInfo));

```

#### Terminology

| Parameter  | Definition | Detail                      |
| :--------- | :--------- | :-------------------------- |
| context    | Context    | Android context object      |
| licenseKey | String     | LT License key              |
| brandID    | String     | Your customized brand       |
| auth api   | String     | LT authenticate url         |
| public meet domain   | String     | LT managed video conference services for customers who has existing user system        |
| private meet domain   | String     | LT managed video conference services for customers who needs new user/channel system        |
| userID     | String     | LT unique user ID after registration           |
| uuid       | String     | LT unique user authenticate code obtained after registration|

## Receive Event

Use ***subscribe*** to handle results of ***createMeeting*** and ***enterMeeting***. See [RxJava Observable and subscribe](https://github.com/ReactiveX/RxJava)

On the other hand, refer to MeetActivity from the sample app on how to implement **LTMeetListener** interface, which allows you capture meet ending events.

```java
meetManager.setManagerListener(new LTMeetListener() {

	@Override
    public void onMeetTerminate() {
        LTLog.d(TAG, "onMeetTerminate");
    }

    @Override
    public void onMeetError(LTMeetErrorInfo errorInfo) {
		 LTLog.e(TAG, "onMeetError");
    }
});
```