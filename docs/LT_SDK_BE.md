# Manage API

<sub>Last update time: 2021/02/25</sub>

---

## Certification token

-   The API must be used to obtain the **_{{Auth\_Token}}_** (OAuth JWT) issued by Loftech in order to calling others API. Refer to [RFC7519] or [https://jwt.io](https://jwt.io) for detailed JWT specifications.

-   The **_{{Auth\_Token}}_** obtained for each call has a certain validity period, it is recommended to temporarily store this **_{{Auth\_Token}}_**, so as not to affect the system performance and time per call. Normally, the period is **30** minutes.

-   It is recommended to check the validity period of this **_{{Auth\_Token}}_** before calling others API every time this **_{{Auth\_Token}}_** is used. If the validation period expires or the expiration date is less than 1 minute, it is recommended to get the new **_{{Auth\_Token}}_** first to prevent Loftech server rejecting the expired token.

### Endpoint

The endpoint used for request a certification token is hosted as the following:

```curl
{{authapi-url}}/oauth2/getDeveloperToken
```

### Request headers

| Header | Value | Description |
| --- | --- | --- |
| Authorization | <code>Basic **_{{Basic\_Auth}}_**</code> | Please contact technical service to get the developer ID and secret, and follow the HTTP Basic Authentication[RFC 2617]。<br>According to the RFC 2617 Basic Authentication rule, **_{{Basic\_Auth}}_** is a base64 encoded string by combining the **_{{Developer\_Account}}_** and **_{{Developer\_Password}}_** separated by a single colon (":") character. |
| Content-Type | `application/json` | Every request must include a **Content-Type** header. |
| Brand-Id | <code>**_{{Brand\_ID}}_**</code> | The identifier of your brand. |

### Request Body (JSON Format)

```json
{
    "scope": "tw:api:sdk"
}
```

| Property | Type | Description |
| --- | --- | --- |
| scope | string | The permission to access to Loftech's services.<br>It should be a fixed value: `tw:api:sdk`. |

### Response Body (JSON Format)

```json
{
    "returnCode": 0,
    "accessToken": "eyJ0eXAiOierV1QiLCJhbGciOiJSU...."
}
```

| Property | Type | Description |
| --- | --- | --- |
| returnCode | number | Return status, 0 represents succeeded. |
| description | string | The error description (Only occurs when returnCode is not 0). |
| accessToken | string | The **_{{Auth\_Token}}_** (OAuth JWT) you successfully accessed. |

#### About **_{{Auth\_Token}}_**

-   The obtained **_{{Auth\_Token}}_** (OAuth JWT) is decoded with Base64, or you can analyze the following information using [jwt.io website](https://jwt.io).
-   Its payload includes the following information:

| Property | Type | Description |
| --- | --- | --- |
| id | string | ID of JWT. |
| iss | string | JWT issuer. |
| aud | string | JWT issued DeveloperID. |
| sub | string | JWT that represent User ID. |
| exp | number | JWT expired Timestamp(Measure of unit = seconds), this timestamp must be greater than iat's timestamp. |
| iat | number | JWT issued Timestamp(Measure of unit = seconds). |
| token_type | string | It should be set to `bearer`. |
| scope | string | The permission to access to Loftech's services. |

---

<br>
<br>

## Register Account

Register LT SDK members for third-party BE.

### Endpoint

The endpoint used for register SDK users is hosted as the following:

```curl
{{antman-url}}/oauth2/register
```

### Request headers

| Header | Value | Description |
| --- | --- | --- |
| Authorization | <code>Bearer **_{{Auth\_Token}}_**</code> | For OAuth 2.0, it is required for Loftech server to identy and authenticate your SDK. |
| Content-Type | `application/json` | Every request must include a **Content-Type** header. |
| Brand-Id | <code>**_{{Brand\_ID}}_**</code> | The identifier of your brand. |

### Request Body (JSON Format)

```json
{
    "verifyMode": "turnkey",
    "turnkey": "{{LTSDK_TurnKey}}",
    "users": [
        {
            "semiUID": "0912345678",
            "semiUID_PW": "123456"
        },
        {
            "semiUID": "user001",
            "semiUID_PW": "123456"
        }
    ]
}
```

| Property | Type | Description |
| --- | --- | --- |
| verifyMode | string | There is a fixed value: `turnkey`. |
| turnkey | string | The **_{{LTSDK\_TurnKey}}_** issued by Loftech for your branded App. |
| users | array of user object | A list of user objects you want to register to the SDK user. Maximum is **100** items. |
| users.semiUID | string | The customized unique key of the member in your brand who wants to register the SDK.<li>支援任意字串，不區分大小寫。</li><li>最多不可超過 20 碼。</li><li>若需以手機號碼註冊時，請務必使用 E-164 手機號碼格式。</li>|
| users.semiUID_PW | string | The password of the semiUID.<li>不可包含空字串，最多不可超過 20 碼。</li>|

### Response Body (JSON Format)

```json
{
    "returnCode": 0,
    "users": [
        {
            "semiUID": "+886912345678",
            "userID": "axlqggyyfqcaaorf",
            "uuid": "0a2be124-1234-4c6a-5678-05353dd1b2c4"
        },
        {
            "semiUID": "user001",
            "userID": "axlqggyytacaal4v",
            "uuid": "ab6aad54-1234-4b90-5678-6ba26e3f2153"
        }
    ]
}
```

| Property | Type | Description |
| --- | --- | --- |
| returnCode | number | Return status, 0 represents succeeded. |
| users | array of user object | A list of user objects you successfully requested to register to SDK . |
| users.semiUID | string | The ID of the user in your brand who wants to register the SDK. |
| users.userID | string | The identifier of SDK user. |
| users.uuid | string | The password of SDK user. |

---

<br>
<br>

## Authenticate Account

Authenticate LT SDK members for third-party BE.

### Endpoint

The endpoint used for authenticate SDK users is hosted as the following:

```curl
{{antman-url}}/oauth2/authenticate
```

### Request headers

| Header | Value | Description |
| --- | --- | --- |
| Authorization | <code>Bearer **_{{Auth\_Token}}_**</code> | For OAuth 2.0, it is required for Loftech server to identy and authenticate your SDK. |
| Content-Type | `application/json` | Every request must include a **Content-Type** header. |
| Brand-Id | <code>**_{{Brand\_ID}}_**</code> | The identifier of your brand. |

### Request Body (JSON Format)

```json
{
    "semiUID": "+886912345678",
    "semiUID_PW": "123456"
}
```

| Property | Type | Description |
| --- | --- | --- |
| semiUID | string | The customized unique key of the member to authenticate.|
| semiUID_PW | string | The password of the semiUID to authenticate.|

### Response Body (JSON Format)

```json
{
    "returnCode": 0,
    "semiUID": "user001",
    "userID": "axlqggyytacaal4v",
    "uuid": "ab6aad54-1234-4b90-5678-6ba26e3f2153"
}
```

| Property | Type | Description |
| --- | --- | --- |
| returnCode | number | Return status, 0 represents succeeded. |
| semiUID | string | The ID of the user in your brand who wants to register the SDK. |
| userID | string | The identifier of SDK user. |
| uuid | string | The password of SDK user. |

---

<br>
<br>



## Return Code

Return code for manager api

| Property | Description |
| --- | --- |
|0|正確|
|6|參數有誤|
|10|註冊資料有錯     |
|601|密碼錯誤  |
|602|帳號不存在|
|603|帳號已存在|
|604|註冊需要輸入密碼|
|605|品牌註冊人數超出了上限|
---

<br>
<br>