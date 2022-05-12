# LT IM SDK iOS Document

<sub>Last update time: 2022/05/10</sub>

---

## Overview

With LT SDK, you can build your own customized application with Call and IM function. This documentary provides a guideline that demonstrates how to build and configure an in-app message and call using LT SDK.

1. At the first beginning, you must create user account/login/password to continue the following process.
2. In order to connect to the LT service please read the [Authentication](#authentication).
3. Support Version：

    iOS 10.0 above ;</br> Support Program language：objective c, swift

## Try the sample app

The sample application has the core functions of our LT SDK. Download the app from our GitHub repository to get an idea of what you can build with the SDK and start building in your project.

[Download sample app](https://github.com/LoFTechs/LTSample-iOS-Swift)

## Getting started

### Import SDK

Step1. You can install LT SDK for iOS through `cocoapods`.

To install the pod, add following line to your Podfile:

```
pod 'LTSDK'
pod 'LTCallSDK'
pod 'LTIMSDK'
```

Step2. Install LT SDK

```
pod install
```

## Authentication

### Init SDK

In the beginning, you must initialize **LTSDK** instance by passing the **LTSDKOptions** of your application as an argument to a parameter in the `initWithOptions:completion:` method. When initializing your App, you need to call `initWithOptions:completion:` in the `application:didFinishLaunchingWithOptions:` method of the application class.

Note: Before switching users, be sure to call [Clean SDK](#clean-sdk).

```objectivec
LTSDKOptions *options = [[LTSDKOptions alloc] init];
options.licenseKey = @"licenseKey";
options.url = @"https://xxx.xxx.net";
options.userID = userID;
options.uuid = uuid;

[LTSDK initWithOptions:options completion:^(LTResponse * _Nonnull response) {
    if (response.returnCode == LTReturnCodeSuccess) {
        //
    } else {
        //
    }
}];
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

You can get user's information by calling `getUsersWithCompletion:` completion method.

```objectivec
[LTSDK getUsersWithCompletion:^(LTResponse * _Nonnull response, NSArray<LTUser *> * _Nullable users) {
    if (response.returnCode == LTReturnCodeSuccess) {
        for (LTUser *user in users) {
            //
        }
    } else {
        //
    }
}];
```

#### The definition of LTusers parameters

| Parameter   | Definition | Detail                            |
| :---------- | :--------- | :-------------------------------- |
| userID      | String     | LT unique user ID                 |
| uuid        | String     | LT unique user authenticate       |
| phoneNumber | String     | User's phone number               |
| semiUID     | String     | Customized unique key to register |
| deviceID    | String     | LT unique device ID               |
| accountSrc  | String     | LT account source                 |

### Get user status

Get the status of other users through their respective phoneNumbers or semiUIDs.

1. query with phonenumbers, use `getUserStatusWithPhoneNumbers: completion:` method.

```objectivec
[LTSDK getUserStatusWithPhoneNumbers:@[@"phonenumber1",@"phonenumber2"] completion:(void (^)(LTResponse * _Nonnull response, NSArray<LTUserStatus *> * _Nullable userStatuses)) {
    if (response.returnCode == LTReturnCodeSuccess) {
        for (LTUserStatus *userStatus in userStatuses) {

        }
    }
}];
```

2. query with semiUIDs, using `getUserStatusWithSemiUIDs: completion:` method.

```objectivec
[LTSDK getUserStatusWithSemiUIDs:@[@"semiUID1",@"semiUID2"] completion:(void (^)(LTResponse * _Nonnull response, NSArray<LTUserStatus *> * _Nullable userStatuses)) {
    if (response.returnCode == LTReturnCodeSuccess) {
        for (LTUserStatus *userStatus in userStatuses) {

        }
    }
}];
```

#### LTUserStatus parameters description

-   **userID** _(M, String)_: LT unique user ID.
-   **phoneNumber** _(O, String)_: user's phoneNumber.
-   **email** _(O, String)_: LT user's email.
-   **semiUID** _(C, String)_: LT unique semiUID.
-   **brandID** _(M, String)_: LT user brand.
-   **corpID** _(M, String)_: LT user corpID.
-   **canVOIP** _(M, Bool)_: user enable VoIP feature.
-   **canIM** _(M, Bool)_: user enable IM feature.

### Clean SDK

When your App was logged in with different users or when the return code of `initWithOptions` from `LTErrorInfo` is 6000, be sure to call `clean`.

```objectivec
[LTSDK initWithOptions:options completion:^(LTResponse * _Nonnull response) {
    if (response.returnCode == LTReturnCodeNotCurrentUser) {
        [LTSDK clean];
    }
}];
```

## APNS

Apple Push Notification service (APNs) is the centerpiece of the remote notifications feature. It is a robust, secure, and highly efficient service for app developers to propagate information to iOS (and, indirectly, watchOS), tvOS, and macOS devices. [See More](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)

### Generate your Apple Push Notification Push SSL Certificate

**_Step 1_** : Open [Apple developer website](https://developer.apple.com)

![Imgur](images/apns-1.png)

**_Step 2_** : Click "Account" &gt;&gt; "Certificates, IDs & Profiles".

![Imgur](images/apns-2.png)

**_Step 3_** : Select the type of Apple Push Notification Push SSL Certificate.

![Imgur](images/apns-3.png)

-   For Apple Push Notification Push SSL Certificate, please select "Apple Push Notification service SSL (Sandbox & Production)".
-   For VOIP Apple Push Notification Push SSL Certificate, please select "VoIP Services Certificate".

**_Step 4_** : Select your APP ID (For example,select com.loftechs.helper).

![Imgur](images/apns-4.png)

**_Step 5_** : Based on the description of Certificate Assistant, create Certificate Request.

![Imgur](images/apns-5-1.png)

![Imgur](images/apns-5-2.png)

**_Step 6_** : Upload Certificate Request that was created previously.

![Imgur](images/apns-6.png)

**_Step 7_** : Download and double click the certification and add it to Keychain Access.

![Imgur](images/apns-7.png)

### Provide APNS .p12 certificate

**_Step 1_** : Launch the Keychain Access application (Application -&gt; Utilities -&gt; Keychain Access -&gt; Certificates) in your Mac OS X and Generate APNS .p12 certificate.

Go to Keychain Access, select login keychain and My Certificate from side menu. Find app certificate and right click to export it

![Imgur](images/apns-p12.png)

**_Step 2_** : Provide APNS .p12 certificate to Loftech. Otherwise you can't send APNS.

### Enable the Push Notifications Capability

To add the required entitlements to your app, enable the Push Notifications capability in your Xcode project, as shown in Figure 1. Enabling this option in iOS adds the APS Environment Entitlement to the app. In macOS, it adds the APS Environment (macOS) Entitlement. See Enable push notifications in Xcode help for more information.

![Imgur](images/apns-enable.png)

### Register Your App and Retrieve Your App's Device Token

Register your app with APNs and receive a globally unique device token, which is effectively the address of your app on the current device. Your provider server must have this token before it can deliver notifications to the device.

```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
    [center requestAuthorizationWithOptions:(UNAuthorizationOptionAlert + UNAuthorizationOptionSound + UNAuthorizationOptionBadge) completionHandler:^(BOOL granted, NSError * _Nullable error) {

        if (grand) { // Request Authorization Success
            dispatch_async(dispatch_get_main_queue(), ^{
                [[UIApplication sharedApplication] registerForRemoteNotifications];
            });
        }

    }];

    return YES;
}

- (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
    //Send DeviceToken To Server
}

- (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error {
    // Try again later.
}
```

### Update APNS Token to LT Server

Please always keep updating APNS Token. Otherwise, the users may not be able to receive messages or call notification.

```objectivec
- (void)pushRegistry:(PKPushRegistry *)registry didUpdatePushCredentials:(PKPushCredentials *)credentials forType:(NSString *)type {
    NSString *token = [self hexadecimalStringFromData:credentials.token];
    [LTSDK updateNotificationKeyWithAPNSToken:@"" voipToken:token cleanOld:NO completion:^(LTResponse * _Nonnull response) {
        if (response.returnCode == 0) {
        } else {
        }
    }];
}

- (NSString *)hexadecimalStringFromData:(NSData *)data {
    NSUInteger dataLength = data.length;
    if (dataLength == 0) {
        return nil;
    }

    const unsigned char *dataBuffer = data.bytes;
    NSMutableString *hexString  = [NSMutableString stringWithCapacity:(dataLength * 2)];
    for (int i = 0; i < dataLength; ++i) {
        [hexString appendFormat:@"%02x", dataBuffer[i]];
    }
    return [NSString stringWithFormat:@"%@",hexString];
}
```

#### Request parameters description

-   **apnsToken** _(O, String)_ : Member Device Token
-   **voipToken** _(M, String)_ : Member Device PushKit Token.
-   **cleanOld** _(O, boolean)_ : Clean the tokens of previous devices that login using the same userID.

## Start IM

Please use `[LTSDK getIMManagerWithUserID:]` to get **LTIMManager** object, and use **LTIMManager** object to operate connection related services. After a call to the API, results may be obtained from the **Block** and [Receive Event](#receive-event). Be sure bring **transID** on every query, The **transID** is an exclusive ID for this API.

```objectivec
LTIMManager *manager = [LTSDK getIMManagerWithUserID:user.userID];
if (manager) {
    // Susscess to get LTIMManager.
} else {
    // LTUser is invalid or not able to use IM service.
}
```

### Connect

Before use any IM related API services, Please do use **LTIMManager** to query `connect`. Get results from **Block**. Start using IM API services after successful connection. Success would trigger [Receive Event - Common](#common) method [`LTIMManagerConnectedWithReceiver:`](#common), and trigger [`LTIMManagerDisconnectedWithReceiver:error`](#common) if failed.

```objectivec
[manager connectWithCompletion:^(BOOL success, LTErrorInfo * _Nullable error) {
    if (error) {
        //connect error
        return;
    }
    //connect success
}];
BOOL success = [manager connect];
if (success) {
    // Start to connect or be connecting server.
} else {
    // LTUser is neccessary to be updated.
}
```

Receive Event :

```objectivec
- (void)LTIMManagerConnectedWithReceiver:(NSString * _Nonnull)receiver {
    //connect success
}

- (void)LTIMManagerDisconnectedWithReceiver:(NSString * _Nonnull)receiver error:(NSError * _Nullable)error {
    //connect erro
}
```

### Disconnect

If IM service is no longer needed, call `disconnect` to disconnect IM service. When disconnect, [`LTIMManagerDisconnectedWithReceiver:error`](#common) will be triggered. Strongly suggest to disconnect in background, so as to reduce unnecessary background operations.

```objectivec
 [manager disconnectWithCompletion:^(BOOL success, LTErrorInfo * _Nullable error) {
    if (success) {
        //disconnect success
        return;
    }
}];
```

Receive Event :

```objectivec
- (void)LTIMManagerDisconnectedWithReceiver:(NSString * _Nonnull)receiver error:(NSError * _Nullable)error {
    //the error is nil when disconnecting actively
}
```

### Detect Connection

Call `isConnected` to detect connection when necessary.

```objectivec
BOOL isConnected = [manager isConnected];
```

## Channel

Channel is a delivered path of messages. If you don't have any channel, you should create a channel before sending messages.

Obtain **LTChannelHelper** from **LTIMManager** and call channel funtions.

```
LTIMManger *manager = [LTSDK getIMManagerWithUserID:userID];
LTChannelHelper *helper = manager.channelHelper;
// Call channel functions...
```

### Create My File Channel

Create a My File Channel, which is exclusive for user oneself's chatroom.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];

[helper createMyFileChannelWithTransID:transID completion:^(LTCreateChannelResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

### Create Single Channel

Single channel is for two users in a chatroom to send messages, it is unique for these two specific users. As being specific, there is no need to delete or kick off, and both members have equal rights and privilege, so no role setting for **LTMemberModel** required. **LTMemberModel** has been set for member information, it was built before the channel was created. **LTMemberModel** object created first, then bring in `createSingleChannelWithTransID:member:completion:` for single channel. Success creation will trigger [Receive Event - Channel](#channel-1) method [`LTIMManagerIncomingCreateChannel:receiver:`](#channel-1).

```objectivec
LTMemberModel *member = [[LTMemberModel alloc] init];
member.userID = @"userID";
member.chNickname = @"Paul";

NSString *transID = [[NSUUID UUID] UUIDString];

[helper createSingleChannelWithTransID:transID member:member completion:^(LTCreateChannelResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

Receive Event :

```objectivec
- (void)LTIMManagerIncomingCreateChannel:(LTCreateChannelResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {

}
```

### Create Group Channel

Group Channel is a multi members chatroom, every channel is unique. Once the channel is dismissed, the chatroom doesn't exist any more. In group channel, member's privilege can be set in **LTMemberModel**, and bring in `createGroupChannelWithTransID:chID:channelSubject:members:completion:` Success creation would trigger [Receive Event - Channel](#channel-1) method [`LTIMManagerIncomingCreateChannel:receiver:`](#channel-1). Besides, the difference from a single channel is that the chatroom's ID and displayed name can be defined. Examples as:

```objectivec
LTMemberModel *memberA = [[LTMemberModel alloc] init];
memberA.userID = @"userA";
memberA.chNickname = @"Paul";
memberA.roleID = LTChannelRoleParticipant;

LTMemberModel *memberB = [[LTMemberModel alloc] init];
memberB.userID = @"userB";
memberB.chNickname = @"Hugo";
memberB.roleID = LTChannelRoleParticipant;

NSMutableSet *initialMembers = [[NSMutableSet alloc] initWithObjects:memberA, memberB, nil];

NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = [[NSUUID UUID] UUIDString];
NSString *subject = @"Channel Subject";

[helper createGroupChannelWithTransID:transID chID:chID channelSubject:subject members:initialMembers completion:^(LTCreateChannelResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

Receive Event :

```objectivec
- (void)LTIMManagerIncomingCreateChannel:(LTCreateChannelResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {

}
```

### Set Channel

After a channel is created, channel related settings can be edited, including:

#### Channel Preference

Channel Preference means user oneself's exclusive setting in this chatroom and the setting value can only be obtained by user oneself. When any setting successful, such channel prefrenece shall be sent back. When setting is successful, it would trigger [Receive Event - Channel](#channel-1) method [`LTIMManagerIncomingChannelPreference:receiver:`](#channel-1).

-   **Mute**: set the chatroom to be silent. If you want to turn the notification off, set it as true, if not, set it as false.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
BOOL isMute = YES;

[helper setChannelMuteWithTransID:transID chID:chID isMute:isMute completion:^(LTChannelPreferenceResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    //LTChannelPreferenceResponse including Mute, RingTone and Nickname current state.
}];
```

-   **RingTone**: setting for the ringtone of the chatroom, can also the way the ringtone sync, e.g. the route or the corresponding source ID.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
NSString * ringToneID = @"xxxxxxxx.mp3";

[helper setChannelRingToneWithTransID:transID chID:chID ringToneID:ringToneID completion:^(LTChannelPreferenceResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    //LTChannelPreferenceResponse including Mute, RingTone and Nickname current state.
}];
```

-   **Nickname**: setting for the chatroom's nickname, this nickname shall be shown in all member's chatroom.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
NSString *nickname = @"new nickname";

[helper setChannelUserNicknameWithTransID:transID chID:chID nickname:nickname completion:^(LTChannelPreferenceResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    //LTChannelPreferenceResponse including Mute, RingTone and Nickname current state.
}];
```

#### Channel Profile

Channel Profile means the setting for the chatroom, all members shall receive the same info. If some member edited it, he and other members shall receive the setting value and trigger [Receiver Event - Channel](#channel-1) method [`LTIMManagerIncomingChannelProfile:receiver:`]

-   **Subject**: setting the chatroom's name.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
NSString *subject = @"new subject";

[helper setChannelSubjectWithTransID:transID chID:chID subject:subject completion:^(LTChannelProfileResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    //LTChannelProfileResponse including current subject only
}];
```

-   **Avatar**: chatroom's avatar/icon.

Setting chatroom's avatar.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
NSString *avatarPath = @"xxxx/xxxx/xxxx.jpg";//avatar local path

[helper setChannelAvatarWithTransID:transID chID:chID avatarPath:avatarPath completion:^(LTChannelProfileResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    //LTChannelProfileResponse including current avatar only
} progress:^(LTProgressResponse * _Nullable progressResponse, BOOL isDone) {
    //LTProgressResponse, isDone = YES means upload success
}];
```

Delete chatroom's avatar

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
LTFileInfo *fileInfo =channel.profileImageFileInfo;//channel fileInfo

[helper deleteChannelAvatarWithTransID:transID chID:chID fileInfo:fileInfo completion:^(LTChannelProfileResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
} ];
```

-   **CustomAttr**: setting chatroom's customization requirement, can edit the attribute, using bit concept to expand display requirement with flexibility.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
NSUInteger customAttr = x;

[helper setChannelCustomAttrWithTransID:transID chID:chID customAttr:customAttr completion:^(LTChannelProfileResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    //LTChannelProfileResponse including current customAttr only.
}];
```

### Get Channel

This chapter shall introduce how to obtain information about a channel.

#### Channel Information

Using different parameters to get specific channel information. Parameter can prioritize member's information first, when it's set as false, you can also obtain the info from [Get Member](#get-member). As the data load is heavy, please do use batch mechanism to avoid heavy loading. Suggest one batch to be 30 data at one batch.

-   Get a chatroom with **specific ID**: bring in chatroom channelID

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
BOOL withMembers = YES;

[helper queryChannelWithTransID:transID chID:chID withMembers:withMembers completion:^(LTQueryChannelsResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

-   Get chatroom with **specific type**: bring in queried chType, Set and batchCount.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSSet *chTypes = [[NSSet alloc] initWithObjects:@(LTChannelTypeGroup), nil];
NSUInterger batchCount = 30;
BOOL withMembers = YES;

[helper queryChannelWithTransID:transID chTypes:chTypes batchCount:batchCount withMembers:withMembers completion:^(LTQueryChannelsResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

-   Get changes after **specific time**: bring in queried timestamp and batchCount.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
long long lastUpdateTime = 1608797454000;
NSUInterger batchCount = 30;
BOOL withMembers = YES;

[helper queryChannelWithTransID:transID lastUpdateTime:lastUpdateTime batchCount:batchCount withMembers:withMembers completion:^(LTQueryChannelsResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

#### Channel Read Time

Get chatroom reading time can be used to count unread counts and all related unread functions.

-   Get a chatroom with **specific ID**: bring in chatroom channelID

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";

[helper queryChannelReadTimeWithTransID:transID chID:chID completion:^(LTQueryChannelsReadTimeResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

-   Get chatrooms with **specific parameters**: With lastChID, lastChType and count parameters. lastChID can bring in the chatroom ID from the previous time to get the next batch of chatrooms. If it's the first batch, please bring in an **empty string** and **LTChannelTypeUnknown** to capture from the first chatroom.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *lastChID = @""; // first batch
LTChannelType lastChType = LTChannelTypeUnknown; // first batch
NSUInteger count = 100;

[helper queryChannelsReadTimeWithTransID:transID lastChID:lastChID lastChType:lastChType count:count completion:^(LTQueryChannelsReadTimeResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    if (response.channels.count == count) {
        lastChID = response.channels.lastObject.chID;
        lastChType = response.channels.lastObject.chType;
        // next data available
    } else {
        // data end
    }
}];
```

#### Unread Channels

Get unread chatroom and all unread counts: query `queryUnreadChannelsWithTransID:completion:`.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];

[helper queryUnreadChannelsWithTransID:transID completion:^(LTQueryUnreadChannelsResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

#### Channel ReadInfo

Get chatroom member's read time, query `queryChannelReadInfoWithTransID:chID:completion:`.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";

[helper queryChannelReadInfoWithTransID:transID chID:chID completion:^(LTQueryChannelReadInfoResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

### Delete Channel

When deleting a channel, with privilege, query `dismissChannelWithTransID:chID:completion:`. When successfully delete the channel, it would trigger [`Receive Event - Channel`](#channel-1) method [`LTIMManagerIncomingDismissChannel:receiver:`](#channel-1).

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";

[helper dismissChannelWithTransID:transID chID:chID completion:^(LTDismissChannelResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

## Channel Member

Obtain **LTChannelHelper** from **LTIMManager** and call channel member funtions.

```objectivec
LTIMManger *manager = [LTSDK getIMManagerWithUserID:userID];
LTChannelHelper *helper = manager.channelHelper;
// Call channel member functions...
```

### Invite Member

If privileged to invite a member, query `inviteMembersWithTransID:chID:members:joinMethod:completion:` **no need for the invitee's permission** to join the room, the invitee value **cannot be empty**. Can Use **LTJoinMethod** to calculate the invitation, bring **LTJoinMethodNormal** if not necessary. Success invitation would trigger [`Receive Event - Channel Member`](#channel-member-1) method [`LTIMManagerIncomingInviteMember:receiver:`](#channel-member-1).

```objectivec
LTMemberModel *member = [[LTMemberModel alloc] init];
member.userID = @"xxxxxxx";
member.roleID = LTChannelRoleParticipant;
member.chProfileID = @"profileID";

NSMutableSet *invitelMembers = [[NSMutableSet alloc] initWithObjects:member, nil];

NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";

[helper inviteMembersWithTransID:transID chID:chID members:invitelMembers joinMethod:LTJoinMethodNormal completion:^(LTInviteMemberResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

### Kick Member

If privileged to kick a member, query `kickMembersWithTransID:chID:members:completion: ` to kick a specific member out of the chatroom. Kick member value **cannot be empty**. If successful kick, it would trigger [`Receive Event - Channel Member`](#channel-member-1) method [`LTIMManagerIncomingKickMember:receiver:`](#channel-member-1).

```objectivec
LTMemberModel *member = [[LTMemberModel alloc] init];
member.userID = @"xxxxxxx";

NSMutableSet *kickMembers = [[NSMutableSet alloc] initWithObjects:member, nil];

NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";

[helper kickMembersWithTransID:transID chID:chID members:kickMembers completion:^(LTKickMemberResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

### Join

The difference with Invite is that invite is the invitee is invited to join by others, and Join can actively join the chatroom. If the chatroom has the privilege to join, query `joinChannelWithTransID:chID:joinMethod:byWho:completion:` Join, like Invite, can bring the setting by **LTJoinMethod** and **byWho** to count and match the invitee info, bring **nil** if not necessary. If successfully joined, it would trigger [`Receive Event - Channel Member`](#channel-member-1) method [`LTIMManagerIncomingJoinChannel:receiver:`](#channel-member-1).

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";

[helper joinChannelWithTransID:transID chID:chID joinMethod:LTJoinMethodNormal byWho:nil completion:^(LTJoinChannelResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

### Leave

If privileged to leave, query `leaveChannelWithTransID:chID:completion:` to leave the chatroom. If leave successfully, it would trigger [`Receive Event - Channel Member`](#channel-member-1) method [`LTIMManagerIncomingLeaveChannel:receiver:`](#channel-member-1).

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";

[helper leaveChannelWithTransID:transID chID:chID completion:^(LTLeaveChannelResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

### Set Member

If privileged to edit a member's role, query `setMemberRoleWithTransID:chID:userID:roleID:completion:` Set member RoleID including oneself. Most common scenario is when the group admin leaves the chatroom, he needs to assign a member to be admin before leaving.If setting successful, it would trigger [`Receive Event - Channel Member`](#channel-member-1) method [`LTIMManagerIncomingSetMemberRole:receiver:`](#channel-member-1).

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
NSString *userID = @"xxxxxxxx"; // 須為 channel member
LTChannelRole roleID = LTChannelRoleParticipant; // 新 roleID

[helper setMemberRoleWithTransID:transID chID:chID userID:userID roleID:roleID completion:^(LTSetMemberRoleResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

### Get Member

#### Member Information

Based on roles and privileges, get member result could be different. The higher privilege, the more roles can be edited. Use `queryChannelMembersWithTransID:chID:lastUserID:count:completion:` to capture member list. Suggest to capture info in batch, **lastUserID** is the previous batch user ID, if it's the first time, bring in **empty string**.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
NSString *lastUserID = @""; // first batch
NSUInteger count = 30;

[helper queryChannelMembersWithTransID:transID chID:chID lastUser:lastUser count:count completion:^(LTQueryChannelMembersResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    if (response.count == count) {
        lastUserID = response.members.lastObject.userID;
        // next data available
    } else {
        // data end
    }
}];
```

#### Member Read Info

Get member read info by using `queryChannelReadInfoWithTransID:chID:completion:` to get the chatroom's member read info. Use the time of read to decide status

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";

[helper queryChannelReadInfoWithTransID:transID chID:chID completion:^(LTQueryChannelReadInfoResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

## Message

Obtain **LTMessageHelper** from **LTIMManager** and call message funtions.

```objectivec
LTIMManger *manager = [LTSDK getIMManagerWithUserID:userID];
LTMessageHelper *helper = manager.messageHelper;
// Call message functions...
```

### Send Message

This chapter introduces LTIMSDK message sending and message types.

#### Messages

Below is what LTIMSDK supports in messge type, with the following chapters of [Send Message](#send-message), [Send Broadcast Messages](#send-broadcast-messages) and [Send Scheduled Messages](#send-scheduled-messages).

-   **_Text Message_**: Text Message is for general text messages. To avoid overloading, limit the messages in 8000 characters. Text message supports tag type, use tag to specify specific member. specified member can be marked by using **LTTagUser**, accompanied by **start** and **length** in **LTTagUser** the displayed way replaced in **msgContent**.

```objectivec
LTTagUser *tagUser = [[LTTagUser alloc] init];
tagUser.userID = @"userIDA";
tagUser.start = 0;
tagUser.length = 4;

NSSet *tagUsers = [[NSSet alloc] initWithObjects: tagUser, nil];

LTTextMessage *message = [[LTTextMessage alloc] init];
message.transID = [[NSUUID UUID] UUIDString];
message.chID = @"ChannelID";
message.chType = LTChannelTypeGroup;
message.msgContent = @"@Paul, How are you?";
message.tagUsers = tagUsers;
```

-   **_Sticker Message_**: this sticker message can be LT owned stickers or replaced by self-defined sticker. When using self-defined sticker, use **msgContent** to acquire the sticker, such as the sticker file source.

```objectivec
LTStickerMessage *message = [[LTStickerMessage alloc] init];
message.transID = [[NSUUID UUID] UUIDString];
message.chID = @"ChannelID";
message.chType = LTChannelTypeGroup;
message.msgContent = @"690d1a03-b9c5-ae30-046b-24936dc97c46,3013,2";
```

-   **_Image Message_**: If using **LTImageMessage** to send a message, the message shall be sent to LT designated cloud space. If other designated space is required, use **LTCustomMessage** to customize the sending. In **LTImageMessage**, can send the original image or thumbnail file path by using the client's local files. LT also provides an **extInfo** column for attached info, such as the dimension of the image to be displayed on the image.

```objectivec
NSString *imageFilePath = [[NSBundle mainBundle] pathForResource:@"ef05f74a-90f7-4f69-94cb-d588fa435bae" ofType:@"png"];
NSString *thumbnailFilePath = [[NSBundle mainBundle] pathForResource:@"ef05f74a-90f7-4f69-94cb-d588fa435bae" ofType:@"png"];

NSDictionary *extInfo = @{@"width":@(360), @"height":@(422)};

LTImageMessage *message = [[LTImageMessage alloc] init];
message.transID = [[NSUUID UUID] UUIDString];
message.chID = @"ChannelID";
message.chType = LTChannelTypeGroup;
message.imagePath = imageFilePath;
message.thumbnailPath = thumbnailFilePath;
message.extInfo = extInfo;
message.fileName = @"iOS.png";
```

-   **_Video Message_**: if using **LTVideoMessage** to send a video message, the message shall be sent to LT designated cloud space. If other designated space is required, use **LTCustomMessage** to customize the sending. In **LTVideoMessage**, can send original video or thumbnail file path by using client's local files. LT also provides an **extInfo** column for attached info, such as the dimension of the image to be displayed on the image.

```objectivec
NSString *videoFilePath = [[NSBundle mainBundle] pathForResource:@"video" ofType:@"mp4"];
NSString *thumbnailFilePath = [[NSBundle mainBundle] pathForResource:@"image" ofType:@"png"];

NSDictionary *extInfo = @{@"width":@(360), @"height":@(422)};

LTImageMessage *message = [[LTImageMessage alloc] init];
message.transID = [[NSUUID UUID] UUIDString];
message.chID = @"ChannelID";
message.chType = LTChannelTypeGroup;
message.videoPath = videoFilePath;
message.thumbnailPath = thumbnailFilePath;
message.extInfo = extInfo;
message.fileName = @"iOS.mp4";
```

-   **_Voice Message_**: if using **LTVoiceMessage** to send a voice message, the message shall be sent to LT designated cloud space. If other designated space is required, use **LTCustomMessage** to customize the sending. In **LTVoiceMessage**, can send original video or thumbnail file path by using client's local files. LT also provides an **extInfo** column for attached info, such as the dimension of the image to be displayed on the image.

```objectivec
NSString *voiceFilePath = [[NSBundle mainBundle] pathForResource:@"voice" ofType:@"aac"];

NSDictionary *extInfo = @{@"duration":@"00:05:30"};

LTVoiceMessage *message = [[LTVoiceMessage alloc] init];
message.transID = [[NSUUID UUID] UUIDString];
message.chID = @"ChannelID";
message.chType = LTChannelTypeGroup;
message.voicePath = voiceFilePath;
message.extInfo = extInfo;
message.fileName = @"iOS.aac";
```

-   **_Location Message_**: together with [Google Map](https://developers.google.com/maps/documentation/android-sdk/map) to get location info, assorted data for **LTLocation** parameter in sending **LTLocationMessage**.

```objectivec
LTLocation *location = [[LTLocation alloc] init];
location.address = @"No.5, Shaoxing N. St., Zhongzheng Dist., Taipei City, Taiwan (R.O.C.)";
location.latitude = 25.04492228152394;
location.longitude = 121.52542401563323;

LTLocationMessage *message = [[LTLocationMessage alloc] init];
message.transID = [[NSUUID UUID] UUIDString];
message.chID = @"ChannelID";
message.chType = LTChannelTypeGroup;
message.location = location;
```

-   **_Contact Message_**: if using **LTContactMessage** to send a contact message, the message shall be sent to LT designated cloud space. If other designated space is required, use **LTCustomMessage** to customize the sending. In **LTContactMessage**, can send original contact info or thumbnail file path by using client's local files. LT also provides an **extInfo** column for attached info, such as the contact displayed name on the image.

```objectivec
NSString *contactFilePath = [[NSBundle mainBundle] pathForResource:@"contact" ofType:@"vcf"];
NSString *thumbnailFilePath = [[NSBundle mainBundle] pathForResource:@"image" ofType:@"jpeg"];

NSDictionary *extInfo = @{@"displayName":@"Ann"};

LTContactMessage *message = [[LTContactMessage alloc] init];
message.transID = [[NSUUID UUID] UUIDString];
message.chID = @"ChannelID";
message.chType = LTChannelTypeGroup;
message.contactPath = contactFilePath;
message.thumbnailPath = thumbnailFilePath;
message.extInfo = extInfo;
message.fileName = @"Ann.vcf";
```

-   **_Document Message_**: If using **LTDocumentMessage** to send a document message, the message shall be sent to LT designated cloud space. If other designated space is required, use **LTCustomMessage** to customize the sending. In **LTDocumentMessage**, can send original contact info or thumbnail file path by using client's local files. LT also provides an **extInfo** column for attached info, such as the file size to be displayed on the image.

```objectivec
NSString *documentFilePath = [[NSBundle mainBundle] pathForResource:@"document" ofType:@"doc"];

NSDictionary *extInfo = @{@"fileSize":@"5MB"};

LTDocumentMessage *message = [[LTDocumentMessage alloc] init];
message.transID = [[NSUUID UUID] UUIDString];
message.chID = @"ChannelID";
message.chType = LTChannelTypeGroup;
message.filePath = filePath;
message.extInfo = extInfo;
message.fileName = @"Document.docf";
```

#### Relpy Messages

Reply messages : can reply to specified parrent [Messages](#messages), and also can reply all type of child [Messages](#messages). The way to specify parrent message is to use **LTReplyMessage** to bring in child message. **LTReplyMessage** will bring the successful delivery info of the parent message in designated parameter. In addition, **msgID** of parent message needs to be brought in child message as **parentMsgID**. When child message is set, use [Send Message](#send-message) to send. Following is an image and text message sending example.

-   Image message (Parent message) replies to a text message (child message)

```objectivec
LTReplyMessage *replyMessage = [[LTReplyMessage alloc] init];
replyMessage.msgID = @"cec95048-9e62-11ea-9c63-599b39045450";
replyMessage.msgType = LTMessageTypeText;
replyMessage.sendTime = 1590054366458;
replyMessage.senderID = @"sendUserID";
replyMessage.senderNickName = @"Ann";

NSString *imageFilePath = [[NSBundle mainBundle] pathForResource:@"ef05f74a-90f7-4f69-94cb-d588fa435bae" ofType:@"png"];
NSString *thumbnailFilePath = [[NSBundle mainBundle] pathForResource:@"ef05f74a-90f7-4f69-94cb-d588fa435bae" ofType:@"png"];

NSDictionary *extInfo = @{@"width":@(360), @"height":@(422)};

LTImageMessage *message = [[LTImageMessage alloc] init];
message.transID = [[NSUUID UUID] UUIDString];
message.chID = @"ChannelID";
message.chType = LTChannelTypeGroup;
message.imagePath = imageFilePath;
message.thumbnailPath = thumbnailFilePath;
message.extInfo = extInfo;
message.fileName = @"iOS.png";
message.replyMessage = replyMessage;
message.parentMsgID = @"cec95048-9e62-11ea-9c63-599b39045450";
```

-   Text message (child message) replies to an image message (Parent message)

```objectivec
LTReplyMessage *replyMessage = [[LTReplyMessage alloc] init];
replyMessage.msgID = @"9a3a8afa-06f8-11eb-9c63-1a2e0f0676dd";
replyMessage.msgType = LTMessageTypeImage;
replyMessage.sendTime = 1601895037417;
replyMessage.senderID = @"sendUserID";
replyMessage.senderNickName = @"Ann";

LTTextMessage *message = [[LTTextMessage alloc] init];
message.transID = [[NSUUID UUID] UUIDString];
message.chID = @"ChannelID";
message.chType = LTChannelTypeGroup;
message.msgContent = @"1234";
message.replyMessage = replyMessage;
message.parentMsgID = @"07a9dd94-d5f7-11ea-9c63-1a2d5b06220d";
```

#### Send Message

When sending all types of [Messages](#messages) and [Relpy Messages](#reply-messages), use `sendMessage:completion:` to send it. After delivery is successful, shall receive the result of **LTSendMessageResponse**. **LTMessage** can be transformed into the object of all types of [Messages](#messages), and obtain its exclusive info. If successfully sent, it would receive message under [Receive Event - Message](#message-2) method [`LTIMManagerIncomingSendMessage:receiver:`](#message-2). Please refer to the following example.

```objectivec
LTTextMessage *message = [[LTTextMessage alloc] init];
message.transID = [[NSUUID UUID] UUIDString];
message.chID = @"ChannelID";
message.chType = LTChannelTypeGroup;
message.msgContent = @"1234";

NSString *userID = @"userID";

LTIMManager *manager = [LTSDK getIMManagerWithUserID:userID];
[manager.messageHelper sendMessage:message completion:^(LTSendMessageResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

**Send File Message**: send LTFileMessage (including LTImageMessage、LTVideoMessage、LTContactMessage...etc.), If LT setting cloud space used, and needs the progress of sending, query `sendFileMessage:completion:progress:` received from callback. If file is sent successfully, it would receive message under [Receive Event - Message](#message-2) method [`LTIMManagerIncomingSendMessage:receiver:`](#message-2). Please refer to the following example.

```objectivec
NSString *imageFilePath = [[NSBundle mainBundle] pathForResource:@"ef05f74a-90f7-4f69-94cb-d588fa435bae" ofType:@"png"];
NSString *thumbnailFilePath = [[NSBundle mainBundle] pathForResource:@"ef05f74a-90f7-4f69-94cb-d588fa435bae" ofType:@"png"];

NSDictionary *extInfo = @{@"width":@(360), @"height":@(422)};

LTImageMessage *message = [[LTImageMessage alloc] init];
message.transID = [[NSUUID UUID] UUIDString];
message.chID = @"ChannelID";
message.chType = LTChannelTypeGroup;
message.imagePath = imageFilePath;
message.thumbnailPath = thumbnailFilePath;
message.extInfo = extInfo;
message.fileName = @"iOS.png";

NSString *userID = @"userID";

LTIMManager *manager = [LTSDK getIMManagerWithUserID:userID];
[manager.messageHelper sendFileMessage:message completion:^(LTSendMessageResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    // send success
} progress:^(LTMessageProgressResponse * _Nullable progressResponse, BOOL isDone) {
    //LTProgressResponse: isDone = YES means upload success
}];
```

Receive Event :

```objectivec
- (void)LTIMManagerIncomingSendMessage:(LTSendMessageResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {

}
```

### Send Broadcast Messages

[Send Message](#send_message) can send a any-type of message to any specified contact or channel. If want to send multiple messages please use `broadcastMessageWithTransID:messages:chIDs:completion:` to send multiple contacts or chatrooms. After broadcast, the Boolean value would tell if it's successful. The broadcast message shall be received in [Receive Event - Message](#message-2) method [`LTIMManagerIncomingSendMessage:receiver:`](#message-2).

```objectivec
LTTextMessage *textMessage = [[LTTextMessage alloc] init];
textMessage.transID = [[NSUUID UUID] UUIDString];
textMessage.msgContent = @"1234";

LTStickerMessage *stickMessage = [[LTStickerMessage alloc] init];
stickMessage.transID = [[NSUUID UUID] UUIDString];
stickMessage.msgContent = @"690d1a03-b9c5-ae30-046b-24936dc97c46,3013,2";

NSString *imageFilePath = [[NSBundle mainBundle] pathForResource:@"ef05f74a-90f7-4f69-94cb-d588fa435bae" ofType:@"png"];
NSString *thumbnailFilePath = [[NSBundle mainBundle] pathForResource:@"ef05f74a-90f7-4f69-94cb-d588fa435bae" ofType:@"png"];
NSDictionary *extInfo = @{@"width":@(360), @"height":@(422)};
LTImageMessage *imageMessage = [[LTImageMessage alloc] init];
imageMessage.transID = [[NSUUID UUID] UUIDString];
imageMessage.imagePath = imageFilePath;
imageMessage.thumbnailPath = thumbnailFilePath;
imageMessage.extInfo = extInfo;
imageMessage.fileName = @"iOS.png";

NSArray *messages = [[NSArray alloc] initWithObjects: textMessage, stickMessage, imageMessage, nil];
NSArray *chIDs = [[NSArray alloc] initWithObjects: @"chID", nil];
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *userID = @"userID";

LTIMManager *manager = [LTSDK getIMManagerWithUserID:userID];
[manager.messageHelper broadcastMessageWithTransID:transID messages:messages chIDs:chIDs completion:^(BOOL success, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    // broadcast success
}];
```

### Send Scheduled Messages

When sending messages to contact or chatrooms in the specified time to deliver, please use scheduledMessageWithTransID:messages:chIDs:scheduledTime:completion:`scheduledMessageWithTransID:messages:chIDs:scheduledTime:completion:` sending scheduled message with specified **timeToSend** is a scheduled time tag. **timeToSend** must be set for at least 1 minute and maximum in 30 days. If scheduled message is sent successfully, it would trigger [Receive Event - Message](#message-2) method [`LTIMManagerIncomingScheduledMessage:receiver:`](#message-2). When time is up, the message shall be received in [Receive Event - Message](#message-2) method [`LTIMManagerIncomingSendMessage:receiver:`](#message-2)

```objectivec
LTTextMessage *textMessage = [[LTTextMessage alloc] init];
textMessage.transID = [[NSUUID UUID] UUIDString];
textMessage.msgContent = @"1234";

LTStickerMessage *stickMessage = [[LTStickerMessage alloc] init];
stickMessage.transID = [[NSUUID UUID] UUIDString];
stickMessage.msgContent = @"690d1a03-b9c5-ae30-046b-24936dc97c46,3013,2";

NSString *imageFilePath = [[NSBundle mainBundle] pathForResource:@"ef05f74a-90f7-4f69-94cb-d588fa435bae" ofType:@"png"];
NSString *thumbnailFilePath = [[NSBundle mainBundle] pathForResource:@"ef05f74a-90f7-4f69-94cb-d588fa435bae" ofType:@"png"];
NSDictionary *extInfo = @{@"width":@(360), @"height":@(422)};
LTImageMessage *imageMessage = [[LTImageMessage alloc] init];
imageMessage.transID = [[NSUUID UUID] UUIDString];
imageMessage.imagePath = imageFilePath;
imageMessage.thumbnailPath = thumbnailFilePath;
imageMessage.extInfo = extInfo;
imageMessage.fileName = @"iOS.png";

NSArray *messages = [[NSArray alloc] initWithObjects: textMessage, stickMessage, imageMessage, nil];
NSArray *chIDs = [[NSArray alloc] initWithObjects: @"chID", nil];
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *userID = @"userID";

NSTimeInterval scheduledTime = [[NSDate date] timeIntervalSince1970] + 86400 * 7;

LTIMManager *manager = [LTSDK getIMManagerWithUserID:userID];
[manager.messageHelper scheduledMessageWithTransID:transID messages:messages chIDs:chIDs scheduledTime:scheduledTime completion:^(LTScheduledMessageResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    // schedule success
}];
```

### Send Forward Messages

Send forward messages, please use `forwardMessageWithTransID: msgIDs:chIDs:completion:`.

```
NSArray *msgIDs = [[NSArray alloc] initWithObjects: @"msgID", nil];
NSArray *chIDs = [[NSArray alloc] initWithObjects: @"chID", nil];
NSString *transID = [[NSUUID UUID] UUIDString];

LTIMManager *manager = [LTSDK getIMManagerWithUserID:userID];
[manager.messageHelper forwardMessageWithTransID:transID msgIDs:msgIDs chIDs:chIDs completion:^(BOOL success, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    // forward success
}];
```

### Send Create Vote Messages

Send a vote message to the chatroom.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
NSString *subject = @"new subject";

LTVoteOption *voteOption1 = [[LTVoteOption alloc] init];
voteOption.msgContent = @"Option1";
voteOption.msgType = LTMessageTypeVoteText;

LTVoteOption *voteOption2 = [[LTVoteOption alloc] init];
voteOption.msgContent = @"Option2";
voteOption.msgType = LTMessageTypeVoteText;

NSTimeInterval timeToStartVote = [[NSDate date] timeIntervalSince1970];
NSTimeInterval timeToCloseVote = [[NSDate date] timeIntervalSince1970] + 86400 * 7;

LTIMManager *manager = [LTSDK getIMManagerWithUserID:userID];

[manager.messageHelper createVoteWithTransID:transID chID:chID chType:LTChannelTypeGroup subject:subject voteOptions:@[voteOption1, voteOption2] fileSize:0 timeToStartVote:timeToStartVote timeToCloseVote:timeToCloseVote completion:^(LTCreateVoteResponse * _Nullable response, LTErrorInfo * _Nullable error) {

}];

```

### Send CastVote Messages

send a message to vote, bring in options by QueryVoteResponse to get LTVoteOption 的 optionMsgID

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
NSString *optionMsgID = @"xxxxxxxx";

LTIMManager *manager = [LTSDK getIMManagerWithUserID:userID];

[manager.messageHelper castVoteWithTransID:transID optionMsgID:optionMsgID completion:^(LTCastVoteResponse * _Nonnull response, LTErrorInfo * _Nonnull error) {

}];

```

### Get Message

#### Message

Get messages by query `queryMessageWithTransID:chID:markTS:afterN:completion:` by using different parameters to get different info. chID parameter not empty value means to capture the chatroom message info only. If the chID is empty, means to get user messages from an unspecified channel. Parameters markTS / markMsgID represents a baseline, afterN is to capture messages after markTS / markMsgID. If it's a **negative number**, capture message before markTS / markMsgID. Suggest the arrange of AfterN to be -30 to 30.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
long long markTS = 1608797454000;
NSInteger afterN = 30;

[helper queryMessageWithTransID:transID chID:chID markTS:markTS afterN:afterN completion:^(LTQueryMessageResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    if (response.count == afterN) {
        markTS = response.messages.lastObject.sendTime;
        // next data available
    } else {
        // data end
    }
}];
```

#### Schedule Message

Get schedule message info by query `queryScheduleMessageWithTransID:chID:markTS:afterN:completion:` by using different parameters to get different info. chID parameter not empty value means to capture the chatroom message info only. If the chID is empty, means to get user messages from an unspecified channel. Parameters markTS / markMsgID represents a baseline, afterN is to capture messages after markTS / markMsgID. If it's a **negative number**, capture message before markTS / markMsgID. Suggest the arrangement of AfterN to be -30 to 30.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
NSString *markMsgID = @"xxxxxxx";
NSInteger afterN = 30;

[helper queryScheduleMessageWithTransID:transID chID:chID markMsgID:markMsgID afterN:afterN msgCategory:nil completion:^(LTQueryMessageResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    if (response.count == afterN) {
        markMsgID = response.messages.lastObject.msgID;
        // next data available
    } else {
        // data end
    }
}];
```

#### Voting List

Get chatroom voting list.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
NSTimeInterval markTS = 1590054366458;
NSInteger afterN = 30;

[helper queryVotingListWithTransID:transID chID:chID markTS:markTS afterN:afterN completion:^(LTQueryVoteResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

#### Voting Options List

Get chatroom voting options list.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSArray *msgIDs = [[NSArray alloc] initWithObjects: @"msgID", nil];

[helper queryVotingOptionListWithTransID:transID msgIDs:msgIDs completion:^(LTQueryVoteOptionResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

#### File

If is using LT cloud space, must use **LTStorageManager** `executeWithAcitons:completion:resultsChanged:` to get a file. Bring **LTFileInfo** in **LTStorageAction** parameter. Other **LTFileInfo** can be obtained by [LTSendMessageResponse](#ltsendmessageresponse).

```objectivec
LTSendMessageResponse *respone = ...;
LTMessage *message = response.message;
LTFileInfo *fileInfo = nil;

if ([message isKindOfClass:[LTFileMessage class]]) {
    fileInfo = ((LTFileMessage*)message).fileInfo;
}

if ([message isKindOfClass:[LTThumbnailFileMessage class]]) {
    thumbnailFileInfo = ((LTThumbnailFileMessage*)message). thumbnailFileInfo;
}

if (!fileInfo) {
    return;
}

NSString *storePath = @"/..../..../Local Store Path.png";

LTStorageAction *action = [LTStorageAction createDownloadFileActionWithLTFileInfo:fileInfo storePath:storePath];

NSString *storeThumbPath = @"/..../..../Local Store Thumb Path.png";

LTStorageAction *actionThumbnail = [LTStorageAction createDownloadFileActionWithLTFileInfo:thumbnailFileInfo storePath: storeThumbPath];

NSString *userID = @"userID";

NSArray *actions = [[NSArray alloc] initWithObjects:action, actionThumbnail, nil];

LTStorageManager *manager = [LTSDK getStorageManagerWithUserID:userID];
[manager executeWithAcitons:actions completion:^(LTResponse * _Nonnull response, NSArray<LTStorageResult *> * _Nullable resultArray) {
    if (respose.returnCode == LTReturnCodeSuccess) {
        //download success
    }
} resultsChanged:^(NSArray<LTStorageResult *> * _Nullable resultArray) {
    for (LTStorageResult *result in resultArray) {
        NSString *acitonID = result.actionID;
        LTStorageStatus status = result.status;
        long long loadingByted = result.loadingBytes;
        long long totalLength = result.totalLength;
    }
}];
```

### Read Message

#### Set Read

Use `markReadWithTransID:chID:markTS:completion:` to send Read status. Set read is to bring **sendTime** of the read-message into the parameter. Please notice that once the read-message is set as read, all previous messages shall be set as been read too. The scenario is when the user enters a chatroom, or receives an instant message while in the room, the **sendTime** when the user sends the last message shall be set as all messages read, so there is no need to set every message to be read. Chatroom members' read status can be received from [Receive Event - Message](#message-2) method [`LTIMManagerIncomingMarkRead:receiver:`](#message-2).

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
long long markTS = 1608797454000;

[helper markReadWithTransID:transID chID:chID markTS:markTS completion:^(LTMarkReadResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

Receive Event :

```objectivec
- (void)LTIMManagerIncomingMarkRead:(LTMarkReadResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {

}
```

#### markReadNewsWithTransID

Use `markReadWithTransID:chID:markTS:completion:` to send Read status. Set read is to bring **sendTime** of the read-message into the parameter. Please notice that once the read-message is set as read, all previous messages shall be set as been read too. The scenario is when the user enters a chatroom, or receives an instant message while in the room, the sendTime when the user sends the last message shall be set as all messages read, so there is no need to set every message to be read. Chatroom members' read status can be received from [Receive Event - Message](#message-2) method [`LTIMManagerIncomingMarkReadNews:receiver:`](#message-2).

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
long long markTS = 1608797454000;

[helper markReadNewsWithTransID:transID chID:chID markTS:markTS completion:^(LTMarkReadNewsResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

Receive Event :

```objectivec
- (void)LTIMManagerIncomingMarkReadNews:(LTMarkReadNewsResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {

}
```

#### Get Read

Understanding how to set oneself's read status from [Set Read](#set-read), now goes to the way to get the other members' read status. After chatroom members set rad in [Set Read](#set-read), can receive member's read status from [Receive Event - Message](#message-2) method [`LTIMManagerIncomingMarkRead:receiver:`](#message-2). By the member's last read time, received from [LTMarkReadResponse](#ltmarkreadresponse), we can learn this member's read status in this chatroom. If the member has sent read status but he's offline to receive the read status, can query [Member Read Info](#member-read-info) when back online to get channel members' read status.

-   **read users amount**: format array by queried message msgID, bring in `queryMessageReadCountWithTransID:msgIDs:completion:` to get the read users amount

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *msgID = @"msgID";

[helper queryMessageReadCountWithTransID:transID msgIDs:@[msgID] completion:^(LTQueryMessageReadCountResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

-   read user info：query `queryMessageReadUsersWithTransID:msgID:lastReadTime:count:completion:` to obtain the read user list of this msgID, to avoid loading, LT provides capture by batch. **lastReadTime** can bring up the last user's read time in the last batch. If it's the first time, please bring **0**.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *msgID = @"msgID";
long long lastReadTime = 0;
NSUInteger count = 100;

[helper queryMessageReadUsersWithTransID:transID msgID:msgID lastReadTime:lastReadTime count:count completion:^(LTQueryMessageReadUsersResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    if (response..userscount == count) {
        lastReadTime = response.users.lastObject.readTime;
        // next data available
    } else {
        // data end
    }
}];
```

### Delete Message

This chapter introduces the function of Delete Message.

#### Delete a messages

Delete messages would only delete onesel's messages on server, messages in other member's still exist.

Delete **a specific** message, query `deleteMessagesWithTransID:msgIDs:completion:`. Successful delete would trigger [Receive Event - Message](#message-2) method [`LTIMManagerIncomingDeleteMessages:receiver:`](#message-2).

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *msgID = @"msgID";

[helper deleteMessagesWithTransID:transID msgIDs:@[msgID] completion:^(LTDeleteMessagesResponse *response, LTErrorInfo *error)) {
    if (error) {
        return;
    }
}];
```

Receive Event :

```objectivec
- (void)LTIMManagerIncomingDeleteMessages:(LTDeleteMessagesResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {

}
```

#### Delete channel messages

Delete channel messages in a chatroom would only delete onesef's messages on the server, messages in other member's still exist. Query `deleteChannelMessagesWithTransID:chID:completion:`. Successful delete would trigger [Receive Event - Message](#message-2) method [`LTIMManagerIncomingDeleteChannelMessages:receiver:`](#message-2)

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"chID";

[helper deleteChannelMessagesWithTransID:transID chID:chID completion:^(LTDeleteChannelMessagesResponse *response, LTErrorInfo *error)) {
    if (error) {
        return;
    }
}];
```

-   Receive Event :

```objectivec
- (void)LTIMManagerIncomingDeleteChannelMessages:(LTDeleteChannelMessagesResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {

}
```

#### Delete all messages

Delete all messages in a chatroom would only delete onesef's messages on the server, messages in other member's still exist. Query `deleteAllMessagesWithTransID:completion:`. Successful delete would trigger [Receive Event - Message](#message-2) method [`LTIMManagerIncomingDeleteAllMessages:receiver:`](#message-2).

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];

[helper deleteAllMessagesWithTransID:transID completion:^(LTDeleteChannelMessagesResponse *response, LTErrorInfo *error)) {
    if (error) {
        return;
    }
}];
```

Receive Event :

```objectivec
- (void)LTIMManagerIncomingDeleteChannelMessages:(LTDeleteChannelMessagesResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {

}
```

### Recall Message

Recall messages shall influence other member's query messages. Recall messages are only valid for the sender to recall his own messages. If privileged, query `recallMessageWithTransID:msgIDs:silentMode:completion:`. After successful recall, will receive a **LTMessageTypeRecall** from **LTRecallResponse**, and trigger Receive Event - Message](#message-2) method [`LTIMManagerIncomingRecall:receiver:`](#message-2).

-   **Silent Mode**: if set as true, after message recalled, original message would be queried; if set as false, cannot query the original message but the msgContent has been cleared.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *msgID = @"msgID";

[helper recallMessageWithTransID:transID msgIDs:@[msgID] completion:^(BOOL success, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

## User

User set values, including global nickname, avatar info and APNS settings…etc.

```objectivec
LTIMManger *manager = [LTSDK getIMManagerWithUserID:userID];
LTUserHelper *helper = manager.userHelper;
// Call user functions...
```

### Set User

This chapter shall introduce the user's settings.

#### APNS Setting: device-driven. if user changes his device, the setting shall return to its default value, need to be reset again.

-   **<span id="apns-token">APNS Token</span>**: Set APNS Token value, query `setUserPushTokenWithTransID:key:completion:`. [How to get APNS Token](#register-your-app-and-retrieve-your-app39s-device-token).

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *apnsToken = @"token";

[helper setUserPushTokenWithTransID:transID key:apnsToken completion:^(LTUserPushTokenResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

-   **<span id="apns-display">APNS Display</span>**: set sender's name and content in APNS, query `setUserDeviceNotifyPreviewWithTransID:hidingSender:hidingContent:completion:`.

| Parameter     | Description                   |
| :------------ | :---------------------------- |
| hidingSender  | if False : no sender's name   |
| hidingContent | if False : no message content |

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];

[helper setUserDeviceNotifyPreviewWithTransID:transID hidingSender:YES hidingContent:YES completion:^(LTUserDeviceNotifyPreviewResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

-   **<span id="apns-sound">APNS Sound</span>**: set APNS Sound value, query `setDeviceNotifySoundWithTransID:soundType:soundID:completion:`.

| Parameter | Description |
| :-- | :-- |
| soundType | 1 is to set APNS Sound value, 2 is to set PushKit Sound value (collaborated with LT SDK), 0 is to set APNS + PushKit Sound value |
| soundID | Sound ID, set sound path or sound ID |

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSUInteger soundType = 1; // APNS Sound
NSString *soundID = @"alert.wav";

[helper setDeviceNotifySoundWithTransID:transID soundType:soundType soundID:soundID completion:^(LTUserDeviceNotifySoundResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

-   **<span id="apns-mute">APNS Mute</span>**: set to get APNS or not, query `setUserDeviceMuteWithTransID:muteAll:time:completion:`.

| Parameter | Description |
| :-- | :-- |
| muteAll | if true, no APNS message |
| time | Do not disturb mode. Set time tag. no notification before this time tag. Please notice that if muteAll was set true, do not disturb mode shall be invalid. If no need, please set `null`. |

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];

[helper setUserDeviceMuteWithTransID:transID muteAll:YES time:nil completion:^(LTUserDeviceMuteResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

#### User Profile

Edit user profile: once set, beside oneself receive the changed result, all chatroom members shall receive the same changed result. User's profile setting. When it's set successfully, shall receive the current setting value, and receive the value from [Receiver Event - User](#user-1) method [`LTIMManagerIncomingSetUserProfile:receiver:`](#user-1). If other members set their profile and user has built connection between each other (in same channel), current edit content would be received from [Receiver Event - User](#user-1) method [`LTIMManagerIncomingModifyUserProfile:receiver:`](#user-1).

-   **<span id="user-nickname">User Nickname</span>**: set user's nickname.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *nickname = @"Robin";

[helper setUserNicknameWithTransID:transID nickname:nickname completion:^(LTSetUserProfileResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    // LTSetUserProfileResponse including nickname only
}];
```

-   **<span id="user-avatar">User Avatar</span>**: User's avatar

Set user's avatar.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
NSString *avatarPath = @"xxxx/xxxx/xxxx.jpg"; //avatar local path

[helper setUserAvatarWithTransID:transID filePath:avatarPath completion:^(LTSetUserProfileResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
    // LTSetUserProfileResponse including avatar only
} progress:^(LTProgressResponse * _Nullable progressResponse, BOOL isDone) {
    // LTProgressResponse: isDone = YES means upload success
}];
```

Delete user's avatar.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *chID = @"xxxxxxxx";
LTFileInfo *fileInfo = profileImageFileInfo; //user fileInfo

[helper deleteUserAvatarWithTransID:transID fileInfo:fileInfo completion:^(LTSetUserProfileResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

### Get User

This chapter introduces how to get User's setting.

#### APNS Setting: device driven. If the device changed, it would return to its default value

Query `queryDeviceNotifyWithTransID:completion:`, get [APNS Display](#apns-display), [APNS Mute](#apns-mute), [APNS Sound](#apns-sound) and [APNS Token](#apns-token).

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];

[helper queryDeviceNotifyWithTransID:transID completion:^(LTQueryUserDeviceNotifyResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

#### Query User: Query oneself or other user's info

Use queried userID Array, queried phoneNumber (E164) Array and current located 的 brandID, bring in queryUserProfileWithTransID:userIDs:phoneNumbers:brandID:completion:`queryUserProfileWithTransID:userIDs:phoneNumbers:brandID:completion:` to get [User Nickname](#user-nickname) and [User Avatar](#user-avatar).

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *phoneNumber = @"+886901234567";
NSString *userID = @"userID";
NSString *brandID = @"yourBrand";

[helper queryUserProfileWithTransID:transID userIDs:@[userID] phoneNumbers:@[phoneNumber] brandID:brandID completion:^(LTQueryUserProfileResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

#### Query User ID: Use phoneNumber to query oneself or other user's UserID

Use queried phoneNumber (E164) Array and current located brandID to query `queryUserIDWithTransID:phoneNumbers:brandID:completion:`.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *phoneNumber = @"+886901234567";
NSString *brandID = @"yourBrand";

[helper queryUserIDWithTransID:transID phoneNumbers:@[phoneNumber] brandID:brandID completion:^(LTQueryUserDataResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

#### Query PhoneNumber: Use UserID to query phone number

Use userID Array and current located brandID to query `queryPhoneNumberWithTransID:userIDs:completion:`.

```objectivec
NSString *transID = [[NSUUID UUID] UUIDString];
NSString *userID = @"userID";

[helper queryPhoneNumberWithTransID:transID userIDs:@[userID] completion:^(LTQueryUserDataResponse * _Nullable response, LTErrorInfo * _Nullable error) {
    if (error) {
        return;
    }
}];
```

## Receive Event

When setting an online connection, please set **LTIMManager Delegate** and implement its **Protocol**.

-   **Implement Protocol**

```objectivec
@interface MyDelegate : NSObject <LTIMManagerDelegate>
@end
@implemenetation MyDelegate

//Common
- (void)LTIMManagerConnectedWithReceiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerDisconnectedWithReceiver:(NSString * _Nonnull)receiver error:(NSError * _Nullable)error {
}
- (void)LTIMManagerIncomingMessage:(LTMessageResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}

//Channel
- (void)LTIMManagerIncomingCreateChannel:(LTCreateChannelResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingDismissChannel:(LTDismissChannelResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingChannelPreference:(LTChannelPreferenceResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingChannelProfile:(LTChannelProfileResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}

//Channel Member
- (void)LTIMManagerIncomingJoinChannel:(LTJoinChannelResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingInviteMember:(LTInviteMemberResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingKickMember:(LTKickMemberResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingLeaveChannel:(LTLeaveChannelResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingMemberRole:(LTMemberRoleResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}

//Message
- (void)LTIMManagerIncomingSendMessage:(LTSendMessageResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingScheduledMessage:(LTScheduledMessageResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingScheduledInDueTime:(LTScheduledInDueTimeMessageResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingMarkRead:(LTMarkReadResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingDeleteAllMessages:(LTDeleteAllMessagesResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingDeleteChannelMessages:(LTDeleteChannelMessagesResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingDeleteMessages:(LTDeleteMessagesResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingRecall:(LTRecallResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}

//User
- (void)LTIMManagerIncomingSetUserProfile:(LTSetUserProfileResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
- (void)LTIMManagerIncomingModifyUserProfile:(LTModifyUserProfileResponse * _Nullable)response receiver:(NSString * _Nonnull)receiver {
}
@end
```

-   **Set Delegate**

```objectivec
NSString *userID = @"userID";
LTIMManager *manager = [LTSDK getIMManagerWithUserID:userID];
MyDelegate *delegateObject = [[MyDelegate alloc] init];
manager.delegate = delegateObject;
```

### Common

| Method | Description |
| :-- | :-- |
| <span id="LTIMManagerConnectedWithReceiver">`LTIMManagerConnectedWithReceiver:`</span> | Connected to server |
| <span id="LTIMManagerDisconnectedWithReceiver">`LTIMManagerDisconnectedWithReceiver:error:`</span> | Server disconnected |
| <span id="LTIMManagerIncomingMessage">`LTIMManagerIncomingMessage:reciver:`</span> | General Signal event |

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
| <span id="LTIMManagerIncomingCreateChannel">`LTIMManagerIncomingCreateChannel:receiver:`</span> | Receive a create event, query [Channel Information](#channel-information) to sync channel creation info. |
| <span id ="LTIMManagerIncomingDismissChannel">`LTIMManagerIncomingDismissChannel:receiver:`</span> | Receive a dismiss channel event. |
| <span id="LTIMManagerIncomingChannelPreference">`LTIMManagerIncomingChannelPreference:receiver:`</span> | Receive an exclusive setting value. Normally, multiple devices shall receive one user's edited setting. |
| <span id="LTIMManagerIncomingChannelProfile">`LTIMManagerIncomingChannelProfile:receiver:`</span> | Receive shared setting of a chatroom. |

#### LTCreateChannelResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| members | NSSet <[LTMemberProfile](#ltmemberprofile)> | The member's info. |
| count | NSUInteger | The count for create members |

#### LTDismissChannelResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

#### LTChannelPreferenceResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| channelPreference | [LTChannelPreference](#ltchannelpreference) | The `channelPreference` contains changed information, |

#### LTChannelProfileResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| channelProfile | NSDictionary \<NSString, NSString\> | The `channelProfile` contains changed information, including `subject` and `profileImageID`. |

### Channel Member

| Method | Description |
| :-- | :-- |
| <span id="LTIMManagerIncomingJoinChannel">`LTIMManagerIncomingJoinChannel:receiver:`</span> | Receive events when other member's join |
| <span id="LTIMManagerIncomingInviteMember">`LTIMManagerIncomingInviteMember:receiver:`</span> | Receive events when been invited or other members been invited |
| <span id="LTIMManagerIncomingKickMember">`LTIMManagerIncomingKickMember:receiver:`</span> | Receive events when being invited or other members being kicked off |
| <span id="LTIMManagerIncomingLeaveChannel">`LTIMManagerIncomingLeaveChannel:receiver:`</span> | Receive events when other members leaving |
| <span id="LTIMManagerIncomingSetMemberRole">`LTIMManagerIncomingSetMemberRole:receiver:`</span> | Receive events when channel member role changed |

#### LTJoinChannelResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| members | NSSet \<[LTMemberProfile](#ltmemberprofile)\> | The member's info. |

#### LTInviteMemberResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| members | NSSet <[LTMemberProfile](#ltmemberprofile)> | The member's info. |
| count | NSUInteger | The count for invite members |

#### LTKickMemberResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| members | NSSet <[LTMemberProfile](#ltmemberprofile)> | The member's info. |
| count | NSUInteger | The count for kick members |
| reason | NSUInteger | The reason for kick members, default is 0 |
| memberCount | NSUInteger | The count for channel members |
| channelUpdate | NSUInteger | The update tag, that is bit value. Bit0 is member role. Bit1 is channel preference. Bit2 is member changed. |

#### LTLeaveChannelResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| members | NSSet <[LTMemberProfile](#ltmemberprofile)> | The member's info. |
| channelUpdate | NSUInteger | The update tag, that is bit value. Bit0 is member role. Bit1 is channel preference. Bit2 is member changed. |
| memberCount | NSNumber | The count for channel members |

#### LTSetMemberRoleResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| roleID | [LTChannelRole](#ltchannelrole) | The one's role ID in the channel. |
| memberUserID | NSString | The user ID who role changed. |

### Message

| Method | Description |
| :-- | :-- |
| <span id="LTIMManagerIncomingSendMessage">`LTIMManagerIncomingSendMessage:reciver:`</span> | Receive events when members send messages |
| <span id="LTIMManagerIncomingScheduledMessage">`LTIMManagerIncomingScheduledMessage:receiver:`</span> | Receive rescheduled message from a multiple-devices user |
| <span id="LTIMManagerIncomingScheduledInDueTime">`LTIMManagerIncomingScheduledInDueTime:receiver:`</span> | Receive events when a scheduled message sent |
| <span id="LTIMManagerIncomingMarkRead">`LTIMManagerIncomingMarkRead:receiver:`</span> | Receive events of members read message |
| <span id="LTIMManagerIncomingDeleteAllMessages">`LTIMManagerIncomingDeleteAllMessages:receiver:`</span> | Receive delete message from a multiple-devices user |
| <span id="LTIMManagerIncomingDeleteChannelMessages">`LTIMManagerIncomingDeleteChannelMessages:receiver:`</span> | Receive delete chatroom from a multiple-devices user |
| <span id="LTIMManagerIncomingDeleteMessages">`LTIMManagerIncomingDeleteMessages:receiver:`</span> | Receive delete specific message from a multiple-devices user |
| <span id="LTIMManagerIncomingRecall">`LTIMManagerIncomingRecall:receiver:`</span> | Receive events when a member recalls a message |

#### LTSendMessageResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| message | [LTMessage](#ltmessage) | Query [Send Message](#send-message), [Send Broadcast Messages](#send-broadcast-messages) and [Send Scheduled Messages](#send-scheduled-messages) to send messages |

#### LTScheduledMessageResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| chIDs | NSArray | The channel ID array. |
| messages | NSArray | The array of message ([LTMessage](#ltmessage)) which needed to be schedule. |
| timeToSend | long long | The time when the message will send out in millisecond. |

#### LTScheduledInDueTimeMessageResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

#### LTMarkReadResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

#### LTDeleteAllMessagesResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

#### LTDeleteChannelMessagesResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

#### LTDeleteMessagesResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

#### LTRecallResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

| Parameter   | Definition | Description                   |
| :---------- | :--------- | :---------------------------- |
| recallMsgID | NSString   | msgID of the recalled message |

### User

| Method | Description |
| :-- | :-- |
| <span id="LTIMManagerIncomingSetUserProfile">`LTIMManagerIncomingSetUserProfile:receiver:`</span> | Receive user profile change from a multiple-devices user |
| <span id="LTIMManagerIncomingModifyUserProfile">`LTIMManagerIncomingModifyUserProfile:receiver:`</span> | Receive events when chatroom member modifies profile info |

#### LTSetUserProfileResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| userProfile | NSDictionary \<NSString, NSString\> | Current modified [userProfile](#user-profile) value |

#### LTModifyUserProfileResponse

Inherit **[LTMessageResponse](#ltmessageresponse)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| userProfiles | NSArray \<NSDictionary \<NSString, NSString\>\> | chatroom member changed [userProfile](#user-profile) value |

**<span id="user-profile">User Profile</span>**: as Map format.

Map format as below. only retreive when changed:

| key                  | Definition              | Description     |
| :------------------- | :---------------------- | :-------------- |
| userID               | NSString                | User's ID       |
| nickname             | NSString                | User's nickname |
| profileImageFileInfo | LTFileInfo(#ltfileinfo) | User's Avatar   |

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

Y : Yes <br> N : No <br> O : Option <br>

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
| silentMode | BOOL | The server will response True or False accordingly. If True, the server will delete both message content and system log. If False, the server will delete message content but response system log. |
| recallTime | long long | The timestamp when the message is recalled |
| recallBy | NSString | The userID who recall the message |
| senderNickname | NSString | The user nickName who recall the message |

### LTMemberProfile

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| profileImageFileInfo | LTFileInfo | The user's profile info. |
| nickname | NSString | The user nickname. |
| userID | NSString | One of queried userID. UserID, provided by LT, is an unique code for each user. |
| phoneNumber | NSString | The phone number corresponds to the parameter **userID** behind. |

### LTChannelPreference

| Parameter  | Definition | Description                                |
| :--------- | :--------- | :----------------------------------------- |
| ringToneID | NSString   | The ringTone ID in channel for the user.   |
| nickname   | NSString   | The user's nickname in cahnnel.            |
| isMute     | BOOL       | The sound is mute in channel for the user. |

### LTMessage

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| transID | NSString | The client's message ID in UUID(type 1) format. |
| chID | NSString | The channel ID. |
| chType | LTChannelType | The channel type. |
| replyMessage | [LTReplyMessage](#ltmessage) | The object of the Mother message. |
| parentMsgID | NSString | The message ID of the Mother message. |

### LTThumbnailFileMessage

Inherit **[LTFileMessage](#ltfilemessage)**

| Parameter         | Definition | Description                            |
| :---------------- | :--------- | :------------------------------------- |
| thumbnailFileInfo | LTFileInfo | The server return thumbnail file info. |

### LTFileMessage

Inherit **[LTMessage](#ltmessage)**

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| fileName | NSString | The display name for file. |
| extInfo | NSDictionary \<NSString, NSObject\> | Set custom info |
| fileInfo | [LTFileInfo](#ltfileInfo) | The server return file info. |

### LTReplyMessage

| Parameter | Definition | Description |
| :-- | :-- | :-- |
| senderID | NSString | The sender ID of the Mother message. |
| senderNickname | NSString | The sender ID’s nickname of the Mother message. |
| msgID | NSString | The message ID of the Mother message. |
| msgType | LTChannelType | The message types of the Mother message. |
| sendTime | NSString | The send time of the Mother message. |
| displayText | NSString | The display content of the Mother message. |
| extInfo | NSDictionary \<NSString, NSObject\> | The ext info of the Mother message. |
| tagUsers | NSSet \<[LTTagUser](#lttaguser)\> | The tag user of the Mother message(LTTextMessage only). |
| fileInfo | [LTFileInfo](#ltfileInfo) | The file info of the Mother message(LTFileMessage only). |
| thumbnailFileInfo | LTFileInfo | The thumbnail file info of the Mother message(LTFileMessage only). |
| location | [LTLocation](#ltlocation) | The location info of the Mother message(LTLocationMessage only). |

### LTTextMessage

Inherit **[LTMessage](#ltmessage)**

| Parameter  | Definition                          | Description       |
| :--------- | :---------------------------------- | :---------------- |
| msgContent | NSString                            | message content   |
| extInfo    | NSDictionary \<NSString, NSObject\> | self-defined info |
| tagUsers   | NSSet \<[LTTagUser](#lttaguser)\>   | taged user        |

### LTStickerMessage

Inherit **[LTMessage](#ltmessage)**

| Parameter  | Definition                          | Description       |
| :--------- | :---------------------------------- | :---------------- |
| msgContent | NSString                            | sticker content   |
| extInfo    | NSDictionary \<NSString, NSObject\> | self-defined info |

### LTImageMessage

Inherit **[LTFileMessage](#ltfilemessage)**

| Parameter     | Definition | Description         |
| :------------ | :--------- | :------------------ |
| imagePath     | NSString   | original image path |
| thumbnailPath | NSString   | thumbnail path      |

### LTVideoMessage

Inherit **[LTFileMessage](#ltfilemessage)**

| Parameter     | Definition | Description    |
| :------------ | :--------- | :------------- |
| videoPath     | NSString   | video path     |
| thumbnailPath | NSString   | thumbnail path |

### LTVoiceMessage

Inherit **[LTFileMessage](#ltfilemessage)**

| Parameter | Definition | Description     |
| :-------- | :--------- | :-------------- |
| voicePath | NSString   | voice file path |

### LTContactMessage

Inherit **[LTFileMessage](#ltfilemessage)**

| Parameter     | Definition | Description       |
| :------------ | :--------- | :---------------- |
| contactPath   | NSString   | contact file path |
| thumbnailPath | NSString   | thumbnail path    |

### LTLocationMessage

Inherit **[LTMessage](#ltmessage)**

| Parameter | Definition                | Description       |
| :-------- | :------------------------ | :---------------- |
| location  | [LTLocation](#ltlocation) | Maps info         |
| extInfo   | Map\<NSString, NSObject\> | self-defined info |

### LTDocumentMessage

Inherit **[LTFileMessage](#ltfilemessage)**

| Parameter | Definition | Description |
| :-------- | :--------- | :---------- |
| filePath  | NSString   | file path   |

### LTCustomMessage

Inherit **[LTMessage](#ltmessage)**

| Parameter   | Definition                          | Description       |
| :---------- | :---------------------------------- | :---------------- |
| msgContent  | NSString                            | message content   |
| msgCategory | NSString                            | message category  |
| extInfo     | NSDictionary \<NSString, NSObject\> | self-defined info |
| attributes  | Long                                | message attribute |

### LTFileInfo

| Parameter       | Definition | Description         |
| :-------------- | :--------- | :------------------ |
| isExist         | BOOL       | if file exists      |
| filename        | NSString   | file name           |
| remoteFilePath  | NSString   | file remote path    |
| fileContentType | NSString   | file content type   |
| fileSize        | long long  | file size           |
| storageDomain   | NSString   | LT storage domain   |
| storageID       | NSString   | LT storage ID       |
| endpointName    | NSString   | LT storage endpoint |

### LTLocation

| Parameter | Definition | Description        |
| :-------- | :--------- | :----------------- |
| address   | NSString   | location address   |
| latitude  | double     | location latitude  |
| longitude | double     | location longitude |

### LTTagUser

| Parameter | Definition | Description      |
| :-------- | :--------- | :--------------- |
| start     | NSUInteger | tag point starts |
| length    | NSUInteger | tag point length |
| userID    | NSString   | tag user UserID  |
