## Notification > Push > API v1.3 Guide

### API Test

Available on [CONSOLE] > [Notification] > [Push] > [APIs].

### Secret Key

```
Header
X-Secret-Key: [a-zA-Z0-9]{8}
```

Go to [CONSOLE] > [Notification] > [Push] > [URL & AppKey] to create one. 

### Response

[Response HTTP Status Code]  

200 OK.  
Respond with 200 OK for all API requests.   
See Header at the response body for response details.   

[Response Header]  

```
{
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "Success."
	}
}
```

[resultCode, resultMessage]

| isSuccessful | resultCode | resultMessage |
| --- | --- | --- |
| true | 0 | Success. |
| false | 40001 | Client Error. Wrong URI. |
| false | 40002 | Client Error. Unavailable field value. |
| false | 40003 | Client Error. Bad request. Check your request parameter or body. |
| false | 40004 | Client Error. Target length is exceeded. CHANNEL: 100, UID: 10,000. |
| false | 40005 | Client Error. Content length is exceeded. |
| false | 40006 | Client Error. Wrong target format. Couldn't read two target types at once. Send a target, channels or uids at once. |
| false | 40007 | Client Error. Invalid certificate. |
| false | 40008 | Client Error. Invalid APNS certificate. |
| false | 40009 | Client Error. Invalid APNS Sandbox certificate. |
| false | 40101 | Client Error. Permission denied. Access is not allowed. |
| false | 40102 | Client Error. Unavailable appkey. |
| false | 40402 | Client Error. No messages to send in body. |
| false | 40403 | Client Error. No target in body. |
| false | 40404 | Client Error. Not found certificate. |
| false | 40405 | Client Error. Not found instance. |
| false | 40406 | Client Error. Not found reservation. |
| false | 40407 | Client Error. Unavailable reservation. |
| false | 40408 | Client Error. Not found channel. |
| false | 40409 | Client Error. Not found token. |
| false | 40010 | Client Error. Invalid Tencent certificate. |
| false | 40011 | Client Error. Bad request. Check your content. |
| false | 40012 | Client Error. Expired APNS certificate. |
| false | 40013 | Client Error. Duplicate certificate. |
| false | 40014 | Client Error. Wrong message type. Check contact or removeGuide. |
| false | 40015 | Client Error. Wrong reservationDays. |
| false | 50001 ~ 50501 | Internal Error. Please report this. 'http://cloud.toast.com/support/qaa'. |

### Tokens

##### Register

[Method, URL]

```
POST https://api-push.cloud.toast.com/push/v1.3/appkey/{appkey}/tokens
Content-Type: application/json;charset=UTF-8
```

[Request Body]

```
{
  "channel": "default",
  "oldToken": "oldToken",
  "token": "token",
  "isNotificationAgreement": true,
  "isAdAgreement": true,
  "isNightAdAgreement": true,
  "pushType": "GCM",
  "timezoneId": "Asia/Seoul",
  "uid": "uid",
  "country": "KR",
  "language": "ko"
}
```

[Response Body]

```
{
	"header" : {
		"isSuccessful" : true or false,
		"resultCode": 0,
		"resultMessage" : "Success."
	}
}
```

|Parameter|	Usage||
|---|---|---|
|token|	Required, String| Token, up to 1,600 characters. |
|oldToken|	Optional, String| Old Token, up to 1,600 characters. |
|channel|	Optional, String| Channel name, up to 50 characters. |
|pushType|	Required, String|	GCM, APNS, APNS_SANDBOX, TENCENT, ADM|
|isNotificationAgreement|	Required, Boolean|	true or false|
|isAdAgreement|	Required, Boolean|	true or false|
|isNightAdAgreement|	Required, Boolean|	true or false|
|timezoneId|	Required, String|	Area/Name. IANA time zone database.|
|country|	Required, String| ISO 3166-1 alpha-2, ISO 3166-1 alpha-3, 3 characters. |
|language|	Required, String| ISO 639-1, ISO 639-2, iOS(language code + script code), 8 characters. |
|uid|	Required, String| User ID, 64 characters. |

- If token is registered again when it is already registered, existing information is updated.
- If a token is to be changed, set existing token for oldToken and new token for token, and register; then, it is updated to a new token.
- If channel is registered along with a token, message can be sent to a particular channel. It is not required, and is therefore registered as "default" if not defined. 
- A token can belong to only one channel. 
- "isNotificationAgreement" refers to consent to receive push messages; "isAdAgreement" for ad push messages, and "isNightAdAgreement" for night-time ad push messages.
- For instance, to receive all push messages, set true for all three fields. To receive push messages only, set true for "isNotificationAgreement" only.
- Consent to receive push messages is in accordance with Act on Promotion of Information and Communications Network Utiliztion and Information Protection, etc. (from Article 50 to 50-8).
  - [Go to KISA Guidelines](https://spam.kisa.or.kr/spam/na/ntt/selectNttInfo.do?mi=1020&nttSn=1171&bbsId=1002)
  - [Check the Act](http://www.law.go.kr/lsEfInfoP.do?lsiSeq=123210#)
- Response may be delayed due to many reasons, including bad network connection. To minimize effects on mobile application operations, it is recommended to set shorter timeout, and register tokens every time they are operated.
- Tokens may be re-issued, on many accounts, including security issues, or app updates or deletion. Although they may not be frequently changed, it is recommended to register the most updated tokens whenever they are operated, so as to raise the receiving rate.
- Even if a token is expired due to app deletion, it is not immediately applied to GCM or APNS server, so push message delivery can be successful after app is deleted.

#### Query

###### a. Query Tokens by Token and Push Type

[Method, URL]

```
GET https://api-push.cloud.toast.com/push/v1.3/appkey/{appkey}/tokens?token={token}&pushType={pushType}
Content-Type: application/json;charset=UTF-8
```

[Response Body]

```
{
	"token" : {
		"channel": "default",
		"pushType" : "GCM",
		"isNotificationAgreement": true,
		"isAdAgreement": true,
		"isNightAdAgreement": true,
		"timezoneId" : "Asia/Seoul",
		"country": "KR",
		"language": "ko",
		"uid" : "User ID",
		"token" : "Token"
	},
	"header" : {
		"isSuccessful" : true or false,
		"resultCode": 0,
		"resultMessage" : "Success."
	}
}
```

##### b. Query Tokens by UID

[Method, URL]

```
GET https://api-push.cloud.toast.com/push/v1.3/appkey/{appkey}/uids/{uid}/tokens
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

[Response Body]

```
{
	"tokens": [{
		"channel": "default",
		"pushType" : "GCM",
		"isNotificationAgreement": true,
		"isAdAgreement": true,
		"isNightAdAgreement": true,
		"timezoneId" : "Asia/Seoul",
		"country": "KR",
		"language": "ko",
		"uid" : "User ID",
		"token" : "Token"
	}],
	"header" : {
		"isSuccessful" : true or false,
		"resultCode": 0,
		"resultMessage" : "Success."
	}
}
```

- The API requires a secret key and must be called from a server.

### Messages

#### Send 
※ Push messages sent using the API cannot be retrieved in the console or by the message query API.

[Method, URL]

```
POST https://api-push.cloud.toast.com/push/v1.3/appkey/{appkey}/messages
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

[Request Body]

```
{
	"target" : {
		"type" : "ALL"
	},
	"content" : {
		"default" : {
            "title": "title",
            "body": "body"
        }
	},
	"messageType" : "NOTIFICATION"
}
```

[Response Body]

```
{
	"message" : {
		"messageId" : long
	},
	"header" : {
		"isSuccessful" : true or false,
		"resultCode": 0,
		"resultMessage" : "Success."
	}
}
```

| Parameter | Usage |  |
| --- | --- | --- |
| target.type | Required, String | ALL, CHANNEL, UID |
| target.to | Optional, String Array | 100 if target.type is CHANNEL, and 10,000 if it is UID. |
| target.pushTypes | Optional, String Array | GCM, APNS, APNS_SANDBOX, TENCENT, ADM |
| target.countries | Optional, String Array | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3. 3 bytes. |
| content | Required, Map | 8192 bytes |
| content.default | Required, Map |  |
| content.default.title | Optional, String |  |
| content.default.body | Optional, String |  |
| messageType | Required, String | NOTIFICATION, AD |
| contact | Optional, String | Required, if messageType is AD. |
| removeGuide | Optional, String | Required, if messageType is AD. |
| timeToLive | Optional, Number | By the minute, which is more than 1, including 0 (unlimited). Default is 60. |
| isStored | Optional, Boolean | Whether to save messages or not: default is false. |

- Messages can be delivered only in particular push types, with the "target.pushTypes" field.
  If not defined, they can be delivered to all push types, including, GCM, APNS, APNS_SANDBOX, TENCENT, and ADM.
- If the "target.countries" field is "['KR', 'JP']", messages are delivered to tokens of which the national codes are "KR" or "JP".
- "content.default" is a required field, and for more details regarding "content", see [Common Message Format] as below.
- To send messages in the "messageType": "AD" type, it is required to include the "contact" and "removeGuide" fields. Enter contact information in "contact", and how to withdraw receiving in "removeGuide".
- With the timeToLiveMinute setting, delivery delays beyond certain configured time shall be automatically processed as failure.


["target" Example]

```
If "target.type" is "ALL", "target.to" is not required. 
Request Body
{
	"target" : {
		"type" : "ALL"
	},
	"content" : {
		"default" : {
			"title": "title",
			"body": "body"
		}
	}
}
If "target.type" is "UID" or "CHANNEL", enter uid or channel name to sent to "target.to".
Request Body
{
	"target" : {
		"type" : "UID",
		"to" : ["uid-01", "uid-02"]
	},
	"content" : {
		"default" : {
			"title": "title",
			"body": "body"
		}
	}
}
```

["messageType" Example]

```
To send general push notification messages, instead of ad push messages, 
set "messageType" as "NOTIFICATION", and do not enter "contact" and "removeGuide".
Request Body
{
	"target" : {
		"type" : "ALL"
	},
	"content" : {
		"default" : {
			"title": "title",
			"body": "body"
		}
	},
	"messageType" : "NOTIFICATION"
}
To send ad push messages, set "messageType" as "AD", and enter contact in "contact" and how to withdraw consent of receiving in "removeGuide".
Request Body
{
	"target" : {
		"type" : "ALL"
	},
	"content" : {
		"default" : {
			"title": "title",
			"body": "body"
        }
	},
	"messageType": "AD",
	"contact": "Contact",
	"removeGuide": "How to withdraw conset of receiving"
}
```

#### Common Messages 

Common message type is supported from API v1.3. When messages are written for "content" as described in the below table, messages are created and sent to suit for each push type.

|Reserved Word|	Platform|	Usage|	GCM|	APNS|	TENCENT| ADM |
|---|---|---|---|---|---|---|
|title|	Android, <br/> iOS Watch, <br/> Tencent, <br/> ADM|	Optional, String|	data.title|	aps.alert.title|	title| data.title |
|body|	Android, <br/> iOS, <br/> Tencent, <br/> ADM|	Optional, String|	data.body|	aps.alert.body|	content| data.body|
|title-loc-key|	iOS|	Optional, String| - | aps.alert.title-loc-key| - | - |
|title-loc-args|	iOS|	Optional, Array of Strings| - | aps.alert.title-loc-args	| - | - |
|action-loc-key|	iOS|	Optional, String| - |aps.alert.action-loc-key	| - | - |
|loc-key|	iOS|	Optional, String| - |aps.alert.loc-key	| - | - |
|loc-args|	iOS|	Optional, Array of String	| - | aps.alert.loc-args	| - | - |
|launch-image|	iOS|	Optional, String	| - | aps.alert.launch-image	| - | - |
|badge|	iOS|	Optional, Number| - | aps.badge	| - | - |
|sound|	Android, <br/> iOS, <br/> Tencent, <br/> ADM|	Optional, String|	data.sound|	aps.sound|	custom_content.sound| data.sound |
|content-available|	iOS|	Optional, String	| - | aps.content-available	| - | - |
|category|	iOS|	Optional, String	| - | aps.category	| - | - |
|mutable-content| iOS | Optional, String | - | aps.mutable-content | - | - |
|consolidationKey| ADM | Optional, String | - | - | - | consolidationKey |
|expiresAfter| ADM | Optional, Number | - | - | - | expiresAfter |

Other user-defined words are included to Custom Key/Value as follows:

|Reserved Word|	Platform|	Usage|	GCM|	APNS|	TENCENT| ADM|
|---|---|---|---|---|---|---|
|customKey|	Android, <br/> iOS, <br/> Tencent, <br/> ADM|	Optional, <br/> Object, <br/> Array, <br/> String, <br/> Number|	data.customKey|	customKey|	custom_content.customKey| data.customKey|

["content" Example]

```
"content.default" must be included. "content.ko" and "content.ja" below are language token values of a language. 
Message is sent for each token's language code. 
Request Body
{
	"target" : {
		"type" : "ALL"
	},
	"content" : {
        "default" : {
            "title": "title",
            "body": "body",
            "badge": 1,
            "key": "value"
        },
        "ko" : {
            "title": "title",
            "body": "body"
            "key": "value"
        },
        "ja" : {
            "title": "タイトル",
            "body": "プッシュ・メッセージ"
        }
	},
	"messageType" : "NOTIFICATION"
}
"ko" GCM message 
 {
    "data": {
        "title": "title",
        "body": "body",
        "key": "value"
    }
}
"ja" GCM message
 {
    "data": {
        "title": "タイトル",
        "body": "プッシュ・メッセージ",
        "key": "value"
    }
}
"ko" APNS message
{
    "aps": {
        "alert": {
            "title": "title",
            "body": "body"
        },
        "badge": 1
    },
    "key": "값"

}
"ja" APNS message
{
    "aps": {
        "alert": {
            "title": "タイトル",
            "body": "プッシュ・メッセージ"
        },
        "badge": 1
    },
    "key": "value"
}
"ko" TENCENT message
 {
	"title": "title",
	"body": "body",
	"custom_content": {
		"key": "value"
	}
}
"ja" TENCENT message 
 {
	"title": "タイトル",
	"body": "プッシュ・メッセージ",
	"custom_content": {
		"key": "value"
	}
}
"ko" ADM message
{
  "data":{
    "title":"title",
    "body":"body",
    "customKey":"value"
  },
  "consolidationKey":"",
  "expiresAfter":60
}
"ja" ADM message
{
  "data":{
    "title":"タイトル",
    "body":"プッシュ・メッセージ",
    "customKey":"value"
  },
  "consolidationKey":"",
  "expiresAfter":60
}
```

#### Query 
※ Only push messages sent using the console can be retrieved by the Query API.

[Method, URL]

```
GET https://api-push.cloud.toast.com/push/v1.3/appkey/{appkey}/messages/{message-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

[Response Body]

```
{
  "header": {
    "resultCode": 0,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "message": {
    "messageId": 42983,
    "messageType": "NOTIFICATION",
    "target": {
      "type": "ALL",
      "pushTypes": [
        "GCM"
      ]
    },
    "content": {
      "default": {
        "title": "title",
        "body": "body"
      }
    },
    "targetCount": 1048576,
    "timeToLive": 0,
    "sentTime": "2015-11-23T18:39:38.000+0900",
    "createdDateTime": "2015-11-23T18:39:38.000+0900",
    "messageStatus": "COMPLETE"
  }
}



```

"messageStatus" shows the status of a message, as follows:

- READY: Request for message delivery has been registered.
- PROCESSING: Message is completely created, and ready or under delivery.
- COMPLETE: Message delivery is completed.
- CANCEL_NO_TARGET: Message delivery has been canceled as target is unavailable. Delivery may be canceled on the following accounts:  

​      When there is no registered token;   
      When there is no channel or UID at issue;  
      For ad push messages, when no user has agreed to receive ones; 
      For night-time ad push messages (21pm to 8am), when no user has agreed to receive ones; and,     
      When there is no available token, since previously-registered tokens are deleted.     

- CANCEL_INVALID_CERTIFICATE: Canceled because certificate is invalid. Check certificate status.
- CANCEL_INVALID_MESSAGE: Canceled due to invalid message type.
- CANCEL_UNSUPPORTED_MESSAGE_TYPE: Canceled due to invalid message type.
- CANCEL_UNAUTHORIZED: Failed while authenticating certificate. Check certificate status.
- CANCEL_UNKNOWN: Error has occurred internally.

### Feedbacks

#### Check 

[Method, URL]

```
GET https://api-push.cloud.toast.com/push/v1.3/appkey/{appkey}/feedback
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

[Response Body]

```
{
	"feedback" : [{
		"uid" : "Uid",
		"token" : "Deleted Token",
		"newToken" : "New Token. Nullable",
		"pushType" : "GCM",
		"updateTime" : "yyyy-MM-dd'T'HH:mm:ss.SSSZ"
	}],
	"header" : {
		"isSuccessful" : true or false,
		"resultCode": 0,
		"resultMessage" : "Success."
	}
}
```

<br/>
<br/>
<br/>

* *Document Updates*
    * *(2017.02.23) Document on Query Token API updated*
