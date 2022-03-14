## Notification > Push > API v1.3ガイド

### APIテスト

[CONSOLE] > [Notification] > [Push] > [APIs]タブで行えます。

### Secret Key

```
Header
X-Secret-Key: [a-zA-Z0-9]{8}
```

[CONSOLE] > [Notification] > [Push] > [URL & AppKey]で作成できます。

### Response

[Response HTTP Status Code]  

200 OK.  
すべてのAPIリクエストに対して200 OKレスポンスを返します。
詳細なレスポンス結果は、Response BodyのHeaderで確認できます。

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
| false | 40006 | Client Error. Wrong target format. Clound't read two target types at once. Send a target, channels or uids at once. |
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
| false | 50001 ～ 50501 | Internal Error. Please report this. 'http://cloud.toast.com/support/qaa'. |

### トークン

##### トークンの登録

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
|token|	Required, String|	Token. 最大1,600文字。|
|oldToken|	Optional, String|	Old Token. 最大1,600文字。|
|channel|	Optional, String|	Channel name. 最大50文字。|
|pushType|	Required, String|	GCM, APNS, APNS_SANDBOX, TENCENT, ADM|
|isNotificationAgreement|	Required, Boolean|	true or false|
|isAdAgreement|	Required, Boolean|	true or false|
|isNightAdAgreement|	Required, Boolean|	true or false|
|timezoneId|	Required, String|	Area/Name. IANA time zone database.|
|country|	Required, String|	ISO 3166-1 alpha-2, ISO 3166-1 alpha-3. 3文字。|
|language|	Required, String|	ISO 639-1, ISO 639-2, iOS(language code + script code). 8文字。|
|uid|	Required, String|	User ID、64文字。|

- トークン登録時、すでにトークンが登録されている場合は、トークン基準で別の情報がアップデートされる。
- もしトークンが変更された場合は、oldTokenに既存トークンを、 tokenに新しいトークンを設定して登録すると新しいトークンにアップデートされる。
- トークン登録時にChannelを登録すると、メッセージ送信時に特定Channelに送信できる。必須ではなく、定義しなければ"default"で登録される。
- トークンは1つのChannelにのみ属すことができる。
- "isNotificationAgreement"プッシュメッセージの受信に同意するかどうか、"isAdAgreement"広告性プッシュメッセージを受信するかどうか。"isNightAdAgreement"夜間広告性プッシュメッセージを受信するかどうかを表します。
- 例えば、すべてのプッシュメッセージの受信を希望する場合は、フィールド3個をすべてtrueに設定してください。プッシュメッセージのみ受信する場合は、 "isNotificationAgreement"のみtrueに設定してください。
- 受信に同意するかどうかは、韓国情報通信網法の規定(第50条から第50条の8)に従う。
[KISAガイドリンク](http://spam.kisa.or.kr/enr/notice/dataView.jsp?p_No=49&b_No=49&d_No=52)    
[法令リンク](http://www.law.go.kr/lsEfInfoP.do?lsiSeq=123210#)  

- ネットワーク状態が良くないか、複数の理由によるレスポンス遅延が発生することがある。モバイルアプリケーション起動への影響を最小化するためにTimeoutを短く設定し、起動するたびにトークンを登録することを推奨する。
- トークンはセキュリティ的なイシュー、アプリアップデート、削除など、さまざまな理由で再発行されることがある。頻繁に変更されることはないが、受信率を高めるため、起動するたびに最新トークンを登録することを推奨する。
- アプリ削除などでトークンが満了してもすぐにGCM、APNSサーバーに適用されず、アプリ削除後にプッシュメッセージを送信した時、送信が成功することがある。

#### トークン照会

###### a. トークンとプッシュタイプでトークン照会

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

##### b. uidでトークン照会

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

- Secret Keyが必要なAPIで、サーバーで呼び出される必要があります。

### メッセージ

#### メッセージ送信
※ API로 발송한 푸시 메시지는 콘솔과 메시지 조회 API에서 조회할 수 없습니다.

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
| target.to | Optional, String Array | target.typeがCHANNELの場合は100個、 UIDは10,000個。 |
| target.pushTypes | Optional, String Array | GCM, APNS, APNS_SANDBOX, TENCENT, ADM |
| target.countries | Optional, String Array | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3. 3 bytes. |
| content | Required, Map | 8192 bytes |
| content.default | Required, Map |  |
| content.default.title | Optional, String |  |
| content.default.body | Optional, String |  |
| messageType | Required, String | NOTIFICATION, AD |
| contact | Optional, String | messageTypeがADの場合に必要。 |
| removeGuide | Optional, String | messageTypeがADの場合に必要。 |
| timeToLive | Optional, Number | 単位は分。範囲は0(無制限)含む1以上。基本値は60。 |
| isStored | Optional, Boolean | メッセージを保存するかどうか。デフォルト値はfalse。 |

- "target.pushTypes"フィールドに特定プッシュタイプでのみメッセージを送信できる。
定義しなければ、すべてのプッシュタイプ、 GCM、APNS、APNS_SANDBOX、TENCENT、ADMに送信する。
- "target.countries"フィールドが"['KR', 'JP']"の場合、トークン国コードが"KR"または"JP"のTokenに送信する。
- "content.default"フィールドは必須で、"content"フィールドについての詳細は下記[共通メッセージフォーマット]を参照。
- メッセージを広告タイプ、"messageType": "AD"に送る場合、"contact"、"removeGuide"フィールドを必ず含める必要がある。
"contact"フィールドに連絡先を入力する必要があり、"removeGuide"フィールドに受信解除方法について入力する必要がある。
- timeToLiveフィールドを設定すると、設定した時間以上に送信が遅延する場合、自動的に失敗処理される。


["target" Example]

```
"target.type"が"ALL"の場合、"target.to"は必要ない。
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
"target.type"が"UID"または"CHANNEL"の場合、"target.to"に送信するuidまたはchannel nameを入力する必要がある。
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
広告性プッシュメッセージではなく、一般通知プッシュメッセージで送信する場合、
"messageType"を"NOTIFICATION"にして、 "contact"、"removeGuide"は入力しない。
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
広告性プッシュメッセージで送る場合、 "messageType"を"AD"にして、"contact"に連絡先、
"removeGuide"に受信同意撤回方法を入力する。
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
	"messageType" : "AD",
	"contact" : "連絡先",
	"removeGuide" : "受信解除方法"
}
```

#### 共通メッセージ

API v1.3から共通メッセージ形式をサポートする。 "content"に下記表の通りにメッセージを作成すると、各プッシュタイプに合わせてメッセージが作成され、送信される。

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

その他のユーザーが定義したWordは、次のようにCustom Key/Valueフィールドに入る。

|Reserved Word|	Platform|	Usage|	GCM|	APNS|	TENCENT| ADM|
|---|---|---|---|---|---|---|
|customKey|	Android, <br/> iOS, <br/> Tencent, <br/> ADM|	Optional, <br/> Object, <br/> Array, <br/> String, <br/> Number|	data.customKey|	customKey|	custom_content.customKey| data.customKey|

["content" Example]

```
"content.default"は必須。下記"content.ko"、"content.ja"は、トークンの言語コード値。
該当トークンの言語コードに合わせてメッセージが送信される。
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
            "title": "タイトル",
            "body": "内容"
            "key": "値"
        },
        "ja" : {
            "title": "タイトル",
            "body": "プッシュ・メッセージ"
        }
	},
	"messageType" : "NOTIFICATION"
}
"ko" GCMメッセージ
 {
    "data": {
        "title": "タイトル",
        "body": "内容",
        "key": "値"
    }
}
"ja" GCMメッセージ
 {
    "data": {
        "title": "タイトル",
        "body": "プッシュ・メッセージ",
        "key": "value"
    }
}
"ko" APNSメッセージ
{
    "aps": {
        "alert": {
            "title": "タイトル",
            "body": "内容"
        },
        "badge": 1
    },
    "key": "値"

}
"ja" APNSメッセージ
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
"ko" TENCENTメッセージ
 {
	"title": "タイトル",
	"body": "内容",
	"custom_content": {
		"key": "値"
	}
}
"ja" TENCENTメッセージ
 {
	"title": "タイトル",
	"body": "プッシュ・メッセージ",
	"custom_content": {
		"key": "value"
	}
}
"ko" ADMメッセージ
{
  "data":{
    "title":"タイトル",
    "body":"内容",
    "customKey":"値"
  },
  "consolidationKey":"",
  "expiresAfter":60
}
"ja" ADMメッセージ
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

#### メッセージ照会
※ 콘솔로 발송한 푸시 메시지만 메시지 조회 API로 조회할 수 있습니다.

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

"messageStatus"フィールドはメッセージの状態を表す。次のような状態がある。

- READY：メッセージ送信リクエストが登録された状態。
- IN_MQ：メッセージの作成が完了し、待機または送信中。
- COMPLETE：メッセージの送信が完了した状態。
- CANCEL_NO_TARGET：メッセージ送信対象が存在しないためキャンセルされた状態。次のような理由で送信がキャンセルされることがある。
 登録されたトークンがない時
 該当ChannelまたはUidがない時
 広告プッシュメッセージの場合、受信に同意したユーザーがいない時
 夜間広告プッシュメッセージ(21時～ 8時)の場合、夜間広告受信に同意したユーザーがいない時
 登録されたトークンが削除され、トークンがない時
- CANCEL_INVALID_CERTIFICATE：証明書が無効でキャンセルされた状態。証明書の状態を確認する必要がある。
- CANCEL_INVALID_MESSAGE：メッセージ形式が合っておらずキャンセルされた状態。
- CANCEL_UNSUPPORTED_MESSAGE_TYPE：メッセージ形式が合っておらずキャンセルされた状態。
- CANCEL_UNAUTHORIZED：証明書の認証プロセスで失敗した状態。証明書の状態を確認する必要がある。
- CANCEL_UNKNOWN：内部エラーが発生した状態。

### フィードバック

#### フィードバックの確認

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

* *文書修正履歴*
    * *(2017.02.23)トークン照会API文書の補強*
