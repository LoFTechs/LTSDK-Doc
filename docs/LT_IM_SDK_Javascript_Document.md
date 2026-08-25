# <a name = "index">LT IM SDK Javascript Document<a>
-------------------------------------------
Version: 5.3.100
Date: 2026/08/04

**Change History**

|  Version   |Update content
|:----------:|:-----|
|  5.3.1000  |First version.|

---
* [Overview](#overview)
* [安裝說明](#install)
* [使用說明](#use_im) 
	* [初始設定與認證](#im_authentication)
	* [IM SDK基本介紹](#im_introduction)
		* [IMMessage](#im_message)
		* [IQMessage](#im_iqmessage)
	* [連線狀態與監聽](#im_register_status)
		* [連線IM Server](#im_register)
		* [斷線IM Server](#im_unregister)
		* [如何判斷IM Server是否連線](#im_connect)
		* [Monitor IM Server Status](#im_connect_status)
	* [聊天室管理](#im_channel)
		* [建立聊天室](#im_createChannel)
		* [聊天室資訊](#im_getChannelInfo)
		* [聊天室列表](#im_getChannelList)
		* [未讀聊天室列表](#im_getUserUnreadChannelList)
		* [活動群組列表](#im_queryCPChannelList)
		* [Sync chatroom read time with Server](#sync_chatroom_read_time)
		* [獲取聊天室成員已讀時間](#im_queryChannelReadInfo)
		* [獲取各聊天室的最後讀取時間](#im_getChannelReadTime)
		* [修改成員權限](#im_modifyRoleID)	
		* [修改聊天室頭像或名稱](#im_modifyProfile)
		* [修改登入者頭像或暱稱](#im_modifyNickname)
		* [取得使用者頭像與暱稱](#im_getUserProfile)
		* [取得聊天室頭像](#im_getProfile)
		* [取得聊天室名稱](#im_getSubject)
		* [設定聊天室靜音、鈴聲、名稱](#im_channelSetting)
		* [設定聊天室隱藏](#im_channelUserSetting)
		* [設定聊天室屬性](#im_channelUserAttr)
		* [設定聊天室備註](#im_channelMemo)
		* [Get chatroom active user count](#im_getChannelActiveUserCount)
		* [Get chatroom members](#im_getchatroomMembers)
		* [邀請成員](#im_inviteMember)
		* [移除成員](#im_kickMember)
		* [離開聊天室](#im_leaveChannel)
		* [解散聊天室](#im_dismissChannel)
	* [訊息管理](#im_message_manager)
		* [撈取訊息](#im_queryMessage)
		* [傳送訊息](#im_sendMessage)
		* [訊息轉傳](#im_forwardMessage)
		* [訊息廣播](#im_broadcastMessage)
		* [刪除指定訊息](#im_delMessage)
		* [刪除聊天室所有訊息](#im_delMessageByChid)
		* [刪除登入者所有訊息](#im_delAllMessage)
		* [回覆訊息](#im_replyMessage)
		* [回收訊息](#im_recallMessage)
		* [心情訊息](#im_reaction_messages)
		* [預約訊息](#im_scheduled_messages)
		* [Get message read/unread](#get_messages_read_state)
		* [Send channel group messages](#send_channel_group_messages)
	* [投票管理](#im_vote)
		* [建立投票](#im_createVote)
		* [投票](#im_castVote)
		* [投票列表](#im_queryVote)
		* [投票選項資訊](#im_voteOption)
	* [File manage](#storage) 
		* [Get file link (image, video, voice, documents)](#storage_download_file)
	* [Appendix](#appendix)
		* [Appendix A](#appendix_A) 
		* [Appendix B](#appendix_B) 

[Top](#index)
## <a name="overview">Overview</a>

1. 此份文件描述使用LoFTech服務的所有功能與操作。依據這份文件的各項功能規格描述，您便可以透過此SDK介接LoFTech IM服務，以達到您想要完成的各種即時訊息作業，比如: 建立各種聊天室、發送訊息、接收訊息、投票 ... 等等，詳細功能請參考這份文件的目錄列表。

2. 此份文件主要在描述每一項LoFTech SDK功能的呼叫方式與回傳結果，即Request與Return各項參數說明。

3. 每一位使用者LoFTech服務帳號密碼，透過LoFTech認證機制獲得認證資訊

4. 各參數將以(M, String)格式表示此參數的必要性與資料型態

* M/O: M表示此參數是必要的，O表示此參數是可有可無。

String/Integer/Boolean/Objcet:

* String表示字串資料型態
* Integer表示數字資料型態 
* Boolean表示布林資料型態 (true/false)

若未提供該參數，則該參數依據各資料型態的預設值如下:

* String: 表示該參數預設值為空字串。
* Integer: 表示該參數預設值為0。
* Boolean: 表示該參數預設值為false。

[Top](#index)
## <a name="install">安裝說明</a>

1. 使用LoFTech提供之「Javascript SDK」時，從網站(yourcompany.com)上若要傳送檔案到LoFTech儲存服務的後台時，因為離開網站會被瀏覽器判斷為Cross domain行為，所以LoFTech儲存服務的後台需要允許來自於網站的相關網域 (即開放CORS)存取。
如果使用方要在除了網站(yourcompany.com)以外的網站使用此Javascript SDK，那麼使用的這個網域也要先知會LoFTech進行開放，若使用方不特別告知，LoFTech預設會開放 *.yourcompany.com 全部。</br>

	使用方開發者在測試調用的過程中如果遇到瀏覽器console輸出「No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'null' is therefore not allowed access.」錯誤，就表示是CORS問題, 請洽詢LoFTech是否需要額外使用方測試區網域。


2. 將 webimsdk.config.js 與 webimsdk-{version}.min.js 加入頁面

```
<script type='text/javascript' src='./webimsdk.config.js'></script>
<script type='text/javascript' src='./webimsdk-{version}.min.js'></script>
```

[Top](#index)
## <a name="use_im">使用說明</a>
### <a name="im_authentication">初始設定與認證</a>

在使用SDK前請務必設定以下初始化。

#### webimsdk.config

```
var BRAND_ID = brandID;
var BOSH_SERVICE = boshServiceUrl;
var IM_SERVER = imServerUrl;
var STORAGE_DOMAIN = storageDomain;
var STORAGE_ACCOUNT = storageAccount;
```
#### 參數說明及必要性

以下五個參數，皆由LoFTech配發，請洽LoFTech技術服務人員。

|常用參數    | 定義	| 	必要性	|備註|
| :------:| :------: |:------: |:------: |
| BRAND_ID 			| 品牌ID| 必要	|如：ltsdk|
| BOSH_SERVICE 		| IM Sever的http用來binding的Url| 必要 |測試開發區及正式上線區的Url會有所差異|
| IM_SERVER 		   	| IM Server所提供的專屬Domain | 必要 |測試開發區及正式上線區的Url會有所差異|
| STORAGE_DOMAIN 	| 檔案管理主機網域| 非必要 |使用[LT的檔案管理](#storage)才需要加，測試開發區及正式上線區的Url會有所差異|
| STORAGE_ACCOUNT  	| 檔案管理帳號| 非必要 |使用[LT的檔案管理](#storage)才需要加，測試開發區及正式上線區的Url會有所差異|

### <a name="im_introduction">IM SDK基本介紹</a>
#### <a name="im_message">IMMessage</a>
After sending out the \<message\> protocol to the IM Server, once the IM Server processes the Request it will return a Response message to the Sender and associated Members(for example: a chatroom and its members). Not only you can get the response from each API in IM SDK, but also from imMessageCallback of [Monitor IM Server Status](#im_connect_status).

#### Base Parameters
* messageType (M, MessageType): The whole message types were in MessageType object of IM SDK. The followings were message type in MessageTypeBasic,
	* **TEXT** *(M, Integer)* : Message Type 1，為[傳送訊息](#im_sendMessage)中傳送文字所屬Type.
	* **EMOJI** *(M, Integer)* : Message Type 2，為[傳送訊息](#im_sendMessage)中傳送貼圖所屬Type.
	* **PHOTO** *(M, Integer)* : Message Type 3，為[傳送訊息](#im_sendMessage)中傳送圖片所屬Type.
	* **VIDEO** *(M, Integer)* : Message Type 4，為[傳送訊息](#im_sendMessage)中傳送影片所屬Type.
	* **AUDIO** *(M, Integer)* : Message Type 5，為[傳送訊息](#im_sendMessage)中傳送聲音所屬Type.
	* **CONTACT** *(M, Integer)* : Message Type 6，為[傳送訊息](#im_sendMessage)中傳送聯絡人所屬Type.
	* **LOCATION** *(M, Integer)* : Message Type 7，為[傳送訊息](#im_sendMessage)中傳送位置所屬Type.
	* **DOCUMENT** *(M, Integer)* : Message Type 8，為[傳送訊息](#im_sendMessage)中傳送文件所屬Type.
	* **CREATE\_CHANNEL** *(M, Integer)* : Message Type 1001，為[建立聊天室](#im_createChannel)中傳送文件所屬Type.
	* **CREATE\_CHANNEL\_V2** *(M, Integer)* : Message Type 1017，為[建立聊天室](#im_createChannel)中傳送文件所屬Type.
	* **MARK\_READ** *(M, Integer)* : Message Type 1101，為[設定聊天室已讀時間](#im_queryChannelRead)中傳送文件所屬Type.
	* **SET\_MEMBER\_ROLE\_ID** *(M, Integer)* : Message Type 1010，為[修改成員權限](#im_modifyRoleID)中傳送文件所屬Type.
	* **SET\_MEMBER\_ROLE\_ID\_V2** *(M, Integer)* : Message Type 1018，為[修改成員權限](#im_modifyRoleID)中傳送文件所屬Type.
	* **SET\_CHANNEL\_PROFILE** *(M, Integer)* : Message Type 1015，為[修改聊天室頭像或名稱](#im_modifyProfile)中傳送文件所屬Type.
	* **SET\_USER\_PROFILE** *(M, Integer)* : Message Type 1012，為[修改登入者頭像或暱稱](#im_modifyNickname)中傳送文件所屬Type.
	* **SET\_CHANNEL\_PREF** *(M, Integer)* : Message Type 1011，為[設定聊天室靜音、鈴聲、名稱](#im_channelSetting)中傳送文件所屬Type.
	* **INVITE\_MEMBER** *(M, Integer)* : Message Type 1004，為[邀請成員](#im_inviteMember)中傳送文件所屬Type.
	* **KICK\_MEMBERS** *(M, Integer)* : Message Type 1006，為[移除成員](#im_kickMember)中傳送文件所屬Type.
	* **LEAVE\_CHANNEL** *(M, Integer)* : Message Type 1003，為[離開聊天室](#im_leaveChannel)中傳送文件所屬Type.
	* **DISMISS\_CHANNEL** *(M, Integer)* : Message Type 1002，為[解散聊天室](#im_dismissChannel)中傳送文件所屬Type.   
	* **DELETE\_MESSAGE** *(M, Integer)* : Message Type 1102，為[刪除指定訊息](#im_delMessage)中傳送文件所屬Type.
	* **DELETE\_CHANNEL\_MESSAGE** *(M, Integer)* : Message Type 1103，為[刪除聊天室所有訊息](#im_delMessageByChid)中傳送文件所屬Type.
	* **RECALL\_MESSAGE** *(M, Integer)* : Message Type 1104，為[回收訊息](#im_recallMessage)中傳送文件所屬Type.
	* **DELETE\_ALL\_MESSAGE** *(M, Integer)* : Message Type 1105，為[刪除登入者所有訊息](#im_delAllMessage)中傳送文件所屬Type.
	* **PUSH\_CHANNEL\_ACTIVE\_USER** *(M, Integer)*:Message Type 1106, defined as [Get channel active user count](#im_getChannelActiveUserCount).
	* **VOTE\_QUESTION** *(M, Integer)* : Message Type 101，為[建立投票](#im_createVote)中傳送文件所屬Type.
	* **VOTED\_OPTION** *(M, Integer)* : Message Type 109，為[投票](#im_castVote)中傳送文件所屬Type.

[Top](#index)
#### <a name="im_iqmessage">IQMessage</a>
XMPP \<iq\>協定產生物件，送出<iq>協定給IM Server後，當IM Server處理完後，只會先發出Response給發送端。在IM SDK中除了每隻API呼叫的callback方法得到，在[監聽IM Server 狀態](#im_connect_status)中iqMessageCallback收到。若有使用批次時則會多次呼叫API中callback方法，或是直接使用[監聽IM Server 狀態](#im_connect_status)中設定的IQMessageReceiver收到。

#### Base Parameters		
* **messageItem** *(M, String)* : iqMessage.getItem()可取得，是XMPP中 <iq>協定中header的Item項目。
	* **kIQQueryChannelReadInfo** *(M, String)* : 為[獲取聊天室成員已讀時間](#im_queryChannelReadInfo)所屬Type.
	* **kIQListChannels** *(M, String)* : 為[聊天室資訊](#im_getChannelInfo)與[聊天室列表](#im_getChannelList)所屬Type.
	* **kIQGetUserUnreadChannels** *(M, String)* : 為[未讀聊天室列表](#im_getUserUnreadChannelList)所屬Type.
	* **kIQQueryMessage** *(M, String)* : 為[撈取訊息](#im_queryMessage)所屬Type.
	* **kIQListChannelsReadTime** *(M, String)* : 為[獲取各聊天室的最後讀取時間](#im_getChannelReadTime)所屬Type.
	* **kGetUserProfile** *(M, String)* : 為[取得使用者頭像與暱稱](#im_getUserProfile)所屬Type.
	* **kIQGetChannelProfile** *(M, String)* : 為[取得聊天室頭像](#im_getProfile)所屬Type.
	* **kIQGetChannelSubject** *(M, String)* : 為[取得聊天室名稱](#im_getSubject)所屬Type.
	* **kIQCorporationChannelList** *(M, String)* : 為[活動群組列表](#im_queryCPChannelList)所屬Type.
	* **kIQQueryVotingOptions** *(M, String)* : 為[投票選項資訊](#im_voteOption)所屬Type.
	* **kIQQueryVoteList** *(M, String)* : 為[投票列表](#im_queryVote)所屬Type.

[Top](#index)
### <a name="im_register_status">連線狀態與監聽</a>
#### <a name="im_register">連線IM Server</a>
在與IM server 送訊息或任何訊令前，須先與IM Server建立起連線。

#####初始化連線

```
initConnection();
```

#####IM帳號登入IM Server

* **imUserID** (UserID): LoFTech配發給每一位使用者的專屬使用者代號，使用者代號請洽LoFTech技術服務人員。
* **imUserPw** : LoFTech配發給每一位使用者的專屬密碼，如何帶入密碼方式請洽LoFTech技術服務人員。

```
connectIMServer(imUserID, imUserPw);
```

[Top](#index)
#### <a name="im_unregister">斷線IM Server</a>
若不需要與IM Server通訊，可以與IM Server中斷連線。

```
disconnectIMServer();
```

[Top](#index)
#### <a name="im_connect">IM Server是否連線</a>
在建立連線前，網路切換時或是有其他時機點需要先確認與IM server是否有連線，可利用isConnectIMServer()來確認。

```
var isConnect = isConnectIMServer();
```

[Top](#index)
#### <a name="im_connect_status">監聽IM Server 狀態</a>
設定監聽後才可正常監聽到IM Server的訊息與狀態。

##### 監聽接收 IM Server連線狀態

```
function connectionStateObserver(status) {
	...
}

// Set Observer
setConnecttionStateObserver(connectionStateObserver);
```
##### 監聽接收 IM IQ Message

```
function recvMessageIQ(msgIQ) {
	...
}

// Set Observer
setIQMessageReceiver(recvMessageIQ);
```

##### 監聽接收 IM message
```
function recvMessage(msg) {
	...
}

// Set Observer
setMessageReceiver(recvMessage);
```

[Top](#index)
### <a name="im_channel">聊天室管理</a>
#### <a name="im_createChannel">建立聊天室</a>
若要與對方聊天時，需要先建立聊天室，才可以進行互動。<br/>
此為 im message請參考[IMMessage](#im_message).<br/>
建立完聊天室後所得到的回應並非為聊天室所有的資訊，請務必呼叫[聊天室資訊](#im_getChannelInfo)，取得更詳細的聊天室資訊。<br/>

以下有幾個方法可以建立不同類型的聊天室。

1.一對一聊天室

The chID of one to one chatroom is combined with two userIDs and one colon. The Server backend check the chID by investigating if this userID is part of chID or not. If the userID is not part of chID, this chatroom can’t be created.

#### Request parameters description
* **userID** *(M, String)* : Join userID.
* **callback** *(O, Object)* : Response function.

```
createOneToOneChannel(userID, function(obj) {
	...
});
```

2.我的檔案

#### Request parameters description
* **callback** *(O, Object)* : Response function.

```
createSelfChannel(function(obj) {
	...                
});
```

3.群組聊天室

When creating group channel, at least need to add one userID. This one
can be oneself.

chNickname 參數，系統將會為該成員設定專屬的頻道暱稱（內部屬性 isChNickname=true）。 顯示優先權限制： 頻道暱稱的顯示優先級高於全域暱稱。若成員已於特定頻道中設定頻道暱稱，後續即使透過 set_user_profile 更新全域暱稱，該頻道內的顯示名稱仍會維持原頻道暱稱，不會進行連動更新。 開發建議： 若業務情境預期成員名稱需跨頻道全域同步連動，建議於呼叫建立頻道或邀請成員 API 時，略過傳遞 chNickname 參數。

#### `UserObject` object
Property | Type | Description
--- | --- | ---
userID | String | The user ID.
roleID |RoleID| The one's role ID in the channel.
chNickname | String | The sender's nickname of the last message in the channel.
chProfileID | String | The member's profile image ID in channel.

#### Request parameters description
* **subject** *(M, String)* : Channel subject.
* **userIDs** *(M, Array)* : String array or UserObject array.
* **callback** *(O, Object)* : Response function.

```
userIDs = ['UserID1', 'UserID2'];
createGroupChannel(subject, userIDs, function(obj) {
	...         
});
```


```
userObj = new UserObject(UserID1, RoleID.MODERATOR_ROLEID, 'Channel Manager');
createGroupChannel(subject, [userObj], function(obj) {
	...         
});

```

4.自訂聊天室

若需要建立客製化的聊天室，可使用自訂聊天室的建立方式。若需要自訂聊天室需與IM server申請。

可自行輸入ChannelID(chID) 與 ChannelType(chType).

ChannelID: 呼叫端系統自行產生且符合UUID的唯一識別碼，若不符合UUID格式，將會遭到IM Server拒絕。可透過 getNewGroupChannelID() 產生。

```
var channelID = getNewGroupChannelID();
```

ChannelType: 已制訂一些既有的格式，但仍可增加自訂的type，增加自訂的type需要與IM server申請，否則無法建立成功。可透過 getChTypeString() 獲得ChType 對應的字串值。

```
var chTypeString = getChTypeString(ChannelType.SYSTEM_CHATROOM);
```

另外，以下格式無法利用此方法新增：

* ChannelType.ONE_TO_ONE_CHATROOM ("1"): 請利用createOneToOneChannel。
* ChannelType.CHATROOM ("2") : 不提供建立。
* ChannelType.COMMUNITY ("3") :  不提供建立。
* ChannelType.AOTP_CHATROOM ("6") : 不提供建立。
* ChannelType.MEETING ("8") : 不提供建立。
* ChannelType.COMPANY_ENTERPRISE ("9") : 不提供建立.
* ChannelType.COMPANY_ONE_TO_ONE_CHATROOM ("10") : 不提供建立。
* ChannelType.SYSTEM ("system") : 不提供建立。
* ChannelType.SYSTEM_TO_USER ("sys2user") : 不提供建立。
* ChannelType.SELF ("self") : 請利用createSelfChannel。
* ChannelType.TIME_TO_SEND ("timetosend") : 不提供建立。


#### Request parameters description
* **chID** *(M, String)* : The channel UUID.
* **chType** *(M, Object)* : ChannelType object.
* **subject** *(M, String)* : channel subject.
* **userIDs** *(M, Array)* : String array or UserObject array.
* **callback** *(O, Object)* : Response function.

```
userIDs = ['UserID1', 'UserID2'];
createChannel(chID, chType, subject, userIDs, function(obj) {
	...         
});
```


```
userObj = new UserObject(UserID1, RoleID.MODERATOR_ROLEID, 'Channel Manager');
createChannel(chID, chType, subject, [userObj], function(obj) {
	...         
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful.
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : chatroom
* **msgContent** *(M, String)* : The User IDs (seperated by comma) of all members invited to join the channel.
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : 1001
* **extInfo** *(M, Integer)* : The subject of the created channel.
* **senderNickname** *(M, Integer)* : The user nickname who create the channel.
* **sendTime** *(M, Integer)* : The channel creation message's sent time.
* **senderID** *(M, String)* : The user ID who create the channel.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message.
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.

#### Response Example

```
{
  "returnCode": 0,
  "chID": "bfd0159a-8e87-492d-9c9d-8252c44542d1",
  "chType": "4",
  "encrypted": false,
  "isRead": false,
  "msgCategory": "chatroom",
  "msgContent": "avzrg6l7tac73trb,avzrg6ltsac74o3f,avzrg6mgracakvux",
  "msgID": "d1f2d6e0-96d1-11e7-ae46-1a2db0016f11",
  "msgType": 1001,
  "extInfo": "SDK Test Subject",
  "senderNickname": "test nickname",
  "sendTime": 1505121357277,
  "senderID": "avzrg6mgracakvux",
  "timeToSend": 1505121357277,
  "transID": "034c621d-6314-49e6-b1e8-4eb4a6708ebe"
}
```

[Top](#index)
#### <a name="im_getChannelInfo">聊天室資訊</a>
取得單一聊天室的資訊資訊，此為iq message，請參考[IQMessage](#im_iqmessage).<br/>

#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **withMembers** *(O, Boolean)*: This is optional, default is *true*. If the value is true, the result will contains the all members' information with *memberPrivilege* tag. Otherwise, the result will not contain any member information with *memberPrivilege* empty. For performance issue, strongly suggest the value is false, not to return members' information.
* **callback** *(O, Object)* : Response function.

```
queryChannel(chID, withMembers, function(obj) {
	...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful.
* **channels** *(M, Array)* :
	* **availCategory** *(M, Integer)* : The following bits indiate the channel supported category of
messages. Bit 0: chat room, Bit 1: voting.
	* **chID** *(M, String)* : The channel ID.
	* **chProperties** *(M, Integer)* : The channel's other properties. Reference [channels] table.
	* **chType** *(M, String)* : The channel type. Currently, only types '1','2','3','4','5' are supported. It can be extended as bussiness needs.
	* **createTime** *(M, Integer)* : The channel created timestamp in milliseconds.
	* **createUserID** *(M, String)* : The User ID who create the channel.
	* **fileSizeLimit** *(M, Integer)* : The file size limit in KBytes of each file which can be sent in the channel.
	* **inviteTime** *(M, Integer)* : 使用者被邀請加入此Channel的Timestamp時間(milliseconds)。
	* **inviteUserID** *(M, String)* : 要請使用者加入此Channel的使用者UserID。
	* **isMute** *(M, Integer)* : The value is true, if the receiver set the channel muted.
	* **joinTime** *(M, Integer)* : 加入此Channel的Timestamp時間(milliseconds).
	* **lastMsgContent** *(M, String)* : The receiver's last message in the channel.
	* **lastMsgID** *(M, String)* : The receiver's last message's ID in the channel.
	* **lastMsgSenderID** *(M, String)* : The receiver's last message's userID in the channel.
	* **lastMsgSenderNickname** *(M, String)* : The receiver's last message's nick name in the channel.
	* **lastMsgSenderProfileID** *(M, String)* : The receiver's last profile info in the channel.
	* **lastMsgTime** *(M, Integer)* : The receiver's last message's send time in the channel.
	* **lastMsgType** *(M, Integer)* : The receiver's last message type in the channel.
	* **lastReadTime** *(M, Integer)* : The receiver's last read time in the channel.
	* **maxMembers** *(M, Integer)* :	The maximum number of members in the channel.
	* **minMembers** *(M, Integer)* :	The minimum number of members in the channel.
	* **profileBackImageID** *(M, String)* : The channel's profile back image uuid.
	* **profileImageID** *(M, String)* :	 The channel's profile image uuid.	
	* **ranking** *(M, Integer)* : 分類的排列順序,預設值為0，Client端將以升冪方式排序顯示。
	* **ringToneID** *(M, String)* : The ring tone ID which the receiver set in the channel. The value is null or empty string, if the receiver did not set.
	* **subject** *(M, String)* : The subject of the channel.
	* **unreadCount** *(M, Integer)* : unread message count in channel.
	* **updateTime** *(M, Integer)* : The channel updated timestamp in milliseconds.
	* **updateUserID** *(M, String)* : The User ID who update the channel's properties (subject or profile image).
	* **privilege** *(M, Object)* : 使用者自己在此Channel所具有的權限。 可利用getPrivilege()取得Map Object，其中Map Object中的key即其為以下變數。
		* **affID** *(M, Integer)* : The receiver's affiliation ID.
		* **affPriv** *(M, Integer)* : Reference [affiliationdefaultpriv] table.
		* **banMembers** *(M, Integer)* : Reference [affiliationdefaultpriv] table.
		* **canSendMsgType** *(M, Integer)* : Reference [affiliationdefaultpriv] table.
		* **getMemberList** *(M, Integer)* : Reference [affiliationdefaultpriv] table.
		* **inviteRoleID** *(M, Integer)* : Reference [roledefaultpriv] table.
		* **kickRoleID** *(M, Integer)* : Reference [roledefaultpriv] table.
		* **recvMsg** *(M, Integer)* : Reference [roledefaultpriv] table.
		* **recvPres** *(M, Integer)* : Reference [roledefaultpriv] table.
		* **roleID** *(M, Integer)* : The receiver's role ID.
		* **roleName** *(M, String)* : The receiver's role name.
		* **rolePriv** *(M, Integer)* : Reference [roledefaultpriv] table.
		* **setMemberAffID** *(M, Integer)* : Reference [affiliationdefaultpriv] table.
		* **setMemberRoleID** *(M, Integer)* : Reference [roledefaultpriv] table.
	* **memberPrivilege** *(M, Array)* : 此Channel所有Members(包括自己)的Role & Affiliation權限代號。 可利用getMemberPrivilege()取得Map Array Object，其中Map Object中的key即其為以下變數。
		* **affID** *(M, Integer)* : The other user's affiliation ID.
		* **affTime** *(M, Integer)* : The other user's affiliation time.
		* **brandID** *(M, String)* : The other user's brand ID
		* **isChProfileImage** *(M, Boolean)* : The above profileID is specified for the channel.
		* **joinTime** *(M, Integer)* : join time what the user into channel
		* **lastReadTime** *(M, Integer)* : The other user's last read time in the channel.
		* **nickname** *(M, String)* : The other user's nick name
		* **phoneNumber** *(M, String)* : The other user's phone number
		* **profileID** *(M, String)* : The other user's profile image ID.
		* **roleID** *(M, Integer)* : The other user's role id in the channel.
		* **userID** *(M, String)* : The other user ID in the channel.
	* **memberCount** *(M, Long)* : The total joined number of members per chatroom.
	* **isLiveMode** *(M, Boolean)* : The value is true, if thie channel is live mode.

#### Response Example

```
{
	"returnCode": 0,
  	"channels": [{
		"availCategory": 1,
		"chID": "avzrg6mgracakvux:avzrg6mkjqchp5o4",
		"chProperties": 3360,
		"chType": "1",
		"createTime": 1503404261057,
		"createUserID": "avzrg6mkjqchp5o4",
		"fileSizeLimit": 102400,
		"inviteTime": 1503404261057,
		"inviteUserID": "avzrg6mkjqchp5o4",
		"isMute": false,
		"joinTime": 1503404261057,
		"lastMsgContent": "1,8,0",
		"lastMsgID": "7a68eb68-88a7-11e7-ae46-1a2d3100478b",
		"lastMsgSenderID": "avzrg6mgracakvux",
		"lastMsgSenderNickname": "",
		"lastMsgSenderProfileID": "",
		"lastMsgTime": 1503563855270,
		"lastMsgType": 2,
		"lastReadTime": 1503563548583,
		"maxMembers": 2,
		"memberPrivilege": [{
			"affID": 4,
			"affTime": 1503404261057,
			"brandID": "ltsdk",
			"isChProfileImage": false,
			"joinTime": 1503404261057,
			"lastReadTime": 1503563855270,
			"nickname": "",
			"phoneNumber": "0999000314",
			"profileID": "",
			"roleID": 4,
			"userID": "avzrg6mgracakvux"
		}, ...],
		"minMembers": 0,
		"privilege": {
			"affID": 8,
			"affName": "Admin",
			"affPriv": 122814,
			"banMembers": 6,
			"canSendMsgType": 1023,
			"getMemberList": 14,
			"inviteRoleID": 4,
			"kickRoleID": 6,
			"recvMsg": 12,
			"recvPres": 12,
			"roleID": 8,
			"roleName": "Moderator",
			"rolePriv": 1023,
			"setMemberAffID": 12,
			"setMemberRoleID": 12
		},
		"profileBackImageID": "",
		"profileImageID": "",
		"ranking": 0,
		"ringToneID": null,
		"subject": "",
		"unreadCount": 8,
		"updateTime": 1503404261057,
		"updateUserID": "avzrg6mkjqchp5o4",
		"memberCount:" 1,
		"isLiveMode": false}]
}
```
[Top](#index)
#### <a name="im_getChannelList">聊天室列表</a>

以下有幾種方法可以取得聊天室列表。

1.使用最後更新時間取得聊天室列表。
此為iq message，請參考[IQMessage](#im_iqmessage).<br/>

#### Request parameters description
* **chType** *(O, String/Array)* : This is optional. If defined, IM server will query those channels with this
                             specified chtype(s) only and those are belong to the sender. The value can be a string or an array
                             of strings.
* **withMembers** *(O, Boolean)* : This is optional, default is *true*. If the value is true, the result will contains the all members' information with *memberPrivilege* tag. Otherwise, the result will not contain any member information with *memberPrivilege* empty. For performance issue, strongly suggest the value is false, not to return members' information.
* **updateTime** *(M, Integer)* : Just list channels those update time is grater than the **lastUpdateTime** value.
* **batchCount** *(M, Integer)* : The number of batch count. (Range: 5 ~ 100)
* **sortType** *(O, Integer)* : This argument is optional, if not inputted the default shall be SortType.LAST_MSG_TIME. If the value is SortType.LAST_MSG_TIME, the result of channels will be sorted by chatrooms' lastMsgTime (last message time). If the value is SortType.UNREAD_MSG_TIME and sortOrder’s value is DESC, the returned result will first be the list of channels with unread messages, then followed by the list of channels without unread messages. Both indivdually sorted by channels' lastMsgTime in descending order. If the value is SortType.UNREAD_MSG_TIME and sortOrder’s value is ASC, the returned result will be the exact reverse of sortType’s value is SortType.UNREAD_MSG_TIME and sortOrder’s value is DESC.
* **sortOrder** *(O, Integer)* : This is optional, default is SortOrder.DESCENDING. If the value is SortOrder.ASCEDING, the result of channels will be sorted by asceding order based on the above *sortType* field. If the value is SortOrder.DESCENDING, The result is sorted by descending order.
* **count** *(M, Integer)* : The number of channels to be returned for the request. The maximum of the count value is 100. If you want to request more than 100 channels, you can request multiple times and with markChID to specify the previous request's last chID.
* **callback** *(O, Object)* : Response function.

```
getListChannelsByUpdateTime(chType, withMembers, updateTime, batchCount, SortType.LAST_MSG_TIME, SortOrder.ASCEDING, count, function(obj) {
    ...
});
```

2.使用Channel ID取得後面聊天室列表。
此為iq message，請參考[IQMessage](#im_iqmessage).<br/>

#### Request parameters description
* **chID** *(O, String)* : This is optional, default is "" empty string. If the value is specified with non-empty string, the request result will only return channels those chID is grater or less the the *chID* value. This is used for multiple requests of all channels. The first request is with markChID is empty string. Then, the following requests should specify the *chID* with the previous request's last channel's chID.
* **chType** *(O, String/Array)* : This is optional. If defined, IM server will query those channels with this
                             specified chtype(s) only and those are belong to the sender. The value can be a string or an array
                             of strings.
* **withMembers** *(O, Boolean)*: This is optional, default is *true*. If the value is true, the result will contains the all members' information with *memberPrivilege* tag. Otherwise, the result will not contain any member information with *memberPrivilege* empty. For performance issue, strongly suggest the value is false, not to return members' information. 
* **batchCount** *(M, Integer)* : The number of batch count. (Range: 5 ~ 100)
* **sortType** *(O, Integer)* : This argument is optional, if not inputted the default shall be SortType.LAST_MSG_TIME. If the value is SortType.LAST_MSG_TIME, the result of channels will be sorted by chatrooms' lastMsgTime (last message time). If the value is SortType.UNREAD_MSG_TIME and sortOrder’s value is DESC, the returned result will first be the list of channels with unread messages, then followed by the list of channels without unread messages. Both indivdually sorted by channels' lastMsgTime in descending order. If the value is SortType.UNREAD_MSG_TIME and sortOrder’s value is ASC, the returned result will be the exact reverse of sortType’s value is SortType.UNREAD_MSG_TIME and sortOrder’s value is DESC.
* **sortOrder** *(O, Integer)* : This is optional, default is SortOrder.DESCENDING. If the value is SortOrder.ASCEDING, the result of channels will be sorted by asceding order based on the above *sortType* field. If the value is SortOrder.DESCENDING, The result is sorted by descending order.
* **count** *(M, Integer)* : The number of channels to be returned for the request. The maximum of the count value is 100. If you want to request more than 100 channels, you can request multiple times and with markChID to specify the previous request's last chID.
* **callback** *(O, Object)* : Response function.

```
getListChannelsByChID(chID, chType, withMembers, batchCount, SortType.LAST_MSG_TIME, SortOrder.ASCEDING, count, function(obj) {
    ...
});
```
3.使用最後訊息時間取得聊天室列表。
 
#### Request parameters description
* **chType** *(O, Array)* : This is optional. If defined, IM server will query those channels with this
                             specified chtype(s) only and those are belong to the sender. The value can be a string or an array
                             of strings.
* **withMembers** *(O, Boolean)*: This is optional, default is *false*. If the value is true, the result will contains the all members' information with *memberPrivilege* tag. Otherwise, the result will not contain any member information with *memberPrivilege* empty. For performance issue, strongly suggest the value is false, not to return members' information.
* **lastMsgTime** *(M, Integer)* : Just list channels whose last message time are grater or less than the **lastMsgTime** value.                              
* **unReadType** *(O, Integer)* : Retrieve the channels by “read-unread” status. If the value is “ UnReadChannelType.ALLCHANNELTYPE”, all the channel would be retrieved. If the value is “UnReadChannelType.UNREADMESSAGECHANNEL”, those channel with any unread messages would be retrieved. If the value is “UnReadChannelType.READMESSAGECHANNEL”, those channel with any read messages would be retrieved. The definition of “read-unread” is unread count = 0 while unread count > 0.
* **sortOrder** *(O, Integer)* : This argument is optional, default is SortOrder.ASCEDING. If the value is SortOrder.ASCEDING, the result of channels will be sorted by channels' lastMsgTime (last message time) by ascending order. Otherwise, if the value is SortOrder.DESCENDING, the result of channels will be sorted by channels' lastMsgTime (last message time) by descending order. 
* **count** *(M, Integer)* : The number of channels to be returned for the request, the default value is 10. If the value is positive number, those channels with last message time > lastMsgTime will be returned. Otherwise, it the value is negative number, those channels with last message time < lastMsgTime will be returned. The maximum of the count value is 100 (Range: -100~100). If you want to request more than 100 channels, you can request multiple times by specifying the lastMsgTime tag for next request.
* **userAttr** *(O, Integer)*: 0: only return channels whose userAttr value is 0. > 0: only return channels whose userAttr g value are not 0. -1:all channels will be returned no matter what the userAttr value is.
* **userSetting** *(O, Integer)*: "UserSetting.USER_SETTING_ALL": all channels will be returned no matter what the userSetting value is. "UserSetting.USER_SETTING_SHOW": only return channels whose userStting value is 0. "UserSetting. USER_SETTING_HIDDEN": only return channels whose userStting value are not 0.
* **callback** *(O, Object)* : Response function.

```
getListChannelsByLastMsgTime(chTypeArray, withMembers, lastMsgTime, UnReadChannelType.UNREAD_MESSAGE_CHANNEL, SortOrder.ASCEDING, 20, function(obj) {
    ...
});
```

```
getListChannelsWithSettingByLastMsgTime(chTypeArray, withMembers, lastMsgTime, UnReadChannelType.UNREAD_MESSAGE_CHANNEL, SortOrder.ASCEDING, 20, 1, UserSetting.USER_SETTING_ALL, function(obj) {
	...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **batchNo** *(M, Integer)* : 當前的批次號碼 (只有使用批次參數才會出現)。
* **batchTotal** *(M, Integer)* : 批次總數 (只有使用批次參數才會出現)。
* **channels** *(M, Array)* :
	* **availCategory** *(M, Integer)* : The following bits indiate the channel supported category of
messages. Bit 0: chat room, Bit 1: voting.
	* **chID** *(M, String)* : The channel ID.
	* **chProperties** *(M, Integer)* : The channel's other properties. Reference [channels] table.
	* **chType** *(M, String)* : The channel type. Currently, only types '1','2','3','4','5' are supported. It can be extended as bussiness needs.
	* **createTime** *(M, Integer)* : The channel created timestamp in milliseconds.
	* **createUserID** *(M, String)* : The User ID who create the channel.
	* **fileSizeLimit** *(M, Integer)* : The file size limit in KBytes of each file which can be sent in the channel.
	* **inviteTime** *(M, Integer)* : 使用者被邀請加入此Channel的Timestamp時間(milliseconds)。
	* **inviteUserID** *(M, String)* : 要請使用者加入此Channel的使用者UserID。
	* **isMute** *(M, Integer)* : The value is true, if the receiver set the channel muted.
	* **joinTime** *(M, Integer)* : 加入此Channel的Timestamp時間(milliseconds)。
	* **lastMsgContent** *(M, String)* : The receiver's last message in the channel.
	* **lastMsgID** *(M, String)* : The receiver's last message's ID in the channel.
	* **lastMsgSenderID** *(M, String)* : The receiver's last message's userID in the channel.
	* **lastMsgSenderNickname** *(M, String)* : The receiver's last message's nick name in the channel.
	* **lastMsgSenderProfileID** *(M, String)* : The receiver's last profile info in the channel.
	* **lastMsgTime** *(M, Integer)* : The receiver's last message's send time in the channel.
	* **lastMsgType** *(M, Integer)* : The receiver's last message type in the channel.
	* **lastReadTime** *(M, Integer)* : The receiver's last read time in the channel.
	* **maxMembers** *(M, Integer)* :	The maximum number of members in the channel.
	* **minMembers** *(M, Integer)* :	The minimum number of members in the channel.
	* **profileBackImageID** *(M, String)* : The channel's profile back image uuid.
	* **profileImageID** *(M, String)* :	 The channel's profile image uuid.	
	* **ranking** *(M, Integer)* : 分類的排列順序,預設值為0，Client端將以升冪方式排序顯示。
	* **ringToneID** *(M, String)* : The ring tone ID which the receiver set in the channel. The value is null or empty string, if the receiver did not set.
	* **subject** *(M, String)* : The subject of the channel.
	* **unreadCount** *(M, Integer)* : unread message count in channel.
	* **updateTime** *(M, Integer)* : The channel updated timestamp in milliseconds.
	* **updateUserID** *(M, String)* : The User ID who update the channel's properties (subject or profile image).
	* **userSetting** *(M, Integer)* : The channel Setting (0 : show/ 1: hidden).
	* **userAttr** *(M, Integer)* : The channel Attribute for the user.
	* **memo** *(M, String)* : The channel memo for the user.
	* **privilege** *(M, Integer)* : 使用者自己在此Channel所具有的權限。可利用getPrivilege() 取得Map Object，其中Map Object中的key即其為以下變數。
		* **affID** *(M, Integer)* : The receiver's affiliation ID.
		* **affPriv** *(M, Integer)* : Reference [affiliationdefaultpriv] table.
		* **banMembers** *(M, Integer)* : Reference [affiliationdefaultpriv] table.
		* **canSendMsgType** *(M, Integer)* : Reference [affiliationdefaultpriv] table.
		* **getMemberList** *(M, Integer)* : Reference [affiliationdefaultpriv] table.
		* **inviteRoleID** *(M, Integer)* : Reference [roledefaultpriv] table.
		* **kickRoleID** *(M, Integer)* : Reference [roledefaultpriv] table.
		* **recvMsg** *(M, Integer)* : Reference [roledefaultpriv] table.
		* **recvPres** *(M, Integer)* : Reference [roledefaultpriv] table.
		* **roleID** *(M, Integer)* : The receiver's role ID.
		* **roleName** *(M, String)* : The receiver's role name.
		* **rolePriv** *(M, Integer)* : Reference [roledefaultpriv] table.
		* **setMemberAffID** *(M, Integer)* : Reference [affiliationdefaultpriv] table.
		* **setMemberRoleID** *(M, Integer)* : Reference [roledefaultpriv] table.
	* **memberPrivilege** *(M, Array JSON)* : 此Channel所有Members(包括自己)的Role & Affiliation權限代號。可利用 getMemberPrivilege() 取得Map Array Object，其中Map Object中的key即其為以下變數。
		* **affID** *(M, Integer)* : The other user's affiliation ID.
		* **affTime** *(M, Integer)* : The other user's affiliation time.
		* **brandID** *(M, String)* : The other user's brand ID
		* **isChProfileImage** *(M, Boolean)* : The above profileID is specified for the channel.
		* **joinTime** *(M, Integer)* : join time what the user into channel
		* **lastReadTime** *(M, Integer)* : The other user's last read time in the channel.
		* **nickname** *(M, String)* : The other user's nick name
		* **phoneNumber** *(M, String)* : The other user's phone number
		* **profileID** *(M, String)* : The other user's profile image ID.
		* **roleID** *(M, Integer)* : The other user's role id in the channel.
		* **userID** *(M, String)* : The other user ID in the channel.
	* **memberCount** *(M, Long)* : The total joined number of members per chatroom.
	* **isLiveMode** *(M, Boolean)* : The value is true, if thie channel is live mode.

#### Response Example

```
{
	"returnCode": 0,
	"batchNo": 1,
	"batchTotal 1,
  	"channels": [{
		"availCategory": 1,
		"chID": "avzrg6mgracakvux:avzrg6mkjqchp5o4",
		"chProperties": 3360,
		"chType": "1",
		"createTime": 1503404261057,
		"createUserID": "avzrg6mkjqchp5o4",
		"fileSizeLimit": 102400,
		"inviteTime": 1503404261057,
		"inviteUserID": "avzrg6mkjqchp5o4",
		"isMute": false,
		"joinTime": 1503404261057,
		"lastMsgContent": "1,8,0",
		"lastMsgID": "7a68eb68-88a7-11e7-ae46-1a2d3100478b",
		"lastMsgSenderID": "avzrg6mgracakvux",
		"lastMsgSenderNickname": "",
		"lastMsgSenderProfileID": "",
		"lastMsgTime": 1503563855270,
		"lastMsgType": 2,
		"lastReadTime": 1503563548583,
		"maxMembers": 2,
		"memberPrivilege": [{
			"affID": 4,
			"affTime": 1503404261057,
			"brandID": "ltsdk",
			"isChProfileImage": false,
			"joinTime": 1503404261057,
			"lastReadTime": 1503563855270,
			"nickname": "",
			"phoneNumber": "0999000314",
			"profileID": "",
			"roleID": 4,
			"userID": "avzrg6mgracakvux"
		}, ...],
		"memo": "SDK set Memo",
		"minMembers": 0,
		"privilege": {
			"affID": 8,
			"affName": "Admin",
			"affPriv": 122814,
			"banMembers": 6,
			"canSendMsgType": 1023,
			"getMemberList": 14,
			"inviteRoleID": 4,
			"kickRoleID": 6,
			"recvMsg": 12,
			"recvPres": 12,
			"roleID": 8,
			"roleName": "Moderator",
			"rolePriv": 1023,
			"setMemberAffID": 12,
			"setMemberRoleID": 12
		},
		"memberCount:" 1,
		"isLiveMode": false,
		"profileBackImageID": "",
		"profileImageID": "",
		"ranking": 0,
		"ringToneID": null,
		"subject": "",
		"unreadCount": 8,
		"updateTime": 1503404261057,
		"updateUserID": "avzrg6mkjqchp5o4",
		"userAttr": 1,
        "userSetting": 0}, ...]
}
```

[Top](#index)
#### <a name="im_getUserUnreadChannelList">未讀聊天室列表</a>
取得使用者未讀訊息的聊天室列表。
此為iq message，請參考[IQMessage](#im_iqmessage).<br/>

#### Request parameters description
* **callback** *(O, Object)* : Response function.

```
getUserUnreadChannels(function(obj) {
    ...
});
```
#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful.
* **totalUnread** *(M, Integer)* : The total count of the user’s unread messages.
* **result** *(M, Array)* :
	* **chID** *(M, String)* : The channel ID.
	* **count** *(M, Integer)* : The unread count for the above chID specified channel.
	* **chType** *(M, String)* : The channel ID.
		
#### Response Example

```
{
    "returnCode": 0,
    "totalUnread": 3,
    "result": [
        {
            "chID": "442a7cac-b2d6-11e7-bc88-8c950000004f",
            "count": 1,
            "chType": 4
        },
        {
            "chID": "a850f71e-b548-11e7-bc88-8c960000004f",
            "count": 2,
            "chType": 5
        }
    ]
}
```

[Top](#index)
#### <a name="im_queryCPChannelList">活動群組列表</a>
取得活動群組列表，若要設定活動群組，請洽LoFTech人員。
此為iq message，請參考[IQMessage](#im_iqmessage).<br/>

#### Request parameters description
* **lastUpdateTime** *(M, Integer)* : Just list channels those update time is grater than the lastUpdateTime value.
* **callback** *(O, Object)* : Response function.

```
queryCorporationChannelList(lastUpdateTime, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 	
* **channels** *(M, Array)* :
	* **availCategory** *(M, Integer)* : The following bits indiate the channel supported category of
messages. Bit 0: chat room, Bit 1: voting.
	* **chID** *(M, String)* : The channel ID.
	* **chProperties** *(M, Integer)* : The channel's other properties. Reference [channels] table.
	* **chType** *(M, String)* : The channel type. Currently, only types '1','2','3','4','5' are supported. It can be extended as bussiness needs.
	* **createTime** *(M, Integer)* : The channel created timestamp in milliseconds.
	* **createUserID** *(M, String)* : The User ID who create the channel.
	* **fileSizeLimit** *(M, Integer)* : The file size limit in KBytes of each file which can be sent in the channel.
	* **inviteTime** *(M, Integer)* : 使用者被邀請加入此Channel的Timestamp時間(milliseconds)。
	* **inviteUserID** *(M, String)* : 要請使用者加入此Channel的使用者UserID。
	* **isMute** *(M, Integer)* : The value is true, if the receiver set the channel muted.
	* **joinTime** *(M, Integer)* : 加入此Channel的Timestamp時間(milliseconds).
	* **lastMsgContent** *(M, String)* : The receiver's last message in the channel.
	* **lastMsgID** *(M, String)* : The receiver's last message's ID in the channel.
	* **lastMsgSenderID** *(M, String)* : The receiver's last message's userID in the channel.
	* **lastMsgSenderNickname** *(M, String)* : The receiver's last message's nick name in the channel.
	* **lastMsgSenderProfileID** *(M, String)* : The receiver's last profile info in the channel.
	* **lastMsgTime** *(M, Integer)* : The receiver's last message's send time in the channel.
	* **lastMsgType** *(M, Integer)* : The receiver's last message type in the channel.
	* **lastReadTime** *(M, Integer)* : The receiver's last read time in the channel.
	* **maxMembers** *(M, Integer)* :	The maximum number of members in the channel.
	* **minMembers** *(M, Integer)* :	The minimum number of members in the channel.
	* **profileBackImageID** *(M, String)* : The channel's profile back image uuid.
	* **profileImageID** *(M, String)* :	 The channel's profile image uuid.	* **ranking** *(M, Integer)* : 分類的排列順序,預設值為0，Client端將以升冪方式排序顯示。
	* **ringToneID** *(M, String)* : The ring tone ID which the receiver set in the channel. The value is null or empty string, if the receiver did not set.
	* **subject** *(M, String)* : The subject of the channel.
	* **unreadCount** *(M, Integer)* : unread message count in channel.
	* **updateTime** *(M, Integer)* : The channel updated timestamp in milliseconds.
	* **updateUserID** *(M, String)* : The User ID who update the channel's properties (subject or profile image).
	* **memberPrivilege** *(M,Array JSON)* : 此Channel所有Members(包括自己)的Role & Affiliation權限代號。 可利用 getMemberPrivilege() 取得Map Array Object，其中Map Object中的key即其為以下變數。
		* **affID** *(M, Integer)* : The other user's affiliation ID.
		* **affTime** *(M, Integer)* : The other user's affiliation time.
		* **brandID** *(M, String)* : The other user's brand ID
		* **isChProfileImage** *(M, Boolean)* : The above profileID is specified for the channel.
		* **joinTime** *(M, Integer)* : join time what the user into channel
		* **lastReadTime** *(M, Integer)* : The other user's last read time in the channel.
		* **nickname** *(M, String)* : The other user's nick name
		* **phoneNumber** *(M, String)* : The other user's phone number
		* **profileID** *(M, String)* : The other user's profile image ID.
		* **roleID** *(M, Integer)* : The other user's role id in the channel.
		* **userID** *(M, String)* : The other user ID in the channel.
	* **corpID** *(M, String)* : 企業代表帳號。
	* **corpID** *(M, String)* : 企業設定名稱。
	
#### Response Example

```
{
  "returnCode": 0,
  "channels": [
    {
      "chID": "6ce39991-cbcf-8f0c-3fd2-e3333c76a777",
      "chType": "7",
      "subject": "Loftech Fax",
      "profileImageID": "1475740916.2419",
      "profileBackImageID": "",
      "chProperties": 1114,
      "availCategory": 3,
      "createTime": 1475740773051,
      "updateTime": 1475740916322,
      "createUserID": "E0000050077",
      "updateUserID": "E0000050077",
      "minMembers": 0,
      "maxMembers": 200,
      "fileSizeLimit": 102400,
      "unreadCount": 0,
      "lastMsgSenderID": "",
      "lastMsgSenderNickname": "",
      "lastMsgSenderProfileID": "",
      "lastMsgContent": "",
      "lastMsgType": "",
      "lastMsgTime": 0,
      "lastReadTime": 0,
      "joinTime": 0,
      "lastMsgID": "",
      "isMute": false,
      "ringToneID": "",
      "inviteTime": 0,
      "inviteUserID": "",
      "ranking": null,
      "privilege": null,
      "memberPrivilege": [
        {
          "userID": "E0000050077",
          "phoneNumber": "E0000050077",
          "brandID": null,
          "nickname": "Loftech Fax",
          "profileID": "1475740916.2419",
          "affTime": 1475740773051,
          "joinTime": 1475740773051,
          "lastReadTime": 1475740916322,
          "affID": 16,
          "roleID": 16,
          "isChProfileImage": true
        },
        {
          "userID": "au7tkXXXXqc5ffkx",
          "phoneNumber": "0921179016",
          "brandID": "ltsdk",
          "nickname": "",
          "profileID": "juikerim_jstune.tw.juiker.net_ace8824d-ede1-4ce1-8622-56fa0a116468",
          "affTime": 1475740773051,
          "joinTime": 1475740773051,
          "lastReadTime": 1478065261138,
          "affID": 4,
          "roleID": 4,
          "isChProfileImage": false
        },...
      ],
      "corpID": "E0000050077",
      "corpName": "IPCAM 內測企業"
    },...
  ]
}
```

[Top](#index)
#### <a name="sync_chatroom_read_time">Sync chatroom read time with Server</a>

After enter chatroom, please call this method to sync read time with the server and other members.

此為 im message請參考[IMMessage](#im_message).<br/>

#### Request parameters description
* **chID** *(M, Integer)* : The channel ID which the sender read.
* **markTS** *(M, Integer)* : The timestamp of the last message which the sender received. All messages
with send-time less than or equal to the markTS will be mark as read by the sender.
* **callback** *(O, Object)* : Response function.

```
markRead(chID, markTS, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 		
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : chatroom
* **msgContent** *(M, String)* : Read message time
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : 1101
* **sendTime** *(M, Integer)* : The message's sent time.
* **senderID** *(M, String)* : The user ID who send the message.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message.
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.

#### Response Example

```
{
	"returnCode": 0,
	"chID": "avzrg6mgracakvux:avzrg6mkjqchp5o4",
	"chType": "1",
	"encrypted": false,
	"isRead": false,
	"msgCategory": "chatroom",
	"msgContent": "1503632881175",
	"msgID": "31120b56-8948-11e7-a018-1a2d410018af",
	"msgType": 1101,
	"sendTime": 1503632881175,
	"senderID": "avzrg6mkjqchp5o4",
	"timeToSend": 1503632881175,
	"transID": "77525080-1ee1-4a52-bec0-8ec51de5ba73"
}
```

[Top](#index)
#### <a name="im_queryChannelReadInfo">獲取聊天室成員已讀時間</a>
進聊天室後可利用queryChannelReadInfo，取得此聊天室的所有成員已讀的時間。
此為iq message，請參考[IQMessage](#im_iqmessage).<br/>

#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **callback** *(O, Object)* : Response function.

```
queryChannelReadInfo(chID, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 		
* **chID** *(M, String)* : The channel ID.
* **readInfo** *(M, Array)* : The array of objects which containts userid and last read time, as follows.
	* **userID** *(M, String)* : The user ID who is the member of the channel and read the channel's message.
	* **lastReadTime** *(M, Long)* : The timestamp in milliseconds which the user ID read the channel's message last time.

#### Response Example

```
{
  "returnCode": 0,
  "chID": "d269761e-7ec2-4958-36e9-8cfafbfaf418",
  "readInfo": [
    {
      "userID": "avzrg6mgracakvux",
      "lastReadTime": 0
    },
    {
      "userID": "avzrg6mkjqchp5o4",
      "lastReadTime": 1505792298648
    }, ...
  ]
}
```

[Top](#index)
#### <a name="im_getChannelReadTime">獲取各聊天室的最後讀取時間</a>
利用getChannelsLastReadTime取得每間聊天室登入者本身的讀取時間。
此為iq message，請參考[IQMessage](#im_iqmessage).<br/>

#### Request parameters description
* **callback** *(O, Object)* : Response function.

```
queryListChannelsReadTime(function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 		 
* **channels** *(M, Array)* :
	* **chID** *(M, String)* : The channel ID.
	* **chType** *(M, String)* : The channel Type.
	* **lastReadTime** *(M, Integer)* : The sender's last read time in the channel.
	
#### Response Example

```
{
  "returnCode": 0,
  "channels": [
    {
      "chID": "avuwzo3ryqctyrsg:avzrg6mgracakvux",
      "chType": "1",
      "lastReadTime": 0
    },
    {
      "chID": "32ebda9c-266e-4cac-8e35-690e4d317909",
      "chType": "4",
      "lastReadTime": 1504685650892
    }
  ]
}
```

[Top](#index)
#### <a name="im_modifyRoleID">修改成員權限</a>
成員權限可參考成員權限的文件，並從[聊天室資訊](#im_getChannelInfo)中取得每個成員的roleID。另外請確保每個聊天室至少一位管理者，所以在設計上請務必小心使用。<br/>
修改成員權限後，請務必呼叫[聊天室資訊](#im_getChannelInfo)，取得更詳細的聊天室資訊。<br/>
此為 im message請參考[IMMessage](#im_message).<br/>

#### Request parameters description
* **chID** *(M, Integer)* : The channel ID.
* **memberUserID** *(M, String)* : The member whose role id will be changed.
* **roleID** *(M, Integer)* : The new role ID for the above specified member. (RoleID Object)
* **affID** *(M, Integer)* : The new affiliation ID for the above specified. (AffID Object)
* **callback** *(O, Object)* : Response function.

```
modifyRoleID(chID, memberUserID, roleID, affID, function(obj) {
    ...
});
```

##### RoleID Object (For member) and AffID Object (For Channel)

	NONE_ROLEID : number 0, None member.
	INVITED_ROLEID : number 2, Visited and not to join member.
	MEMBER_ROLEID : number 4, General member
	ADMIN_ROLEID : number 8, Admin member
	OWNER_ROLEID : number 16, Admin member (Owner)

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **extInfo** *(M, String)* : The role ID which the above specified user id is changed.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : chatroom.
* **msgContent** *(M, String)* : The member's user ID whose role id is changed.
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : 1010
* **sendTime** *(M, Integer)* : The modify roleID message's sent time.
* **senderID** *(M, String)* : The user ID who send the message.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message.
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.
		
#### Response Example

```
{
	"returnCode": 0,
	"chID": "d269761e-7ec2-4958-36e9-8cfafbfaf418",
	"chType": "4",
	"encrypted": false,
	"extInfo": "8",
	"isRead": false,
	"msgCategory": "chatroom",
	"msgContent": "avzrg6mkjqchp5o4",
	"msgID": "ed148e6a-8956-11e7-ae46-1a2d42007ac8",
	"msgType": 1010,
	"sendTime": 1503639209583,
	"senderID": "avzrg6mkjqchp5o4",
	"timeToSend": 1503639209583,
	"transID": "74e06edf-c0e5-41fe-70b8-b2b3d7d7ad3c"
}
```

[Top](#index)
#### <a name="im_modifyProfile">修改聊天室頭像或名稱</a>
此為 im message請參考[IMMessage](#im_message).</br>
1.更改聊天室頭像

#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **profileImageFile** *(M, Object)* : The profile image file, 若需要刪除大頭照請帶null或是undefined.
* **callback** *(O, Object)* : Response function.

```
modifyProfile(chID, profileImageFile, function(obj) {
    ...
});
```

#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **profile** *(M, String)* : The profile, 若需要刪除大頭照請帶null或是空字串.
* **callback** *(O, Object)* : Response function.

```
modifyChannelProfile(chID, profile, function(obj) {
    ...
});
```

2.更改聊天室名稱

#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **subject** *(M, String)* : The subject of the channel, 聊天室名稱不可以帶空字串與null.
* **callback** *(O, Object)* : Response function.

```
modifySubject(chID, subject, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : chatroom
* **msgContent** *(M, String)* : profile info
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : 1015
* **sendTime** *(M, Integer)* : The modify profile message's sent time.
* **senderID** *(M, String)* : The user ID who send the message.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.

#### Response Example

```
{
  "returnCode": 0,
  "chID": "32ebda9c-266e-4cac-8e35-690e4d317909",
  "chType": "4",
  "msgID": "ec742650-9f4a-11e7-ae46-1a2db30235e4",
  "transID": "84eade2d-7eb5-4cd0-8f74-920d39872e83",
  "msgType": 1015,
  "msgContent": "{\"profileImageID\":\"storageAccount_storageDomain_5f4daec4-5e75-4556-bdf2-10b43125774e\"}",
  "senderID": "avzrg6mgracakvux",
  "senderNickname": "test nickname",
  "sendTime": 1506052980152,
  "timeToSend": 1506052980152,
  "isRead": false,
  "msgCategory": "chatroom",
  "encrypted": true
}
```

[Top](#index)
#### <a name="im_modifyNickname">修改登入者頭像或暱稱</a>
此為 im message請參考[IMMessage](#im_message).</br>

1.修改登入者頭像

#### Request parameters description
* **profileImageFile** *(M, Object)* : The profile image file, 若需要刪除大頭照請帶null或是undefined.
* **callback** *(O, Object)* : Response function.

```
setUserProfileImage(profileImageFile, function(obj) {
    ...
});
```

#### Request parameters description
* **profile** *(M, String)* : The profile, 若需要刪除大頭照請帶null或是空字串.
* **callback** *(O, Object)* : Response function.

```
setUserProfile(profile, function(obj) {
    ...
});
```

2.修改登入者暱稱 

#### Request parameters description
* **nickName** *(M, String)* : The nickname of the user, 使用者名稱不可以帶null.
* **callback** *(O, Object)* : Response function.

```
setUserNickname(nickName, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **chID** *(M, String)* : 00000001-0000-0000-0000-000000000000.
* **chType** *(M, String)* : system.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : chatroom
* **msgContent** *(M, String)* : profile info
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : 1012
* **sendTime** *(M, Integer)* : Timestamp in millisecond.
* **senderID** *(M, String)* : Sender's User ID.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.

#### Response Example

```
{
  "returnCode": 0,
  "chID": "00000001-0000-0000-0000-000000000000",
  "chType": "system",
  "msgID": "eaa3ce32-9f4c-11e7-ae46-1a2db302709d",
  "transID": "83903c36-ced5-43d9-8753-29c72f4fa140",
  "msgType": 1012,
  "msgContent": "{\"profileID\":\"storageAccount_storageDomain_df550a66-8afd-426e-a2f5-fd4974487d4d\"}",
  "senderID": "avzrg6mgracakvux",
  "sendTime": 1506053836102,
  "timeToSend": 1506053836102,
  "isRead": false,
  "msgCategory": "system",
  "encrypted": false
}
```

[Top](#index)
#### <a name="im_getUserProfile">取得使用者頭像與暱稱</a>
此為iq message，請參考[IQMessage](#im_iqmessage).</br>

#### Request parameters description
* **userIDs** *(M, Array)* : The userID Array.
* **callback** *(O, Object)* : Response function.

```
getUserProfileWithUserIDs(userIDs, function(obj) {
	...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **result** *(M, Array JSON)* : 
	* **userID** *(M, String)* : The user's ID.
	* **phoneNumber** *(M, String)* : The user's phone number
	* **profileImageID** *(M, String)* : The user's profile image ID
	* **nickname** *(M, String)* : The user's nick name



#### Response Example

```
{
	"returnCode": 0,
	"result": [
		{
			"userID": "avzrg6mgracakvux",
			"phoneNumber": "0999000053",
			"profileImageID": "storageAccount_storageDomain_008150bc-6587-45f1-856f-67906d31cc16",
			"nickname": "SDK Tester"
       }
	]
}
```

[Top](#index)
#### <a name="im_getProfile">取得聊天室頭像</a>
此為iq message，請參考[IQMessage](#im_iqmessage).</br>

#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **callback** *(O, Object)* : Response function.

```
getChannelProfile(chID, function(obj) {
	...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **chID** *(M, String)* : The channel ID.
* **profileImageID** *(M, String)* :  The profile image ID of the channel. 如何利用此ID產生下載連結，請參考[檔案管理](#stroage)與範例。</br>

* **subject** *(M, String)* : The subject of the channel.

#### Response Example

```
{
	"returnCode": 0,
	"chID": "8f2f3931-1bbf-4b6b-a412-83b3c760fa12",
	"profileImageID":"storageAccount_storageDomain_008150bc-6587-45f1-856f-67906d31cc16",
	"subject":"SDK Test Demo"
}
```

[Top](#index)
#### <a name="im_getSubject">取得聊天室名稱</a>
此為iq message，請參考[IQMessage](#im_iqmessage).</br>

#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **callback** *(O, Object)* : Response function.

```
getChannelSubject(chID, function(obj) {
	...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **chID** *(M, String)* : The channel ID.
* **subject** *(M, String)* : The subject of the channel.

#### Response Example

```
{
	"returnCode": 0,
	"chID": "abe0776e-07e4-4231-429b-15c0ed880652",
	"subject": "SDK Test Demo Modified !"
}
```

[Top](#index)
#### <a name="im_getchatroomMembers">Get chatroom members</a>

To get members list in the specific chatroom from the server.

此為iq message，請參考[IQMessage](#im_iqmessage).<br/>

#### Request parameters description
* **chID** *(M, String)*: To query the members of the specified chID channel.
* **lastUserID** *(M, String)*: In order to request the rest of members’s userID, the lastUserID is needed to be set to continue. The value of this field is followed to the last userID of the previous response. (Noted: The first request of this field can be empty string.)
* **count** *(M, Integer)*: The result lists all members' userIDs in alphabetical order and the count is the amount of each query. The value of this field limits from 100 to 1000.
* **callback** *(M, Object)*: Response function.

```
queryChannelMembers(chID, lastUserID, 300, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **count** *(M, Integer)* : The count of query member.
* **members** *(M, Array)* : The array of member objects which containg the following key/values:
	* **userID** *(M, Boolean)* : The user ID in the channel.
	* **phoneNumber** *(M, Boolean)* : The user's phone number in the channel.
	* **nickname** *(M, String)* : The user's nickname in the channel.
	* **profileID** *(M, String)* : The user's profile image ID in the channel.
	* **affID** *(M, String)* : The user's affiliation id in the channel.
	* **roleID** *(M, Integer)* : The user's role id in the channel.
	* **isChNickname** *(M, Long)* : The nickname is specified for the channel.
	* **isChProfileImage** *(M, String)* : The user's profileID in the channel.
	* **affTime** *(M, String)* : The user's affTime in the channel.
	* **brandID** *(M, String)* : The user's brandID in the channel.
	* **joinTime** *(M, String)* : The user's join time in the channel.
	* **lastReadTime** *(M, String)* : The user's last read time in the channel.
#### Response Example

```
{
	"returnCode": 0,
	"count": 10,
	"members": [
        {
		 	"affID": 4,
			"affTime": 1561636009361,
			"brandID": "jk",
			"isChNickname": false,
			"isChProfileImage": false,
			"joinTime": 1561636009361,
			"lastReadTime": 1582965192559,
			"nickname": "",
			"phoneNumber": "",
			"profileID": "",
			"roleID": 4,
			"userID": "au7tkmasxxxxxxkx"
        },
        ...
      ]
}
```

[Top](#index)
#### <a name="im_inviteMember">邀請成員</a>
邀請成員權限請參考權限文件，每間聊天室皆有最大成員人數限制，從[聊天室資訊](#im_getChannelInfo)中的maxMembers可得知。
邀請成員後所得到的回應並非為此成員的詳細資料，請務必呼叫[聊天室資訊](#im_getChannelInfo)，取得更詳細的聊天室成員資訊。<br/>
此為 im message請參考[IMMessage](#im_message).</br>

#### Request parameters description
* **chID** *(M, Integer)* : The Channel ID which sender want to invite members to join.
* **members** *(M, Array)* : The arrary of member user IDs those will be invited to join the channel.
* **callback** *(O, Object)* : Response function.

```
members = ['UserID1', 'UserID2'];
inviteMembers(chID, members, function(obj) {
    ...
}
```

```
userObj = new UserObject('UserID1', RoleID.PARTICIPANT_ROLEID, 'Invite_Memeber');
inviteMembers(chID, [userObj], function(obj) {
    ...
}
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : chatroom
* **msgContent** *(M, String)* : The user ID list seperated by comma(,) who be invited to join the channel.
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : 1004
* **sendTime** *(M, Integer)* : The channel invitation message's sent time.
* **senderID** *(M, String)* : The user ID who send the channel invitation.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.

#### Response Example

```
{
	"returnCode": 0,
	"chID": "d269761e-7ec2-4958-36e9-8cfafbfaf418",
	"chType": "4",
	"encrypted": false,
	"isRead": false,
	"msgCategory": "chatroom",
	"msgContent": avzrg6mgracakvux,
	"msgID": "9da636b6-8966-11e7-ae46-1a2d44006fbe",
	"msgType": 1004,
	"sendTime": 1503639209583,
	"senderID": "avzrg6mkjqchp5o4",
	"timeToSend": 1503639209583,
	"transID": "829396a6-ce72-4ad1-0d45-58161ac1df9a"
}
```

[Top](#index)
#### <a name="im_kickMember">移除成員</a>
移除成員權限請參考權限文件</br>
移除成員後所得到的回應並非為此成員的詳細資料，請務必呼叫[聊天室資訊](#im_getChannelInfo)，取得更詳細的聊天室成員資訊。<br/>
此為 im message請參考[IMMessage](#im_message).</br>

#### Request parameters description
* **chID** *(M, Integer)* : The Channel ID which sender want to kick members in the channel.
* **members** *(M, Array)* : The array of user IDs those will be kicked by the sender.
* **callback** *(O, Object)* : Response function.

```
kickMember(chID, members, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : chatroom
* **msgContent** *(M, String)* : The user ID list seperated by comma, those users will be kicked out of the channel.
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : 1006
* **sendTime** *(M, Integer)* : The kick-member message's sent time.
* **senderID** *(M, String)* : The user ID who send the kick-member request in the channel.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.

#### Response Example

```
{
	"returnCode": 0,
	"chID": "d269761e-7ec2-4958-36e9-8cfafbfaf418",
	"chType": "4",
	"encrypted": false,
	"isRead": false,
	"msgCategory": "chatroom",
	"msgContent": avzrg6mgracakvux,
	"msgID": "1b8104b4-896a-11e7-ae46-1a2d4500222a",
	"msgType": 1006,
	"sendTime": 1503639209583,
	"senderID": "avzrg6mkjqchp5o4",
	"timeToSend": 1503639209583,
	"transID": "131e48ef-9a10-4d7a-8078-18705f1d73b2"
}
```

[Top](#index)
#### <a name="im_leaveChannel">離開聊天室</a>
離開聊天室，一對一聊天室無需呼叫此方法。
若登入者為唯一管理者，離開聊天室必須先指定下一個管理者，可利用[修改成員權限](#im_modifyRoleID)功能。</br>
此為 im message請參考[IMMessage](#im_message).</br>

#### Request parameters description
* **chID** *(M, String)* : The channel ID which the user will leave.
* **callback** *(O, Object)* : Response function.

```
leaveChannel(chID, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : chatroom
* **msgContent** *(M, String)* : The user ID who leave the channel.
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : The channel type.
* **sendTime** *(M, Integer)* : The channel leave message's sent time.
* **senderID** *(M, String)* : The user ID who leave the channel.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message.
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.

#### Response Example

```
{
	"returnCode": 0,
	"chID": "5b69a4c6-e63c-493f-b63c-df6700ff6769",
	"chType": "4",
	"encrypted": false,
	"isRead": false,
	"msgCategory": "chatroom",
	"msgContent": "avzrg6mkjqchp5o4",
	"msgID": "b625e37a-896c-11e7-a018-1a2d45004c23",
	"msgType": 1003,
	"sendTime": 1503639209583,
	"senderID": "avzrg6mkjqchp5o4",
	"timeToSend": 1503639209583,
	"transID": "a281d827-7f2f-4269-b864-ba6e2bbfd6c9"
}
```

[Top](#index)
#### <a name="im_dismissChannel">解散聊天室</a>
解散聊天室，一對一聊天室無需呼叫此方法. 只有管理者可以操作此功能.</br>
此為 im message請參考[IMMessage](#im_message).</br>

#### Request parameters description
* **chID** *(M, String)* : The channel ID which will be dismissed.
* **callback** *(O, Object)* : Response function.

```
dismissChannel(chID, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : chatroom
* **msgContent** *(M, String)* : The subject of the dismissed channel.
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : 1002
* **sendTime** *(M, Integer)* : The modify profile message's sent time.
* **senderID** *(M, String)* : The user ID who send the message.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.

#### Response Example

```
{
	"returnCode": 0,
	"chID": "5b69a4c6-e63c-493f-b63c-df6700ff6769",
	"chType": "4",
	"encrypted": false,
	"isRead": false,
	"msgCategory": "chatroom",
	"msgContent": "channel subject",
	"msgID": "b625e37a-896c-11e7-a018-1a2d45004c23",
	"msgType": 1002,
	"sendTime": 1503639209583,
	"senderID": "avzrg6mkjqchp5o4",
	"timeToSend": 1503639209583,
	"transID": "a281d827-7f2f-4269-b864-ba6e2bbfd6c9"
}
```

[Top](#index)
### <a name="im_message_manager">訊息管理</a>
#### <a name="im_queryMessage">撈取訊息</a>
以下有幾種方法可以取得聊天室訊息。

1.獲取單筆訊息資訊
此為iq message，請參考[IQMessage](#im_iqmessage).<br/>

#### Request parameters description
* **msgID** *(M, String)* : The message ID who will be queried.
* **callback** *(O, Object)* : Response function.

```
queryOneMessage(msgID, function(obj) {
    ...
});
```

2.獲取聊天室訊息或是某筆訊息之後的訊息
此為iq message，請參考[IQMessage](#im_iqmessage).<br/>

#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **msgID** *(M, String)* : The message ID who will be queried.
* **afterN** *(M, Integer)* : If afterN > 0, then the afterN messages with sendTime > markTS will be returned and order by sendTime ascendingly. If afterN < 0, then then afterN messages with sendTime < markTS will be returned and ordered by sendTime descendingly. (Range: -100 ~ 100)
* **msgCategory** *(M, String)* : The message category.
* **groupID** *(O, String)*: Query messages by specified groupID。 If the tag is specified, the chID tag must also be set. The system will just return messages meet the tags of chID, groupID, markTS, and afterN only, regardless of other tags.
* **callback** *(O, Object)* : Response function.


```
queryMessageByMsgID(chID, msgID, afterN, msgCategory, groupID, function(obj) {
    ...
});
```

#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **timestamp** *(M, Integer)* : The timestamp of the last message which the sender received. All
messages with send-time less than or equal to the markTS will be mark as read by the sender.
* **afterN** *(M, Integer)* : If afterN > 0, then the afterN messages with sendTime > markTS will be returned and order by sendTime ascendingly. If afterN < 0, then then afterN messages with sendTime < markTS will be returned and ordered by sendTime descendingly. (Range: -100 ~ 100)
* **msgCategory** *(M, String)* : The message category.
* **groupID** *(O, String)*: Query messages by specified groupID。 If the tag is specified, the chID tag must also be set. The system will just return messages meet the tags of chID, groupID, markTS, and afterN only, regardless of other tags.
* **callback** *(O, Object)* : Response function.


```
queryMessageByTimestamp(chID, timestamp, afterN, msgCategory, groupID, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **chID** *(M, String)* : The channel ID.
* **groupID** *(O, String)*: The specified groupID.
* **count** *(M, Integer)* : The count of query message
* **markTS** *(M, Integer)* : The markTS which is the same as the sender's request.
* **message**	 *(M, Array)* : The array of message objects which containg the following key/values:
	* **chID** *(M, String)* : The channel ID.
	* **groupID** *(O, String)*: The specified groupID.
	* **chType** *(M, String)* : The channel type.
	* **msgType** *(M, String)* : The message type.
	* **extInfo** *(M, String)* : 傳送檔案、圖檔、影像、語音類型格式資訊，例如：縮圖資訊、檔案大小，若非以上格式此欄位不回傳.
	* **filename** *(M, String)* : 傳送檔案、圖檔、影像、語音類型的檔案名稱，若非以上格式此欄位不回傳.
	* **reply** *(O, Object)* : The object of the Mother message
		* **senderNickname** *(O, String)*: The sender ID’s nickname of the Mother message.
		* **msgContent** *(M, String)*: The content of the Mother message.
		* **msgID** *(M, String)*: The message ID of the Mother message.
		* **extInfo** *(O, String)*: The additional information for the Mother message.
		* **msgType** *(M, Integer)*: The message types of the Mother message.
	* **parentMsgID** *(O, String)* : The message ID of the Mother message.  
	* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
	* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
	* **msgCategory** *(M, String)* : chatroom
	* **msgContent** *(M, String)* : The message content.
	* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
	* **msgType** *(M, Integer)* : 1002
	* **readTime** *(M, Integer)* : The message's read time.
	* **sendTime** *(M, Integer)* : The message's sent time.
	* **senderID** *(M, String)* : The user ID who send the message.
	* **timeToSend** *(M, Integer)* :The timestamp in illiseconds to send the message.
	* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.
	* **recallStatus** *(O, String)* : This is Json String Type. It includes the following tags: Mode, Time and the one who asks Recall message.
	
#### Response Example


```
{
	"returnCode": 0,
	"chID": "5b69a4c6-e63c-493f-b63c-df6700ff6769",
	"groupID": "G2",
	"count": 30
	"markTS": 0
	"messages": [
	{
      "chID": "avzrg6mgracakvux:avzrg6mkjqchp5o4",
      "groupID": "G2",
      "chType": "1",
      "msgID": "d101ce80-8df3-11e7-ae46-1a2dad004bd1",
      "transID": "12a512cf-1d3d-4685-32c8-b30be8a846d6",
      "msgType": 1,
      "msgContent": "123",
      "senderID": "avzrg6mkjqchp5o4",
      "sendTime": 1504146398121,
      "timeToSend": 1504146398121,
      "isRead": false,
      "msgCategory": "chatroom",
      "encrypted": true
    },
	{
      "chID": "avzrg6mgracakvux:avzrg6mkjqchp5o4",
      "groupID": "G2",
      "chType": "1",
      "msgID": "0ad4aafc-8e25-11e7-ae46-1a2db30008dc",
      "transID": "921e6668-8dfc-4b96-b641-26c0bc6fb615",
      "msgType": 3,
      "msgContent": "file_name=921e6668-8dfc-4b96-b641-26c0bc6fb615.jpg&remote_path=/Juiker/Channel/File/avzrg6mgracakvux:avzrg6mkjqchp5o4&content_type=image/jpeg&owner_id=sdkuser&owner_domain=jstune-sat.baby.juiker.net",
      "extInfo": "{\"image\":\"file_name=921e6668-8dfc-4b96-b641-26c0bc6fb615.jpg&remote_path=/Juiker/Channel/Thumbnail/avzrg6mgracakvux:avzrg6mkjqchp5o4&content_type=null&owner_id=sdkuser&owner_domain=jstune-sat.baby.juiker.net\"}",
      "senderID": "avzrg6mgracakvux",
      "sendTime": 1504167540473,
      "filename": "pic.jpg",
      "timeToSend": 1504167540473,
      "isRead": true,
      "readTime": 1504167540473,
      "msgCategory": "chatroom",
      "encrypted": true
    },
     ...]
}
```

[Top](#index)
#### <a name="im_sendMessage">傳送訊息</a>
此為 im message請參考[IMMessage](#im_message).</br>
聊天室中傳送各類訊息，傳送訊息依類型有以下幾種方法。

#### 1. Send Text Message
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **msgContent** *(M, String)* : The message content.
* **callback** *(O, Object)* : Response function.

```
sendTextMessage(chID, chType, msgContent, function(obj) {
	...
});
```
#### 2. Send Image Message
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] class.
* **image** *(M, Object)* : The image file.
* **thumbnailImage** *(M, Object)* : The thumbnail iamge file.
* **msgTransID** *(O, String)* : The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendImageMessage(chID, chType, image, thumbnailImage, msgTransID, function(obj) {
	...
});
```

#### 3. Send Video Message
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **video** *(M, Object)* : The video file.
* **videoThumbnail** *(M, Object)* : The thumbnail iamge file.
* **msgTransID** *(O, String)* : The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendVideoMessage(chID, chType, video, videoThumbnail, msgTransID, function(obj) {
	...
});
```

#### 4. Send Voice Message
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **vocieFile** *(M, Object)* : The video file.
* **voiceDuration** *(M, String)* : 聲音檔案的長度，可以自訂時間顯示字串格式。 (內容範例："01:30")
* **msgTransID** *(O, String)* : The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
function sendVoiceMessage(chID, chType, vocieFile, voiceDuration, msgTransID, function(obj) {
	...
});
```

#### 5. Send Contact Message
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **contactName** *(M, String)* : The contact name.
* **contactFile** *(M, Object)* : The contact info file. (內容範例：{"firstName":"Tom","lastName":"Wang","fullName":"Tom Wang","tels":["0912333444","0932555666", ...]})
* **contactThumbnail** *(M, Object)* : The contact thumbnail file.
* **msgTransID** *(O, String)* : The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendContactMessage(chID, chType, contactName, contactFile, contactThumbnail, msgTransID, function(obj) {
	...
});
```

#### 6. Send Location Message
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **longitude** *(M, Float)* : The longitude value.
* **latitude** *(M, Float)* : The latitude value.
* **address** *(M, String)* : The address.
* **locationThumbnail** *(M, Object)* : The location thumbnail file.
* **msgTransID** *(O, String)* : The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendLocationMessage(chID, chType, longitude, latitude, address, locationThumbnail, msgTransID, function(obj) {
	...
});
```

#### 7. Send Document Message
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **docUri** *(M, Object)* : the document file.
* **msgTransID** *(O, String)* : The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendDocumentMessage(chID, chType, document, msgTransID, function(obj) {
	...
});
```

#### 8. Send Customized Message

#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **msgCategory** *(M, String)* : Input customized message category. You can pass a null value if you don't need it.
* **msgContent** *(M, String)* : Input customized message. Developer can design message format, that like json string.
* **notifyMessage** *(M, String)* : Input customized message for display notify center.
* **msgTransID** *(O, String)* : The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendCustomMessage(chID, chType, msgCategory, msgContent, notifyMessage, msgTransID, function(obj) {
	...
});
```

#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **msgCategory** *(M, String)* : Input customized message category. You can pass a null value if you don't need it.
* **msgContent** *(M, String)* : Input customized message. Developer can design message format, that like json string.
* **msgTransID** *(O, String)* : The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendCustomMessageWithoutNotification(chID, chType, msgCategory, msgContent, msgTransID, function(obj) {
	...
});
```

#### `MessageAttributeObject ` object
Property | Type | Default value | Description
--- | --- | --- | ---
saveMsg | Boolean | true | The message would be stored by the server.
sendOnlineUsers | Boolean | true | The message status would be synced to other online members who has the same channel. 
sendInChannelUsers | Boolean | true | The message status would be synced to other online member who are also happened to be in the same channel. (Notice: the [Set chatroom userBehavior](#im_setChannelUserBehavior) must be True.)
sendNotification | Boolean | true | The message would stimulate notification.
addUnreadCount | Boolean | true | The message would show “read/unread”.
updateChLastMsg | Boolean | true | The message would be updated to appear in the chatroom list.

#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **msgCategory** *(M, String)* : Input customized message category. You can pass a null value if you don't need it.
* **msgContent** *(M, String)* : Input customized message. Developer can design message format, that like json string.
* **notifyMessage** *(M, String)* : Input customized message for display notify center.
* **MessageAttributeObject** *(M, MessageAttributeObject)*: Set customized message attribute. You can pass a null value if you will to set default value.
* **msgTransID** *(O, String)* : The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendCustomMessageWithMsgAttributeObj(chID, chType, msgCategory, msgContent, notifyMessage, msgAttributeObj, msgTransID, function(obj) {
	...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : chatroom
* **msgContent** *(M, String)* : The message content.
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : The message content by message format.
* **sendTime** *(M, Integer)* : The message's sent time.
* **senderID** *(M, String)* : The user ID who send the message.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message.
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.

#### Response Example

```
{
	"returnCode": 0,
	"chID": "avzrg6mgracakvux:avzrg6mkjqchp5o4",
	"chType": "1",
	"encrypted": true,
	"isRead": false,
	"msgCategory": "chatroom",
	"msgContent": "SDK test",
	"msgID": "55958f48-8970-11e7-ae46-1a2d46000439",
	"msgType": 1,
	"sendTime": 1503639209583,
	"senderID": "avzrg6mkjqchp5o4",
	"timeToSend": 1503639209583,
	"transID": "a281d827-7f2f-4269-b864-ba6e2bbfd6c9"
}
```

[Top](#index)
#### <a name="im_forwardMessage">訊息轉傳</a>
轉傳的訊息必須為有成功傳出與三個月內的訊息，並可轉傳多筆訊息至多間聊天室。
轉傳訊息的回應值請使用api中的callback方法取得，當server回應成功後，server會發送指定的訊息給發送端指定的聊天室裡的所有成員。

#### Request parameters description
* **msgIDs** *(M, Array)* : A set of messages id which will be sent to specified userID or chID.
* **channels** *(M, Array)* : A set of channel ID which will receive messages.
* **callback** *(O, Object)* : Response function.

```
forwardMessages(msgIDs, channelIDs, function(obj) {    
	...
}
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful.
* **forward_messages** *(M, Array)* : forward messages array
	* **chID** *(M, String)* : The channel ID.
	* **msgID** *(M, String)* : Forward messages ID.
	* **returnCode** *(M, Integer)* : Forward messages status.

#### Response Example

```
{
  "returnCode": 0,
  "forward_messages": [{
  	"chID": "avzrg6mgracakvux:avzrg6mkjqchp5o4",
  	"msgID": "a281d827-7f2f-4269-b864-ba6e2bbfd6c9",
  	"returnCode": 0,
  }, ...]
}
```

[Top](#index)
#### <a name="im_broadcastMessage">訊息廣播</a>

Can transfer multiple messages to more than one chatroom.

The response of broadcast message, please get from the listener in api. After server response successfully, sever will send the specified messages to all members in specified chatrooms.

#### `LTRequestMsg` object
Property | Type | Description
--- | --- | ---
msgContent | String | The user ID.
msgType | Integer | The message type.
extInfo | String | Extra information.
filename | String | The display name.
msgAttribute | [MessageAttributeObject](#im_sendMessage) | The message attribute. This tag only works in message type 3000.

#### Request parameters description
* **messages** *(M, Array)* : The array of (LTRequestMsg) message objects
* **chIDs** *(M, Array)* : The channel ID array.
* **callback** *(O, Object)* : Response function.

```
msg01 = new LTRequestMsg("text001", MessageType.TEXT);
msg02 = new LTRequestMsg("text002", MessageType.CUSTOM_MESSAGE_WITH_MSG_ATTRIBUTE_OBJECT, null, null, msgAtriObj);

broadcastMessage([msg01, msg02], chIDs, function(obj) {
    ...
}
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful.
* **broadcast_messages** *(M, Array)* :
	* **chID** *(M, String)* : The channel ID.
	* **msgID** *(M, String)* : Broadcast messages ID.
	* **returnCode** *(M, Integer)* : Broadcast messages status. 

#### Response Example

```
{
  "returnCode": 0,
  "broadcast_messages": [{
  	"chID": "avzrg6mgracakvux:avzrg6mkjqchp5o4",
  	"msgID": "a281d827-7f2f-4269-b864-ba6e2bbfd6c9",
  	"returnCode": 0,
  }, ...]
}
```

[Top](#index)
#### <a name="im_delMessage">刪除指定訊息</a>
此為 im message請參考[IMMessage](#im_message).</br>
可指定聊天室中想要刪除的訊息，但只有刪除登入者端的訊息，聊天室其他成員的訊息仍然存在。

#### Request parameters description
* **msgIDs** *(M, Array)* : The messages id which will be deleted. The function will only delete the sender's message(s) specified by msgID.
* **callback** *(O, Object)* : Response function.

```
deleteMessage(msgIDs, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful.
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : chatroom
* **msgContent** *(M, String)* : The message ID of the deleted message.
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : 1102
* **sendTime** *(M, Integer)* : The message's sent time.
* **senderID** *(M, String)* : The user ID who send the message.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message.
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.

#### Response Example

```
{
	"returnCode": 0,
	"chID": "5b69a4c6-e63c-493f-b63c-df6700ff6769",
	"chType": "4",
	"encrypted": false,
	"isRead": false,
	"msgCategory": "chatroom",
	"msgContent": "channel subject",
	"msgID": "55958f48-8970-11e7-ae46-1a2d46000439",
	"msgType": 1102,
	"sendTime": 1503639209583,
	"senderID": "avzrg6mkjqchp5o4",
	"timeToSend": 1503639209583,
	"transID": "a281d827-7f2f-4269-b864-ba6e2bbfd6c9"
}
```

[Top](#index)
#### <a name="im_delMessageByChid">刪除聊天室所有訊息</a>
此為 im message請參考[IMMessage](#im_message).</br>
可刪除聊天室中的所有訊息，但只有刪除登入者端的訊息，聊天室其他成員的訊息仍然存在。

#### Request parameters description
* **chID** *(M, String)* : All messages in the specified channel id will be deleted. The function will only
delete the sender's messages in specified by chID.
* **callback** *(O, Object)* : Response function.

```
deleteChannelMessages(chID, function(obj) {
    ...
}
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful.
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : chatroom
* **msgContent** *(M, String)* : The channel ID whose all messages will be deleted.
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : 1103
* **sendTime** *(M, Integer)* : The message's sent time.
* **senderID** *(M, String)* : The user ID who send the message.
* **timeToSend** *(M, Integer)* :The timestamp in illiseconds to send the message.
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.

#### Response Example

```
{
	"returnCode": 0,
	"chID": "5b69a4c6-e63c-493f-b63c-df6700ff6769",
	"chType": "4",
	"encrypted": false,
	"isRead": false,
	"msgCategory": "chatroom",
	"msgContent": "5b69a4c6-e63c-493f-b63c-df6700ff6769",
	"msgID": "55958f48-8970-11e7-ae46-1a2d46000439",
	"msgType": 1103,
	"sendTime": 1503639209583,
	"senderID": "avzrg6mkjqchp5o4",
	"timeToSend": 1503639209583,
	"transID": "a281d827-7f2f-4269-b864-ba6e2bbfd6c9"
}
```

[Top](#index)
#### <a name="im_delAllMessage">刪除登入者所有訊息</a>
此為 im message請參考[IMMessage](#im_message).</br>
可刪除登入者所有聊天室中的所有訊息，但只有刪除登入者端的訊息，登入者所有聊天室的其他成員的訊息仍然存在。

#### Request parameters description
* **callback** *(O, Object)* : Response function.

```
deleteAllMessages(function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful.
* **chID** *(M, String)* : 00000001-0000-0000-0000-000000000000.
* **chType** *(M, String)* : system.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : chatroom
* **msgContent** *(M, String)* : If delete all message, the message content is empty.
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : 1105
* **sendTime** *(M, Integer)* : The message's sent time.
* **senderID** *(M, String)* : The user ID who send the message.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message.
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.

#### Response Example

```
{
	"returnCode": 0,
	"chID": "00000001-0000-0000-0000-000000000000",
	"chType": "system",
	"encrypted": false,
	"isRead": false,
	"msgCategory": "chatroom",
	"msgContent": "",
	"msgID": "55958f48-8970-11e7-ae46-1a2d46000439",
	"msgType": 1105,
	"sendTime": 1503639209583,
	"senderID": "avzrg6mkjqchp5o4",
	"timeToSend": 1503639209583,
	"transID": "a281d827-7f2f-4269-b864-ba6e2bbfd6c9"
}
```

[Top](#index)
#### <a name="im_replyMessage">回覆訊息</a>
此為 im message請參考[IMMessage](#im_message).</br>

The Reply feature allows member to respond to a particular message (called as “Mother message”) in an individual chat or group with different types of messages (called as “Son message”). 

#### `ParentMsgObject` object
Property | Type | Description
--- | --- | ---
senderID | String | The sender ID.
senderNickname | String | The sender ID’s nickname.
msgContent | String | The content of the message.
msgID | String | The IM server generated message ID in UUID(type 1) format.
msgType |Integer| The message types. 
extInfo | String | The additional information for the message.

Two method of create parentMsg object:

```
var replyMsgObj = getReplyMsgObjectWithParentMsg(msgObj);

var replyMsgObj = new ParentMsgObject(senderID, senderNickname, msgContent, msgID, msgType, extInfo);

```

#### Response parameters description
*   **senderID** *(M, String)*: The sender ID of the Mother message. Not necessary to set. 
*   **senderNickname** *(M, String)*: The sender ID’s nickname of the Mother message. Not necessary to set.
*   **msgContent** *(M, String)*: The content of the Mother message.
*   **msgID** *(M, String)*: The message ID of the Mother message.
*   **extInfo** *(M, String)*: The additional information for the Mother message. Not necessary to set.
*   **msgType** *(M, Integer)*: The message types of the Mother message. 

#### 1. Reply with text
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **msgContent** *(M, String)* : The message content.
* **parentMsgObj** *(M, Object)*: The object of the Mother message.
* **msgTransID** *(O, String)*: The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendReplyTextMessage(chID, chType, msgContent, parentMsgObj, msgTransID, function(obj) {
	...
});
```

#### 2. Reply with images
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **image** *(M, Object)* : The image file.
* **thumbnailImage** *(M, Object)* : The thumbnail iamge file.
* **parentMsgObj** *(M, Object)*: The object of the Mother message.
* **msgTransID** *(O, String)*: The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendReplyImageMessage(chID, chType, image, thumbnailImage parentMsgObj, msgTransID, function(obj) {
	...
});
```

#### 3. Reply with videos
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **video** *(M, Object)* : The video file.
* **videoThumbnail** *(M, Object)* : The thumbnail iamge file.
* **parentMsgObj** *(M, Object)*: The object of the Mother message.
* **msgTransID** *(O, String)*: The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendReplyVideoMessage(chID, chType, video, videoThumbnail, parentMsgObj, msgTransID, function(obj) {
	...
});
```

#### 4. Reply with voice
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **vocieFile** *(M, Object)* : The video file.
* **voiceDuration** *(M, String)* : 聲音檔案的長度，可以自訂時間顯示字串格式。 (內容範例："01:30")
* **parentMsgObj** *(M, Object)*: The object of the Mother message.
* **msgTransID** *(O, String)*: The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendReplyVoiceMessage(chID, chType, voiceFile, voiceDuration, parentMsgObj, msgTransID, function(obj) {
	...
});
```

#### 5. Reply with files
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **docUri** *(M, Object)* : the document file.
* **parentMsgObj** *(M, Object)*: The object of the Mother message.
* **msgTransID** *(O, String)*: The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendReplyDocumentMessage(chID, chType, document, parentMsgObj, msgTransID, function(obj) {
	...
});
```

#### 6. Reply with customized type
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **msgCategory** *(M, String)* : Input customized message category. You can pass a null value if you don't need it.
* **msgContent** *(M, String)* : Input customized message. Developer can design message format, that like json string.
* **notifyMessage** *(M, String)* : Input customized message for display notify center.
* **parentMsgObj** *(M, Object)*: The object of the Mother message.
* **msgTransID** *(O, String)*: The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendReplyCustomMessage(chID, chType, msgCategory, msgContent, notifyMessage, parentMsgObj, msgTransID, function(obj) {
	...
});
```

#### 7. Reply with customized type (No notification)
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **msgCategory** *(M, String)* : Input customized message category. You can pass a null value if you don't need it.
* **msgContent** *(M, String)* : Input customized message. Developer can design message format, that like json string.
* **parentMsgObj** *(M, Object)*: The object of the Mother message.
* **msgTransID** *(O, String)*: The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendReplyCustomMessageWithoutNotification(chID, chType, msgCategory, msgContent, parentMsgObj, msgTransID, function(obj) {
	...
});
```

#### 8. Reply with customized type (With MessageAttributeObject)
#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, Object)* : Reference [ChannelType] object.
* **msgCategory** *(M, String)* : Input customized message category. You can pass a null value if you don't need it.
* **msgContent** *(M, String)* : Input customized message. Developer can design message format, that like json string.
* **notifyMessage** *(M, String)* : Input customized message for display notify center.
* **parentMsgObj** *(M, Object)*: The object of the Mother message.
* **MessageAttributeObject** *(M, MessageAttributeObject)*: Set customized message attribute. You can pass a null value if you will to set default value.
* **msgTransID** *(O, String)*: The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
sendReplyCustomMessage(chID, chType, msgCategory, msgContent, notifyMessage, parentMsgObj, msgAttributeObj, msgTransID, function(obj) {
	...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful. 
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : chatroom
* **msgContent** *(M, String)* : The message content.
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : The message content by message format.
* **sendTime** *(M, Integer)* : The message's sent time.
* **senderID** *(M, String)* : The user ID who send the message.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message.
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.
* **filename** *(M, String)*: The original filename while msgtype = 3,4,5,6,7.
* **extInfo** *(M, String)* : the message data in json format string
* **parentMsgID** *(O, String)* : the message ID of the Mother message.
* **reply** *(O, Object)* : the object of the Mother message
	*   **senderID** *(M, String)*: The sender ID of the Mother message. Not necessary to set. 
	*   **senderNickname** *(M, String)*: The sender ID’s nickname of the Mother message. Not necessary to set.
	*   **msgContent** *(M, String)*: The content of the Mother message.
	*   **msgID** *(M, String)*: The message ID of the Mother message.
	*   **extInfo** *(M, String)*: The additional information for the Mother message. Not necessary to set.
	*   **msgType** *(M, Integer)*: The message types of the Mother message. #### Response Example

```
{
	"returnCode": 0,
	"chID": "avzrg6mgracakvux:avzrg6mkjqchp5o4",
	"chType": "1",
	"encrypted": true,
	"isRead": false,
	"msgCategory": "chatroom",
	"msgContent": "SDK test",
	"msgID": "55958f48-8970-11e7-ae46-1a2d46000439",
	"msgType": 1,
	"sendTime": 1503639209583,
	"senderID": "avzrg6mkjqchp5o4",
	"timeToSend": 1503639209583,
	"transID": "a281d827-7f2f-4269-b864-ba6e2bbfd6c9",
	"extInfo": "{}",
	"filename": "Demo.png"
	"parentMsgID": "051a2260-83a9-11e9-9c63-bf78180024a8",
	"reply": {
		"msgContent": "reply test",
		"msgID": "051a2260-83a9-11e9-9c63-bf78180024a8",
		"msgType": 1,
		"senderID": "avusvexxxxxxxvs6",
		"senderNickname": "Reply tester",
		"extInfo": "{}"
}
```

[Top](#index)
#### <a name="im_recallMessage">回收訊息</a>

Recall messages
Any member can recall messages. Once the message has been recalled, all the member can’t read this recalled message EXCEPT the admin (The Bit 10 of RolePriv is 1). The admin (The Bit 10 of RolePriv is 1) can read the message content but this message would be remarked as recalled. Therefore, below description is only applied on other members except the admin (The Bit 10 of RolePriv is 1). The admin (The Bit 11 of RolePriv is 1) can recall the messages via API anytime. 

此為 im message請參考[IMMessage](#im_message).</br>

Two types of recalling messages:

1. Without system log: When users recall one message, the server would delete the message content and not send back any system log.  

2. With system log: : When users recall one message, the server would delete the message content and do send back system log. For example, the user recall one message at 14:45.   

#### Request parameters description
* **msgIDs** *(M, Array)* : Provide one or more messages ID which are going to be recalled. (Limitation: less than 10 msgIDs at once.)
* **silentMode** *(M, Boolean)*: provide what type of recalling message is going to be applied. Without system log, please provide “True”. With system log, please provide “False”.
* **callback** *(O, Object)* : Response function.

```
recallMessages(msgIDs, silentMode, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful.
* **recall_messages** *(M, Array)* : The array of recalled messages with the following tags:
	* **returnCode** *(M, Integer)* : 0 : Request is successful.
	* **msgID** *(M, String)* : The message ID.

#### Response Example

```
{
	"returnCode":0,
	"recall_messages": [
		{
			"returnCode": 0,
			"msgID": "99f6fd74-45a9-11e9-ae46-1a5a3600229c"
		},
		{
			"returnCode": 0,
			"msgID": "9a16827a-45a9-11e9-ae46-1a5a360022cc"
		}
	]
}
```

#### Response parameters for all users who received each recalled message
* **returnCode** *(M, Integer)*: When server response “0”, it means that the action of recalling has been proceeded successfully.
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **isRead** *(M, Boolean)* : To check if the message has been read by
    oneself or not.
* **msgCategory** *(M, String)* : The server will response "chatroom"
    at this scenario.
* **msgContent** *(M, String)* :The message content would be represented by the message ID which has been deleted.
* **msgID** *(M, String)* : The IM server generated message ID in
    UUID(type 1) format.
* **msgType** *(M, Integer)* : The code is **1104**. It means recalling message.
* **extInfo** *(M, String)* : Extra information and it is Json String. It includes the following tags: Mode, Time and the one who asks Recall message.
* **sendTime** *(M, Integer)* : The time when the message sent out.
* **senderID** *(M, String)* : The one (userID) who sent the message.
* **timeToSend** *(M, Integer)* : Same as sendTime.
* **transID** *(M, String)* : The requesting ID.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. 

#### Response Example

```
{
	"returnCode": 0,
	"chID": "f3279a55-2db6-42e0-8fc6-8de8dcca95ef",
	"chType": "5",
	"msgID": "9a578a54-45a9-11e9-ae46-1a5a36002334",
	"transID": "e0f4ca10-3de6-4625-9907-e03e752f6546-1",
	"msgType": 1104,
	"msgContent": "99f6fd74-45a9-11e9-ae46-1a5a3600229c",
	"extInfo": "{\"silentMode\":true,\"recall_time\":1552649382539,\"recall_by\":\"UserID1\"}",
	"senderID": "UserID1",
	"sendTime": 1552649382539,
	"timeToSend": 1552649382539,
	"isRead": false,
	"msgCategory": "chatroom",
	"encrypted": false,
}
```

[Top](#index)
#### <a name="im_scheduled_messages">預約訊息</a>
#### 1. How to schedule a message

Only can schedule a message more than 1 min than the create time. Also, only can schedule a message for next 30 days.

After the scheduled message was sent successfully, the system receive a message code (msgType = 206) accordingly. 


#### `ScheduledMsgObject` object
Property | Type | Description
--- | --- | ---
messages | Array | The array of message which needed to be schedule.
chIDs | Array | The channel ID array.
msgID | String | The IM server generated message ID in UUID(type 1) format.
timeToSend |Integer| The time when the message will send out in millisecond.
sendTime|long|The time when the message is created. Access enabled only when query scheduled messages.


#### `LTRequestMsg` object
Property | Type | Description
--- | --- | ---
msgContent | String | The user ID.
msgType | Integer | The message type.
extInfo | String | Extra information.
filename | String | The display name.
msgAttribute | [MessageAttributeObject](#im_sendMessage) | The message attribute. This tag only works in message type 3000.


#### Request parameters description
* **messages** *(M, Array)* : The array of (LTRequestMsg) message objects
* **chIDs** *(M, Array)* : The channel ID array.
* **scheduledTime** *(M, Integer)* : The time when the message will send out in millisecond.
* **msgTransID** *(O, String)*: The requesting ID in UUID(type 1) format.
* **callback** *(O, Object)* : Response function.

```
msg01 = new LTRequestMsg("text001", MessageType.TEXT);
msg02 = new LTRequestMsg("text002", MessageType.CUSTOM_MESSAGE_WITH_MSG_ATTRIBUTE_OBJECT, null, null, msgAtriObj);

sendScheduleMessage([msg01, msg02], chIDs, scheduledTime, null, function(obj) {
    ...
});
```

#### Request parameters description
* **scheduledObj** *(M, Object)* : The object of ScheduledMsgObject.
* **callback** *(O, Object)* : Response function.

```
msg01 = new LTRequestMsg("text_A", MessageType.TEXT);
msg02 = new LTRequestMsg("text_B", MessageType.CUSTOM_MESSAGE_WITH_MSG_ATTRIBUTE_OBJECT, null, null, msgAtriObj);

scheduledObj = new ScheduledMsgObject([msg01, msg02], chIDs, scheduledTime, msgID);

sendScheduleMessageByScheduledObj(scheduledObj, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : When server response “0”, it means that the action of recalling has been proceeded successfully.
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **isRead** *(M, Boolean)* : To check if the message has been read by
    oneself or not.
* **msgCategory** *(M, String)* : The server will response "chatroom"
    at this scenario.
* **msgContent** *(M, String)* :The message content would be represented by the message ID which has been deleted.
* **msgID** *(M, String)* : The IM server generated message ID in
    UUID(type 1) format.
* **msgType** *(M, Integer)* : The code is **205**. It means recalling message.
* **extInfo** *(M, String)* : Extra information and it is Json String. It includes the following tags: Mode, Time and the one who asks Recall message.
* **sendTime** *(M, Integer)* : The time when the message sent out.
* **senderID** *(M, String)* : The one (userID) who sent the message.
* **timeToSend** *(M, Integer)* : Same as sendTime.
* **transID** *(M, String)* : The requesting ID.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. 

#### Response Example

```
{
	"returnCode": 0,
	"chID": "grouptimetosend:UserID1",
	"chType": "timetosend",
	"msgID": "9a578a54-45a9-11e9-ae46-1a5a36002334",
	"transID": "e0f4ca10-3de6-4625-9907-e03e752f6546-1",
	"msgType": 205,
	"msgContent": "{"chIDs":["aae13784-102a-11ea-9c63-4931cb081059"],"messages":[{"msgContent":"text_A","msgType":1},{"msgContent":"text_B","msgType":1}]}",
	"senderID": "UserID1",
	"sendTime": 1552649382539,
	"timeToSend": 1552649382539,
	"isRead": false,
	"msgCategory": "group_time_to_send",
	"encrypted": true,
}
```

#### 2. Query scheduled messages

#### Request parameters description

* **timestamp** *(M, Integer)* : The time to update the latest channel information. Which mean the server would provide all channel information from 1970.01.01.00:00:00 to present time. If the value is 0, it means 1970.01.01.00:00:00. If the value is 1,000, it means 1970.01.01.00:00:01.
* **afterN** *(M, Integer)* : If set 2017.10.27.00:00 and set afterN is more than 0, it means it will get the messages after 2017.10.27.00:00. If the afterN is less than 0, it get the messages before 2017.10.27.(Range: -100 ~ 100)
* **callback** *(O, Object)* : Response function.

```
queryScheduleMessageByMarkTS(timestamp, afterN, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : When server response “0”, it means that the action of recalling has been proceeded successfully.
* **chID** *(M, String)* : The channel ID.
* **count** *(M, Integer)* : The count of messages.
* **markTS** *(M, Integer)* : The markTS which is the same as the sender's request.
* **messages** *(M, Array)* : The array of (ScheduledMsgObject) message objects:
	* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format. 
	* **timeToSend** *(M, Integer)*: The time when the message will send out in millisecond.
	* **sendTime** *(M, long)*: The time when the message is created.
	* **groupID** *(O, String)*: The specified groupID.
	* **chID** *(O, String)*: The channel ID.
	* **chIDs** *(O, Array)*: The channel ID array.
	* **messages** *(M, Array)*: The array of (LTRequestMsg) message which needed to be schedule.
		* **msgContent** *(M, String)*: The message content.
		* **msgType** *(M, Integer)*: The message type.
		* **filename** *(M, String)*: The display name.
		* **extInfo** *(M, String)*: Extra information.
		* **msgAttribute** *(M, MessageAttributeObject)*: The message attribute.
			
#### Response Example

```
{
	"returnCode": 0,
	"chID": "grouptimetosend:UserID1",
	"count": "1",
	"markTS": 1552649382539,
	"messages": [
		{
			"markTS": 1574760211672,
       		"msgID":"46cebc4a-8dc5-11e9-9160-6a1f0b007ba9",
			"chIDs":["2398ee5f-59e0-4cb1-a38e-26631f3f5fa1"],
			"messages": [
				{"msgContent": "text_A", "msgType": 1},
				{"msgContent": "text_B", "msgType": 1}
			]
		}
	]
}
```
#### 3. Cancel scheduled message</a>

Use [Delete selected message](#im_delMessage) to cancel sheduled messages.

[Top](#index)
#### <a name="get_messages_read_state">Get message read/unread</a>
#### 1. Query message read count

#### Request parameters description
* **msgIDs** *(M, Array)* : To query the message ID list, no more than 10 messages.
* **callback** *(O, Object)* : Response function.

```
queryMessageReadCount([msg01, msg02], function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)*: Return status, 0 represents succeeded.  
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.
* **result** *(M, Array)* : 
	* **msgID** *(M, String)* : To query the message ID.
	* **readCount** *(M, Integer)* : The read count of the message ID.
	* **unreadCount** *(M, Integer)* : The unread count of the message ID.

#### Response Example

```
{
	"returnCode": 0,
    "transID": "18956a24-59d2-11eb-a654-1a5a020046b1",
    "result": [
        {
            "msgID": "2b5625f0-5977-11eb-a392-1a5a0000192d",
            "readCount": 3,
            "unreadCount": 0
        },
        {
            "msgID": "8e07aa90-59cf-11eb-ab03-1a5a020038f6",
            "readCount": 0,
            "unreadCount": 3
        }
    ]
}
```

#### 2. Query message read member

#### Request parameters description
* **msgID** *(M, String)* : To query the message ID.
* **lastReadTime** *(M, Long)* : To query the starting point for the user to read the time, the default value is current time. The query results are sorted in descending order, so you can use the last readTime of the last batch of query results to continue to check the next batch of data.
* **count** *(M, Integer)* : Set count of member to be queried, the maximum value is 30, the default is 10.
* **callback** *(O, Object)* : Response function.

```
queryMessageReadUsers(msgID, lastReadTime, count, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : Return status, 0 represents succeeded.  
* **msgID** *(M, String)* : To query the message ID.
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.
* **users** *(M, Array)* : 
	* **userID** *(M, String)* : The user ID of read the message.
	* **readTime** *(M, Long)* : The timestamp when the user read this message.

#### Response Example

```
{
    "returnCode": 0,
    "msgID": "2b5625f0-5977-11eb-a392-1a5a0000192d",
    "transID": "8d592a42-59d4-11eb-a4ef-1a5a02004e21",
    "users": [
        {
            "userID": "userID1",
            "readTime": 1610965450330
        },
        {
            "userID": "userID2",
            "readTime": 1610965429878
        }
    ]
}
```

#### 3. Query message unread member

#### Request parameters description
* **msgID** *(M, String)* : To query the message ID.
* **lastUserID** *(M, String)* : Please take an empty string for the first, and then when you want to continue to check the next batch list, take the last userID of the batch, and the query results will be sorted in ascending order of userID.
* **count** *(M, Integer)* : Set count of member to be queried, the maximum value is 30, the default is 10.
* **callback** *(O, Object)* : Response function.

```
queryMessageUnreadUsers(msgID, lastUserID, count, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)* : Return status, 0 represents succeeded.  
* **msgID** *(M, String)* : To query the message ID.
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.
* **users** *(M, Array)* : The user ID list of unread the message.

#### Response Example

```
{
    "returnCode": 0,
    "msgID": "e847487e-a7ee-11e9-9c63-4f8b10004e5a",
    "users": [
        "userID1",
        "userID2"
    ]
}
```

[Top](#index)
#### <a name="send_channel_group_messages">Send channel group messages</a>

Users are required to be in both the channel and the group list in order to receive messages. The sender must have authorization of querying the group list to send messages.

#### `LTRequestMsg` object
Property | Type | Description
--- | --- | ---
msgContent | String | The user ID.
msgType | Integer | The message type.
extInfo | String | Extra information.
filename | String | The display name.
msgAttribute | [MessageAttributeObject](#im_sendMessage) | The message attribute. This tag only works in message type 3000.

1.Send channel group messages

#### Request parameters description
* **chID** *(M, String)*: The channel ID.
* **groupID** *(M, String)*: The specified groupID.
* **messages** *(M, Array)* : The array of (LTRequestMsg) message objects
* **callback** *(O, Object)* : Response function.

```
sendChannelGroupMessages(chID, groupID, messages, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)*: Return status, 0 represents succeeded.  
* **chID** *(M, String)*: The channel ID.
* **groupID** *(M, String)*: The specified groupID.
* **messages** *(M, Array)* : 
	* **transID** *(M, String)* : The client's message ID in UUID(type 1).
	* **msgID** *(M, String)* : The messages ID.
	* **returnCode** *(M, Integer)* :  The messages status.

#### Response Example

```
{
	"returnCode": 0,
    "chID": "18956a24-59d2-11eb-a654-1a5a020046b1",
    "groupID": "G2",
    "messages": [
        {
            "msgID": "2b5625f0-5977-11eb-a392-1a5a0000192d",
            "transID": "59f22526-887f-11eb-81ca-4931a1020f7f",
            "returnCode": 0
        },
        {
            "msgID": "8e07aa90-59cf-11eb-ab03-1a5a020038f6",
            "transID": "3dc99396-8868-11eb-8fa1-49319b02029e",
            "returnCode": 0
        }
    ]
}
```

2.Send channel group scheduled messages

#### Request parameters description
* **chID** *(M, String)*: The channel ID.
* **groupID** *(M, String)*: The specified groupID.
* **timeToSend** *(M, long)*: The time when the message will send out in millisecond.
* **messages** *(M, Array)* : The array of (LTRequestMsg) message objects
* **callback** *(O, Object)* : Response function.

```
sendChannelGroupScheduledMessages(chID, groupID, timeToSend, messages, function(obj) {
    ...
});
```

#### Response parameters description
* **returnCode** *(M, Integer)*: Return status, 0 represents succeeded.  
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **groupID** *(M, String)*: The specified groupID.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : The server will response "chatroom" at this scenario.
* **msgContent** *(M, String)* : The message content.
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : 205.
* **sendTime** *(M, Long)* : The message's sent time.
* **senderID** *(M, String)* : The user ID who send the message.
* **timeToSend** *(M, Long)* : The timestamp in illiseconds to send the message.
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.
* **parentMsgID** *(O, String)* : the message ID of the Mother message.

#### Response Example

```
{
	"returnCode": 0,
	"chID": "grouptimetosend:UserID1"
	"chType": "timetosend"
	"encrypted": true
	"groupID": "G2"
	"isRead": false
	"msgCategory": "group_time_to_send"
	"msgContent": "{"messages":[{"msgType":1,"msgContent":"text_A"}],"groupID":"G2","chID":"3e327f7f-80b9-45dd-b952-6eb383652fc2"}"
	"msgID": "f202d174-8bab-11eb-a026-49314a031055"
	"msgType": 205
	"returnCode": 0
	"sendTime": 1616485618123
	"senderID": "UserID1"
	"timeToSend": 1616485797893
	"transID": "297d58d6-5761-4af3-977d-2aebe8750143"
}
```


[Top](#index)
### <a name="im_vote">投票管理</a>
#### <a name="im_createVote">建立投票</a>
建立投票的回應值請使用api中的callback方法取得，當server回應成功後，server會發送投票訊息給聊天室裡的所有成員，請參考[IMMessage](#im_message).</br>

#### Request parameters description
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel Type.
* **subject** *(M, String)* : The vote subject.
* **timeToStartVote** *(M, Integer)* : The timestamp in milliseconds to start the voting. 投票開始時間若設定為當下時間為立即投票，若設定為當下時間為未來時間，則為預約投票.
* **timeToCloseVote** *(M, Integer)* : The timestamp in milliseconds to close the voting. 投票結束時間不可小於投票開始時間.
* **options** *(M, Array)* : The array of options of the voting.
	* **msgContent** *(M, String)* : The subject of each option.
	* **msgType** *(M, String)* : The msgtype of the above subject for the created voting. The message type of the voting subject. 原本聊天室訊息的msgType 1 ~ 8，再加100。如:101表示文字內容, 102表示貼圖....。
	* **isOpen** *(M, Integer)* : If the option is an open question for the voter to reply, or just only for selection.
* **callback** *(O, Object)* : Response function.

```
createVote(chID, chType, subject, timeToStartVote, timeToCloseVote, options, function(obj) {
    ...
}

```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful.
* **chID**	 *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **msgCategory**	*(M, String)* : vote_question
* **msgContent**	*(M, String)* : The subject for the created voting.
* **msgID**	*(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType**	*(M, Integer)* : 101
* **msgValidTimestamp**	*(M, Integer)* : Valid time of vote
* **options**	*(M, Array)* : The json array of options of the voting.
	* **isOpen** *(M, Integer)* : If the option is an open question for the voter to reply, or just only for selection.
	* **msgCategory** *(M, String)* : vote_option
	* **msgContent** *(M, String)* : The subject of each option.
	* **msgID** *(M, String)* : The option ID
	* **msgType** *(M, Integer)* : The option type
* **sendTime** *(M, Integer)* : The message's sent time.
* **senderID** *(M, String)* : The user ID who send the message.
* **showVoter** *(M, Integer)* : To show the voter's information or not.
* **showVotingResult** *(M, String)* : To show the voting result while the voting is closed. The default value is true.
* **timeToCloseVote** *(M, Integer)* : The timestamp in milliseconds to close the voting.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message.
* **timeToStartVote** *(M, Integer)* : The timestamp in milliseconds to start the voting.
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.
* **updateVotingResult**	*(M, Integer)* : To update the voting result while the voting is still open. The default value is true.
* **votingTimes** *(M, Integer)* : The voting times for each voter.

#### Response Example

```
{
	"returnCode": 0,
	"chID": "d9ed1431-ddf6-4dfd-674f-2e6404a5999b",
	"chType": "4",
	"msgCategory": "vote_question",
	"msgContent": "SDK vote test",
	"msgID": "431dfe9a-897a-11e7-ae46-1a2d46004c10",
	"msgType:" 101,
	"msgValidTimestamp": 1503740785898,
	"options": [{
		"isOpen": false,
		"msgCategory": "vote_option",
		"msgContent": "Option A",
		"msgID": "431edeb4-897a-11e7-bc88-1a2d46004c10"
		"msgType": 101
		}, ...],
	"sendTime": 1503654386314,
	"senderID": "avzrg6mkjqchp5o4",
	"showVoter": true,
	"showVotingResult": true,
	"timeToCloseVote": 1503740785898,
	"timeToSend": 1503654386319,
	"timeToStartVote": 1503654386319,
	"transID": "b3e1441e-77fd-4bf3-72c3-ea570e827590",
	"updateVotingResult": true,
	"votingTimes": 1
}
```

[Top](#index)
#### <a name="im_castVote">投票</a>
投票的為回應值請使用api中的listener取得，當server回應成功後，server會發送投票訊息給聊天室裡的所有成員，請參考[IMMessage](#im_message).</br>

#### Request parameters description
* **voteOptionMsgID** *(M, String)* : The message ID of vote option.
* **callback** *(O, Object)* : Response function.

```
castVote(voteOptionMsgID, function(obj) {
    ...
}
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful.
* **chID** *(M, String)* : The channel ID.
* **chType** *(M, String)* : The channel type.
* **encrypted** *(M, Boolean)* : The value is true while the message content is encrypted in database. otherwise, the value is false.
* **extInfo** *(M, String)* : vote info
* **good** *(M, String)* : The value is 1, if the sender agree to join the channel. Otherwise, the parameter is absent.
* **isRead** *(M, Boolean)* : Whether the message is read by the receiver.
* **msgCategory** *(M, String)* : vote_ballot_open
* **msgContent** *(M, String)* : 若此使用者投票的選項是開放式問題，此欄位會帶出使用者填使用者輸入的回覆訊息。若是封閉式問題，則此欄位就是帶出使用者所選擇的選項名稱。
* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
* **msgType** *(M, Integer)* : 若是開放式選項題目，則會是使用者回覆的訊息類型101~108。若是封閉式題目，則此欄位值是109。
* **parentMsgID** *(M, String)* : Vote ID
* **sendTime** *(M, Integer)* : The timestamp in milliseconds where the voting is created.
* **senderID** *(M, String)* : The userID who create the voting message.
* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message.
* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.

#### Response Example

```
{
  	"returnCode": 0,
	"chID": "10b971bc-3556-44f1-a908-f649d2cd6aac",
	"chType": "4",
	"encrypted": true,
	"extInfo": "true,true,true,1,false",
	"good": 1,
	"isRead": false,
	"msgCategory": vote_ballot_open,
	"msgContent": "6e35c432-8b96-11e7-bc88-1a2d73005fd7",
	"msgID": "55958f48-8970-11e7-ae46-1a2d46000439",
	"msgType": 109,
	"parentMsgID": "6e35c432-8b96-11e7-bc88-1a2d73005fd7",
	"sendTime": 1503639209583,
	"senderID": "avzrg6mkjqchp5o4",
	"timeToSend": 1503639209583,
	"transID": "a281d827-7f2f-4269-b864-ba6e2bbfd6c9"
}
```

[Top](#index)
#### <a name="im_queryVote">投票列表</a>
進聊天室時可先利用queryVote取得投票列表，才能正確顯示。此為iq message，請參考[IQMessage](#im_iqmessage).</br>

#### Request parameters description
* **chID** *(M, String)* : 是否要指定特定Channel ID下的投票項目。
* **msgID** *(M, String)* : 可以指定msgID只查某一項投票的資訊。若有指定msgID,則以上chID,
msgCategory, afterN, 與markTS等參數皆不再具有任何意義。
* **markTS** *(M, Integer)* : The timestamp of the last message which the sender received. All messages
with send-time less than or equal to the markTS will be mark as read by the sender.
* **afterN** *(M, Integer)* : If afterN > 0, then the afterN messages with sendTime > markTS will be returned and order by sendTime ascendingly. If afterN < 0, then then afterN messages with sendTime < markTS will be returned and ordered by sendTime descendingly.
* **callback** *(O, Object)* : Response function.

```
queryVoteList(chID, msgID, markTS, afterN, function(obj) {
    ...
}
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful.
* **chID** *(M, String)* : The channel ID.
* **count** *(M, Integer)* : Vote count
* **markTS** *(M, Integer)* : The markTS which is the same as the sender's request.
* **votes** *(M, Array)* : The array of message objects which containg the following key/values.
	* **chID** *(M, String)* : The channel ID.
	* **chType** *(M, String)* : The channel type.
	* **count**	*(M, Integer)* : The voting count of the voting option.
	* **msgCategory** *(M, String)* : vote_question
	* **msgContent** *(M, String)* : Voting subjec
	* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
	* **msgType** *(M, Integer)* : The message type.
	* **msgValidTimestamp** *(M, Integer)* : Valid time of vote
	* **options** *(M, Array)* : The json array of options of the voting.
		* **count** *(M, Integer)* : The voting count of the voting option.
		* **isOpen** *(M, Integer)* : If the option is an open question for the voter to reply.
		* **msgCategory** *(M, String)* : vote_option.
		* **msgContent** *(M, String)* : The subject of each option.
		* **msgID** *(M, String)* : The msgID of the voting option.
		* **msgType** *(M, Integer)* : The msgType of the option.
	* **sendTime** *(M, Integer)* : The timestamp in milliseconds where the voting is created.
	* **senderID** *(M, String)* : The user ID who send the message.
	* **showVoter** *(M, Integer)* : To show the voter's information or not. The default value is false.
	* **showVotingResult** *(M, Integer)* : To show the voting result while the voting is closed. The default value is true.
	* **timeToCloseVote** *(M, Integer)* : The timestamp in milliseconds to close the voting.
	* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message.
	* **timeToStartVote** *(M, Integer)* : The timestamp in milliseconds to start the voting. The default value is current time.
	* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.
	* **updateVotingResult** *(M, String)* : To update the voting result while the voting is still open. The default value is true.
	* **votingTimes** *(M, String)* : The voting times for each voter.

#### Response Example

```
{
  "returnCode": 0,
	"chID": "10b971bc-3556-44f1-a908-f649d2cd6aac",
	"count": 1,
	"markTS": 0,
	"votes": [{
		"chID": "10b971bc-3556-44f1-a908-f649d2cd6aac",
		"chType": "4",
		"count": 1,
		"msgCategory": "vote_question",
		"msgContent": "SDK vote test",
		"msgID": "6e352806-8b96-11e7-ae46-1a2d73005fd7",
		"msgType": 101,
		"msgValidTimestamp": 1503972786452,
		"options": [{
			"count": 1,
			"isOpen": false,
			"msgCategory": "vote_option",
			"msgContent": "Option A",
			"msgID": "6e35c432-8b96-11e7-bc88-1a2d73005fd7",
			"msgType": 101	
		}, ...],
		"sendTime": 1503886386842,
		"senderID": "avzrg6mkjqchp5o4",
		"showVoter": true,
		"showVotingResult": true,
		"timeToCloseVote": 1503972786452,
		"timeToSend": 1503886386845,
		"timeToStartVote": 1503886386845,
		"transID": "c8f5c3a4-a1f8-4c52-87c7-d183eda7dab8",
		"updateVotingResult": true,
		"votingTimes": 1
	}, ...]
}
```

[Top](#index)
#### <a name="im_voteOption">投票選項資訊</a>
取得每一個投票中選擇項目，包含此項目投票人數.此為iq message，請參考[IQMessage](#im_iqmessage).</br>
使用時機：查詢每個投票的投票人數總計以及名單。

#### Request parameters description
* **optionMsgIDs** *(M, Array)* : The array of options' message IDs.
* **callback** *(O, Object)* : Response function.

```
queryOptions(voteOptionMsgIDs, function(obj) {
    ...
}
```

#### Response parameters description
* **returnCode** *(M, Integer)* : 0 : Request is successful.
* **count**	*(M, Integer)* : The count of voting options.
* **options**	*(M, Array)* : The array of message objects which containg the following key/values.
	* **chID** *(M, String)* : The channel ID.
	* **chType** *(M, String)* : The channel type.
	* **count**	*(M, Integer)* : The count of voting options.
	* **msgCategory** *(M, String)* : vote_question.
	* **msgContent** *(M, String)* : The subject of each option.
	* **msgID** *(M, String)* : The IM server generated message ID in UUID(type 1) format.
	* **msgType** *(M, Integer)* : The msgType of the option.
	* **msgValidTimestamp** *(M, Integer)* : Valid time of vote
	* **sendTime** *(M, Integer)* : The timestamp in milliseconds where the voting is created.
	* **senderID** *(M, String)* : The user ID who send the message.
	* **showVoter** *(M, Integer)* : To show the voter's information or not. The default value is false.
	* **showVotingResult** *(M, Integer)* : To show the voting result while the voting is closed. The default value is true.
	* **timeToCloseVote** *(M, Integer)* : The timestamp in milliseconds to close the voting.
	* **timeToSend** *(M, Integer)* : The timestamp in illiseconds to send the message.
	* **timeToStartVote** *(M, Integer)* : The timestamp in milliseconds to start the voting. The default value is current time.
	* **transID** *(M, String)* : The client's message ID in UUID(type 1) format.
	* **updateVotingResult** *(M, String)* : To update the voting result while the voting is still open. The default value is true.
	* **votingTimes** *(M, String)* : The voting times for each voter.
	* **ballots** *(M, Array)* : The json array of ballots of the voting option.
		* **msgContent** *(M, String)* : 若是開放式選項題目,則會是使用者回覆的訊息內容。若是封閉式題目,則此欄位值是使用者投票的選項msgID。
		* **msgType** *(M, Integer)* : 若是開放式選項題目,則會是使用者回覆的訊息類型101~108。若是封閉式題目,則此欄位值是109。
		* **sendTime** *(M, Integer)* : 使用者頭此選項的時間 (milliseconds)。
		* **senderID** *(M, String)* : 如果這選項是不記名投票，則不會有此欄位。 如果是記名投票，才會有此欄位。

#### Response Example

```
{
	"returnCode": 0,
	"count": 1,
	"options": [{
		"chID": "10b971bc-3556-44f1-a908-f649d2cd6aac",
		"chType": "4",
		"count": 1,
		"msgCategory": "vote_question",
		"msgContent": "SDK vote test",
		"msgID": "6e352806-8b96-11e7-ae46-1a2d73005fd7",
		"msgType": 101,
		"msgValidTimestamp": 1503972786452,
		"sendTime": 1503886386842,
		"senderID": "avzrg6mkjqchp5o4",
		"showVoter": true,
		"showVotingResult": true,
		"timeToCloseVote": 1503972786452,
		"timeToSend": 1503886386845,
		"timeToStartVote": 1503886386845,
		"transID": "c8f5c3a4-a1f8-4c52-87c7-d183eda7dab8",
		"updateVotingResult": true,
		"votingTimes": 1
		"ballots": [{
			"msgContent": "6e35c432-8b96-11e7-bc88-1a2d73005fd7",
			"msgType": 109,	
			"sendTime": 1503886388205,
			"senderID": "avzrg6mkjqchp5o4"
		}, ...]
	}, ...]
}
```

[Top](#index)
### <a name="storage">File Manage</a>
#### <a name="storage_download_file">Get file link (image, video, voice, documents)</a>

When you receive image, video, voice, document messages or messages such as other member or channels change thier profiles, you can get file link by this method.

#### Request parameters description
* **id** *(M, String)* : Pass chID if you want to get channel profile. Pass userID if you want to get user profile.
* **ownerID** *(M, String)* : Storage ID.
* **ownerDomain** *(M, String)* : Storage server domain.
* **remoteFileName** *(M, String)* : Filename in stroage server. Get the filename in response message by getStorageInfoObject().
* **remoteFilePath** *(M, String)* : File path in stroage server. Get the path in response message by getStorageInfoObject().
* **contentType** *(M, String)* : File type in stroage server. Get the path in response message by getStorageInfoObject().
* **callback** *(O, Object)* : Response function.

```
function getFileDownloadURL(id, ownerID, ownerDomain, remoteFileName, contentType, function(obj) {
    ...
}
```

#### Response parameters description
* **status** : success (操作成功)
* **response**	*(M, String)* : The file download url.

[Top](#index)

### <a name="appendix"> Appendix </a>
#### <a name="appendix_A">Appendix A. Error Code List </a>
|Return Code|Description|
|:---:|:---|
|1|Unthorized|
|2|Invalid authorization|
|3| No authorization|
| 1000| Unknown error|
| 1001| Not supported format|
| 1002| Not supported command|
| 1003| IM database server access error|
| 1004| Request too many times for the API|
| 1101| Channel ID's format error|
| 1102| Channel ID is already exist|
| 1103| Channel type is not supported|
| 1104| Channel type is not matched|
| 1008| Schedule time error|
| 1105| Channel ID is not exist|
| 1106| UserID is not in the channel|
| 1107| The user is without privilege|
| 1108| The number of members in the channel is below the limit|
| 1109| No user to kick|
| 1110| No user to ban|
| 1111| No channel preference to set|
| 1112| No user profile to set|
| 1113| The message is not belong to the user|
| 1114| One of the members is not in the channel|
| 1115| The number of members in the channel is over the upper limit|
| 1116| The user is not invited in the channel|
| 1117| The user is not in the channel|
| 1118| Message not exist|
| 1119| Message is already sent or exist|
| 1120| No user privilege to set|
| 1121| Time expire|
| 1122| More than the number of voting|
| -1| No network|
| -2| No connected exception|
| -3| Interrupted exception|
| -4| Not logged in exception|
| -5| Process exception, need to get error message|
| -6| Runtime exception, need to get error message|

[Top](#index)
#### <a name="appendix_B">Appendix B. Message Type List </a>
Y: Yes 
N: No
O: Option

|msgType|insertMessage|insertLastMessage|sendXMPP|sendNotification|addUnreadCount|Comment|
|:---:|:---:|:---:|:---:|:---:|:---|:---:|
|1|Y|Y|Y|Y|Y|Text message|
|2|Y|Y|Y|Y|Y|Sticker message|
|3|Y|Y|Y|Y|Y|Picture message|
|4|Y|Y|Y|Y|Y|Video message|
|5|Y|Y|Y|Y|Y|Voice message|
|6|Y|Y|Y|Y|Y|Contact message|
|7|Y|Y|Y|Y|Y|Location message|
|8|Y|Y|Y|Y|Y|File message|
|101|Y|Y|Y|Y|Y|Voting's Text Content|
|102|Y|N|Y|Y|Y|Voting's Sticker Content|
|103|Y|N|Y|Y|Y|Voting's Picture Content|
|104|Y|N|Y|Y|Y|Voting's Video Content|
|105|Y|N|Y|Y|Y|Voting's Voice Content|
|106|Y|N|Y|Y|Y|Voting's Contact Content|
|107|Y|N|Y|Y|Y|Voting's Location Content|
|108|Y|N|Y|Y|Y|Voting's File Content|
|201|N|N|N|N|N|Send scheduled message|
|202|Y|N|Y|Y|N|Scheduled message is sent|
|203|N|N|N|N|N|Send scheduled voting message|
|204|Y|N|Y|Y|N|Scheduled voting message is sent|
|205|Y|N|Y|Y|N|Send scheduled message|
|206|N|N|N|N|N|Scheduled message is sent|
|221|Y|Y|Y|N|N|user take a screenshot|
|222|Y|Y|Y|N|N|user make a copy of message|
|223|Y|Y|Y|N|N|user transfer messages to others|
|224|Y|Y|Y|N|N|user saved the channel's message, image, video, voice or file|
|1001|Y|N|Y|Y|-|create a channel|
|1002|Y|N|Y|N|N|dismiss a channel|
|1003|Y|N|Y|N|N|leave a channel|
|1004|Y|N|Y|Y|N|invite members to join a channel|
|1005|Y|N|Y|N|N|respond a channel invitation|
|1006|Y|N|Y|N|N|kick a member out of a channel|
|1007|Y|N|Y|N|N|ban a member|
|1008|Y|N|Y|N|N|change channel's subject|
|1009|Y|N|Y|N|N|change channel's profile image id|
|1010|Y|N|Y|N|N|change members' affiliation and role|
|1011|Y|N|Y|N|N|change user's preference of a channel|
|1012|Y|N|Y|N|N|change user's profile image id and nick name|
|1013|Y|N|Y|N|N|change user's profile image id and nick name in a specific channel|
|1015|Y|N|Y|N|N|change a channel's background image id|
|1017|Y|N|Y|N|N|create a channel|
|1018|Y|N|Y|N|N|change members' affiliation and role|
|1101|-|N|Y|N|N|mark read of a specific channel|
|1102|Y|N|Y|N|N|delete a message|
|1103|Y|N|Y|N|N|delete a channel's all messages|
|1104|Y|N|Y|N|N|recall a message|
|1105|Y|N|Y|N|N|delete user's all channels' all messages|
|1106|N|N|Y|N|N|push channel active user|
|1201|Y|N|Y|N|N|create a public news channel|
|1202|Y|N|Y|Y|Y|create a public news message of the specific channel|
|1203|Y|N|Y|N|N|create a corporation news channel|
|1204|Y|N|Y|Y|Y|create a corporation news message of the specifi corporation channel|
|2001|Y|N|Y|Y|N|incoming call notification|
|2002|Y|N|Y|Y|N|call cancel notification|
|2003|Y|N|Y|N|N|send CDR (call log) message|
|3000|O|O|O|O|O|Send customized message with attribute|
|3001|Y|N|Y|N|N|Customized message without notification and insert last message|
|3002|Y|Y|Y|Y|N|Custom message|

[Top](#index)