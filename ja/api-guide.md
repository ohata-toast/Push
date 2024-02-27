## Notification > Push > API v2.4ガイド

### v2.4 API紹介

#### 追加
- ｢統計｣APIを追加しました。


### 基本情報
#### Endpoint
```
API Endpoint: https://api-push.cloud.toast.com
メッセージ受信/確認したかどうかを収集Endpoint：https://collector-push.cloud.toast.com
```
### Secret Key
- コンソールで確認可能です。
- Secret Keyが必要なAPIを呼び出す時、ヘッダへ下記のように設定して呼び出す必要があります。
```
Header
X-Secret-Key: [a-zA-Z0-9]{8}
```
[CONSOLE] > [Notification] > [Push] > [URL & AppKey]で確認/作成できます。

#### Response

##### Response HTTP Status Code
200 OK.  
すべてのAPIリクエストに対して200 OKレスポンスを返します。
詳細なレスポンス結果はResponse BodyのHeaderで確認できます。

##### Response Header

```
{
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```

## トークン
### 作成
- クライアントで照会可能です。

##### Method、URL
```
POST /push/v2.4/appkeys/{appkey}/tokens
Content-Type: application/json;charset=UTF-8
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |

##### Request Body

```
{
  "oldToken": "oldToken",
  "token": "token",
  "isNotificationAgreement": true,
  "isAdAgreement": true,
  "isNightAdAgreement": true,
  "pushType": "FCM",
  "timezoneId": "Asia/Seoul",
  "uid": "uid",
  "country": "KR",
  "language": "ko",
  "deviceId": "X3LOdJSQdNzCCvcbiSPZTGK1M9srPU5EumRD"
}
```

|Field|	Usage | Description |
|---|---|---|
|token|	Required, String|	トークン、最大1,600文字、日本語使用不可 |
|oldToken|	Optional, String|	既存トークン、最大1,600文字 |
|pushType|	Required, String| 'FCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
|isNotificationAgreement|	Required, Boolean|	true or false|
|isAdAgreement|	Required, Boolean|	true or false|
|isNightAdAgreement| Required, Boolean|	true or false|
|timezoneId|	Required, String|	Area/Name. IANA time zone database.|
|country|	Required, String|	ISO 3166-1 alpha-2, ISO 3166-1 alpha-3, 3文字|
|language|	Required, String|	ISO 639-1, ISO 639-2, iOS(language code + script code), 8文字|
|uid|	Required, String|	ユーザーID、絵文字不可、最大64文字|
|deviceId|	Required, String|	デバイスID、最大36文字|

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tokens' \
-H 'Content-Type: application/json;charset=UTF-8' \
-d '{
    "oldToken": "oldToken",
    "token": "token",
    "isNotificationAgreement": true,
    "isAdAgreement": true,
    "isNightAdAgreement": true,
    "pushType": "FCM",
    "timezoneId": "Asia/Seoul",
    "uid": "uid",
    "country": "KR",
    "language": "ko",
    "deviceId": "deviceId"
}'
```

##### Response Body

```
{
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```

##### Description

- トークンがすでに登録されている場合、再度登録すると,既存情報をアップデートします。
- もしトークンが変更されたら、oldTokenに既存トークンを、tokenに新しいトークンを設定して登録すると、新しいトークンにアップデートします。
- "isNotificationAgreement"はプッシュメッセージの受信に同意するかどうか、"isAdAgreement"は広告性プッシュメッセージを受信するかどうか、isNightAdAgreement"は夜間広告性プッシュメッセージを受信するかどうかを表します。
例えば、すべてのプッシュメッセージの受信を希望する場合は、フィールド3個をすべてtrueに設定してください。プッシュメッセージのみ受信する場合は、 "isNotificationAgreement"のみtrueに設定してください。
- 受信に同意するかどうかは、韓国情報通信網法の規定(第50条から第50条の8)に従います。
    - [KISAガイドリンク](https://spam.kisa.or.kr/spam/na/ntt/selectNttInfo.do?mi=1020&nttSn=1171&bbsId=1002)    
    - [法令リンク](http://www.law.go.kr/lsEfInfoP.do?lsiSeq=123210#)  
- ネットワーク状態が良くないか、複数の理由によるレスポンス遅延が発生することがあります。モバイルアプリケーション起動への影響を最小化するためにTimeoutを短く設定し、起動するたびにトークンを登録することを推奨します。
- トークンはセキュリティ的なイシュー、アプリアップデート、削除など、さまざまな理由で再発行されることがあります。頻繁に変更されることはないですが、受信率を高めるために、起動するたびに最新トークンを登録することを推奨します。
- アプリ削除などでトークンが満了してもすぐにFCM、APNSサーバーに適用されないため、アプリ削除後にプッシュメッセージを送信した時、送信が成功することがあります。

### 照会
#### トークンリスト照会
##### Method, URL

```
GET /push/v2.4/appkeys/{appkey}/tokens-by-cursor?cursorUid={cursorUid}&cursorToken={cursorToken}&limit={limit}
Content-Type: application/json;charset=UTF-8
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、商品利用時に発行されたアプリケーションキー |
| cursorUid | Optional, String | ユーザーのUIDカーソル。ページ移動時に必須 |
| cursorToken | Optional, String | ユーザーのトークンカーソル。ページ移動時に必須 |
| limit | Optional, Number | 一度に照会するリストサイズ、デフォルト値と最大値は1,000 |

##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tokens-by-cursor' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body

```
{
    "tokens" : [{
        "pushType" : "FCM",
        "isNotificationAgreement" : true,
        "isAdAgreement" : true,
        "isNightAdAgreement" : true,
        "timezoneId" : "Asia/Seoul",
        "country" : "KR",
        "language" : "ko",
        "uid" : "User ID",
        "token" : "Token",
        "updatedDateTime" : "2017-08-12T01:04:18.000+09:00",
        "adAgreementDateTime" : "2017-08-12T01:04:19.000+09:00",
        "nightAdAgreementDateTime" : "2017-08-12T01:04:19.000+09:00",
        "deviceId" : "X3LOdJSQdNzCCvcbiSPZTGK1M9srPU5EumRD",
        "activatedDateTime" : "2017-08-12T01:04:19.000+09:00"
    }],
    "header" : {
        "isSuccessful" : true,
        "resultCode" : 0,
        "resultMessage" : "success"
    }
}
```

##### Description
- ページ移動時、"cursorUid"、"cursorToken"全て必須です。設定した"cursorUid"、"cursorToken"の次の順番から照会します。

#### トークンでトークン照会
- クライアントで照会可能です。
##### Method、URL

```
GET /push/v2.4/appkeys/{appkey}/tokens/{token}?pushType={pushType}
Content-Type: application/json;charset=UTF-8
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |
| pushType | Required, String | 'FCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |

##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tokens/'"${TOKEN}"'?pushType='"${PUSH_TYPE}" \
-H 'Content-Type: application/json;charset=UTF-8'
```

##### Response Body

```
{
    "token" : {
        "pushType" : "FCM",
        "isNotificationAgreement" : true,
        "isAdAgreement" : true,
        "isNightAdAgreement" : true,
        "timezoneId" : "Asia/Seoul",
        "country" : "KR",
        "language" : "ko",
        "uid" : "User ID",
        "token" : "Token",
        "updatedDateTime" : "2017-08-12T01:04:18.000+09:00",
        "adAgreementDateTime" : "2017-08-12T01:04:19.000+09:00",
        "nightAdAgreementDateTime" : "2017-08-12T01:04:19.000+09:00",
        "deviceId" : "X3LOdJSQdNzCCvcbiSPZTGK1M9srPU5EumRD",
        "activatedDateTime" : "2017-08-12T01:04:19.000+09:00"
    },
    "header" : {
        "isSuccessful" : true,
        "resultCode" : 0,
        "resultMessage" : "SUCCESS"
    }
}
```

| Field | Usage | Description |
| - | - | - |
| updatedDateTime | -, DateTime String | トークンアップデート日時 |
| adAgreementDateTime | -, DateTime String | 広告性プッシュメッセージ受信同意日時 |
| nightAdAgreementDateTime | -, DateTime String | 夜間広告性プッシュメッセージ受信同意日時 |
| deviceId | -, String | デバイスID |
| activatedDateTime | -, Datetime String | トークンの最終登録リクエスト日時 |

#### ユーザーIDでトークン照会
- Secret Keyが必要なAPI。サーバーで呼び出す必要があります。
##### Method、URL

```
GET /push/v2.4/appkeys/{appkey}/tokens?uid={uid}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |
| uid | Required, String | 照会するユーザーID |

##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tokens?uid='"${USER_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```


##### Response Body

```
{
	"tokens": [{
		"pushType" : "FCM",
		"isNotificationAgreement": true,
		"isAdAgreement": true,
		"isNightAdAgreement": true,
		"timezoneId" : "Asia/Seoul",
		"country": "KR",
		"language": "ko",
		"uid" : "User ID",
		"token" : "Token",
        "updatedDateTime": "2017-08-12T01:04:18.000+09:00",
        "adAgreementDateTime": "2017-08-12T01:04:19.000+09:00",
        "nightAdAgreementDateTime": "2017-08-12T01:04:19.000+09:00",
        "deviceId" : "X3LOdJSQdNzCCvcbiSPZTGK1M9srPU5EumRD",
        "activatedDateTime" : "2017-08-12T01:04:19.000+09:00"
	}],
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```


#### 有効ではないトークン照会
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/invalid-tokens?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&messageId={messageId}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |
| pageIndex | Optional, Number | 基本値0 |
| pageSize | Optional, Number | 基本値25、最大値100 |
| from | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| messageId | Optional, Number | 有効ではないトークンが発生したメッセージID |

##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/invalid-tokens' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"invalidTokens" : [{
			"messageId" : 0,
			"uid" : "uid",
			"token" : "invalid-token",
			"pushType" : "FCM",
			"createdDateTime" : "2017-02-08T19:39:04.000+09:00"
		}
	]
}
```


### 削除
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/tokens/{token}?pushType={pushType}
Content-Type: application/json;charset=UTF-8
```
| Field | Usage | Description |
| - | - | - |
| token | Required, String | 端末のトークン |
| pushType | Optional, String | トークンのプッシュタイプ。値がない場合は、すべてのプッシュタイプに該当するトークンを削除 |

##### Request Body
```
なし
```

##### cURL
```
curl -X DELETE \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tokens/'"${TOKEN}"'?pushType='"${PUSH_TYPE}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" : true,
        "resultCode" : 0,
        "resultMessage" : "Success."
    }
}
```

## メッセージ
### 送信
※ APIで送信したプッシュメッセージはコンソールと単件、リスト照会APIで照会できません。 APIで送信したプッシュメッセージはLogging機能を有効にしてからログ照会APIを利用してください。
##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/messages
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

##### Request Body
```json
{
    "target" : {
        "type" : "UID",
        "to": ["uid-1", "uid-2"]
    },
    "content" : {
        "default" : {
            "title": "title",
            "body": "body"
        }
    },
    "messageType" : "AD",
    "contact": "1588-1588",
    "removeGuide": "メニュー > 設定",
    "timeToLiveMinute": 1,
    "provisionedResourceId": "id",
    "adWordPosition": "TITLE"
}
```

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/messages' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "target": {
        "type": "ALL"
    },
    "content": {
        "default": {
            "title": "title",
            "body": "body",
            "customKey1": "It is default"
        },
        "ko": {
            "title": "제목",
            "body": "내용",
            "customKey2": "한국어 입니다."
        }
    },
    "messageType": "AD",
    "contact": "1588-1588",
    "removeGuide": "メニュー > 設定"
}'
```

##### Response Body
```json
{
    "message" : {
        "messageId" : 0,
        "messageIdString": "0"
    },
    "header" : {
        "isSuccessful" : true,
        "resultCode": 0,
        "resultMessage" : "SUCCESS"
    }
}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |
| target.type | Required、String | 'ALL'、'UID'、'TAG'受信ターゲットタイプ |
| target.to | Optional, String Array | target.typeが受信者UIDリスト(最大10,000個)またはTAG条件 |
| target.pushTypes | Optional, String Array | 'FCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
| target.countries | Optional, String Array | ISO 3166-1 alpha-2、ISO 3166-1 alpha-3(最大3文字) |
| content | Required, Map | 受信者に送信された内容(最大8,192文字) |
| content.default | Required, Map | '詳細は、下記の共通メッセージ形式を'参照 |
| content.default.title | Optional, String |  |
| content.default.body | Optional, String |  |
| content.default.notification | Optional, Object | FCMで使用するnotificationフィールド |
| content.default.style.useHtmlStyle | Optional, Boolean | 「true」に設定すると、iOSでHTMLが除去されたメッセージが表示されます。 |
| messageType | Required, String | NOTIFICATION, AD |
| contact | Optional, String | messageTypeがADの場合は必須、数字(0-9)とハイフン(Hypen, -)のみ可能です。 |
| removeGuide | Optional, String | messageTypeがADの場合は必須 |
| timeToLiveMinute | Optional, Number | 単位は分です。範囲は1から60。基本値は10です。 |
| provisionedResourceId | Optional, String | 割り当てられた専用リソース(provisioned Resource) IDです。お問い合わせsupport@cloud.toast.com |
| adWordPosition | Optional、String | 'TITLE'、'BODY'広告表示文言位置。基本値は'TITLE'です。 |

##### Description
- "target.type"に'UID'を設定した時、"target.to"に最大10,000個までUIDを設定できます。
- "target.type"に'TAG'を設定した時、"target.to"にタグIDと3個の条件と1個の括弧('()')を入れた条件を設定できます。
    - 例、男性、30代タグがついているか、女性タグがついている対象にメッセージを送信するなら、
    "target.to=(、男性_ID,AND,30代_ID,),OR、女性_ID"に設定できます。
- "target.pushTypes"フィールドに特定プッシュタイプでのみメッセージを送信できます。
定義しなければすべてのプッシュタイプ、 FCM、APNS、APNS_SANDBOX、TENCENT、ADMで送信します。
- "target.countries"フィールドが"['KR', 'JP']"の場合、トークン国コードが"KR"または"JP"のTokenに送信します。
- "content.default"フィールドは必須で、"content"フィールドの詳細は下記[共通メッセージフォーマット]を参照してください。
- メッセージを広告タイプ、"messageType"："AD"で送信する場合、 "contact"、"removeGuide"フィールドを必ず含める必要があります。
"contact"フィールドに連絡先を入力する必要があり、"removeGuide"フィールドに受信解除方法について入力する必要があります。
- timeToLiveMinuteフィールドを設定すると、設定した時間以上に送信が遅延する場合は自動的に失敗処理されます。


### 共通メッセージ
"content"に下記の表通りにメッセージを作成すると、各プッシュタイプに合わせてメッセージが作成され、送信されます。

|Reserved Word|	Platform|	Usage|	FCM|	APNS|	TENCENT| ADM |
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
|messageDeliveryReceipt| Android, <br/>iOS, <br/> Tencent | Unnecessary | - | - | - | - |
|messageDeliveryReceiptData| Android, <br/>iOS, <br/> Tencent | Unnecessary | - | - | - | - |
|notification| Android | Optional, Object | notification | - | - | - |
|content_available| FCM(iOS) | Optional, Boolean | content_available | - | - | - |
|mutable_content| FCM(iOS) | Optional, Boolean | content_available | - | - | - |

Reserved Wordは、メッセージ作成時にPlatformごとに適切な位置に設定されます。ユーザーが任意でデータタイプや位置などを変更できません。
その他のユーザーが定義したWordは、次のようにCustom Key/Valueフィールドに入ります。

|Word|	Platform|	Usage|	FCM|	APNS|	TENCENT| ADM|
|---|---|---|---|---|---|---|
|customKey|	Android, <br/> iOS, <br/> Tencent|	Optional, <br/> Object, <br/> Array, <br/> String, <br/> Number|	data.customKey|	customKey|	custom_content.customKey| data.customKey|

### メッセージ送信例

- メッセージ送信APIのリクエスト本文(Request Body)のcontent.defaultは必須です。

#### 1. 全員に送信
登録されたすべての対象にメッセージを送信する例です。

##### Request Body
```json
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
##### Description
- target.typeを'ALL'に設定すると、すべてのトークンへメッセージを送信します。

#### 2. 特定ユーザーに送信
ユーザーIDを入力して特定ユーザーにメッセージを送信する例です。

##### Request Body
```json
{
    "target" : {
        "type" : "UID",
        "to": ["uid-01", "uid-02"]
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
##### Description
- target.typeを'UID'に設定し、target.toにユーザーIDを設定して特定ユーザーへメッセージを送信します。

#### 3. 一部の国やプッシュタイプのユーザーに送信
特定の国や端末(Android、iOS…)を使用するユーザーにのみメッセージを送信する例です。

##### Request Body
```json
{
    "target" : {
        "type" : "ALL",
        "countries": ["KR", "JP"],
        "pushTypes": ["FCM", "APNS"]
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
##### Description
- target.countriesに国コードを、target.pushTypesにプッシュタイプを設定して、条件を満たすユーザーにメッセージを送信します。

#### 4. プッシュタイプ別メッセージ変換
メッセージを送信する時、プッシュタイプごとにメッセージが変換されて送信されますが、変換されるルールを説明する例です。

##### Request Body
```json
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "title": "title",
            "body": "body",
            "badge": 1,
            "customKey": "value"
        }
    },
    "messageType" : "NOTIFICATION"
}
```

##### FCM(Android)で受信するメッセージ
```json
{
	"data": {
		"title": "title",
		"body": "body",
		"customKey": "value"
	}
}
```
##### APNS(iOS)で受信するメッセージ
```json
{
    "aps": {
        "alert": {
            "title": "title",
            "body": "body"
        },
        "badge": 1
    },
    "customKey": "value"

}
```
##### TENCENT(Android)で受信するメッセージ
```json
 {
    "title": "title",
    "body": "body",
    "custom_content": {
        "customKey": "value"
    }
}
```

##### ADM(Fire OS)で受信するメッセージ
```json
{
	"data": {
		"title": "title",
		"body": "body",
		"customKey": "value"
	}
}
```

##### Description
- contentに入力したメッセージ内容は、各プッシュタイプに合わせて変換され、送信されます。
- title、bodyなどの予約語は、プッシュタイプに合ったメッセージへ変換される時、指定された位置に設定され、送信されます。
 その他、ユーザーが定義したフィールドは、各プッシュタイプのCustom Keyの位置に設定されます。
- badge、consolidationKeyなどの特定プッシュタイプにのみ定義されている予約語は、他のプッシュタイプからは除外されます。
 例えばbadgeはAPNS(iOS)メッセージにのみ設定され、FCM、TENCENT、ADMには除外されます。

#### 5. 広告性メッセージ
広告性メッセージの送信時にメッセージへ追加される広告文言例です。

##### Request Body
```json
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "title": "金曜日特別イベント",
            "body": "今すぐご注文で50%OFF！"
        }
    },
    "messageType" : "AD",
    "contact": "1588",
    "removeGuide": "メニュー > 通知設定"
}
```

##### FCM(Android)、ko(韓国語)で受信するメッセージ
```json
 {
    "data": {
        "title": "(広告)金曜日特別イベント1588",
        "body": "今すぐご注文で50%OFF！\nメニュー > 通知設定"
    }
}
```
##### APNS(iOS)、ko(韓国語)で受信するメッセージ
```json
{
    "aps": {
        "alert": {
            "title": "(広告)金曜日特別イベント1588",
            "body": "今すぐご注文で50%OFF！\nメニュー > 通知設定"
        }
    }
}
```
##### FCM(Android)、ja(日本語)で受信するメッセージ
```json
 {
    "data": {
        "title": "金曜日特別イベント",
        "body": "今すぐご注文で50%OFF！"
    }
}
```
##### APNS(iOS), ja(日本語)で受信するメッセージ
```json
{
    "aps": {
        "alert": {
            "title": "金曜日特別イベント",
            "body": "今すぐご注文で50%OFF！"
        }
    }
}
```
##### Description
- 広告性メッセージを送信するにはmessageTypeをAD(広告)に設定し、contactとremoveGuideに代表番号と受信同意撤回方法を入力する必要があります。
- 各プッシュタイプでメッセージが送信される時、titleに広告表示文言と代表番号が、bodyに受信同意撤回方法が追加されて送信されます。
- 広告性メッセージは、言語コードが韓国語(ko, ko-)のユーザーにのみ広告文言が追加されます。上の例のように海外ユーザー(日本語)には広告文言が追加されません。

#### 6. 多言語メッセージ
多様な言語でメッセージを送信する例です。

##### Request Body
```json
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "title": "title",
            "body": "body",
            "customKey": "value"
        },
        "ko" : {
            "title": "タイトル",
            "body": "内容",
            "customKey": "'ko', 'ko-'で始まる言語コードに設定されます。"
        },
        "ja" : {
            "title": "タイトル",
            "body": "プッシュ・メッセージ"
        }
    },
    "messageType" : "NOTIFICATION"
}
```

##### FCM(Android)、ko(韓国語)で受信するメッセージ
```json
{
    "data": {
        "title": "タイトル",
        "body": "内容",
        "customKey": "'ko', 'ko-'で始まる言語コードに設定されます。"
    }
}
```
##### FCM(Android)、ko-KR(韓国語)で受信するメッセージ
```json
{
    "data": {
        "title": "タイトル",
        "body": "内容",
        "customKey": "'ko', 'ko-'で始まる言語コードに設定されます。"
    }
}
```
##### FCM(Android)、ja(日本語)で受信するメッセージ
```json
{
    "data": {
        "title": "タイトル",
        "body": "プッシュ・メッセージ",
        "customKey": "value"
    }
}
```
##### FCM(Android)、en(英語)で受信するメッセージ
```json
{
    "data": {
        "title": "title",
        "body": "body",
        "customKey": "value"
    }
}
```
##### Description
- contentの下位に各言語コードについてのメッセージを入力すると、トークンの言語コードと一致するか類似した言語のメッセージに変換されて送信されます。
 トークンの言語コードとマッチする言語コードがない場合、defaultの内容が送信されます。言語コードがen(英語)のユーザーには、conent.defaultの内容が送信されます。
- トークンの言語コードと完全に一致しなくても、言語コードの類似度を比較して最も近い言語に変換します。
 リクエスト本文にcontent.koのみ入力されていますが、言語コードがko-KR(韓国語)のユーザーにもcontent.koの内容が送信されます。
- customKeyはcontent.jaに定義されていないため、content.defaultの値で送信されます。共通する内容はcontent.defaultに入力できます。

#### 7. リッチメッセージ
メッセージ送信時、'content'に'richMessage'フィールドを定義すると、リッチメッセージでメッセージを送信できます。
共通メッセージ、広告性メッセージ、多言語メッセージと一緒に使用できます。
v1.7以上のSDKが適用された場所でのみ使用できます。

##### Request Body
```json
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "title" : "title",
            "body" : "body",
            "richMessage" : {
                "buttons" : [{
                        "name" : "ボタン名",
                        "buttonType" : "REPLY | DEEP_LINK | OPEN_APP | OPEN_URL | DISMISS",
                        "link" : "URL | ...",
                        "hint" : "メッセージを入力してください。"
                    }
                ],
                "media" : {
                    "sourceType" : "REMOTE | LOCAL",
                    "source" : "URL | LOCAL_RESOURCE",
                    "mediaType" : "IMAGE | GIF | VEDIO | AUDIO",
                    "extension" : "jpg | png",
                    "expandable" : true
                },
                "largeIcon" : {
                    "sourceType" : "REMOTE | LOCAL",
                    "source" : "URL | LOCAL_RESOURCE"
                },
                "group" : {
                    "key" : "KEY",
                    "description" : "通知集"
                }
            }
        }
    },
    "messageType" : "NOTIFICATION"
}
```

| Field | Usage | Description |
| - | - | - |
| richMessage | Optional, Object | リッチメッセージ使用時に必要 |
| richMessage.buttons | Optional, Object Array | リッチメッセージに追加されるボタン。最大3個まで可能 |
| richMessage.button.name | Required, String | ボタン名 |
| richMessage.button.buttonType | Required, String | ボタンタイプ、 REPLY, DEEP_LINK, OPEN_APP, OPEN_URL, DISMISS |
| richMessage.button.link | Required, String | ボタンを押した時に接続するリンク |
| richMessage.button.hint | Required, String | ボタンについてのヒント |
| richMessage.media | Optional, Object | リッチメッセージに追加されるメディア |
| richMessage.media.sourceType | Required, String | メディアの位置、 REMOTE、LOCAL |
| richMessage.media.source | Required, String | メディアがある場所のアドレス |
| richMessage.media.mediaType | Required、String | メディアのタイプ、 IMAGE、GIF、VEDIO、AUDIO。AndroidではIMAGEのみサポート |
| richMessage.media.extension | Required, String | メディアファイルの拡張子 |
| richMessage.media.expandable | Required, Boolean | Androidでメディアをクリックした時、広げる機能を使用するかどうか |
| richMessage.largeIcon | Optional, Object | リッチメッセージに追加される大アイコン。Androidでのみサポート |
| richMessage.largeIcon.sourceType | Required, String | 大アイコンの位置、 REMOTE、LOCAL |
| richMessage.largeIcon.source | Required, String | メディアがある場所のアドレス |
| richMessage.group | Optional, Object | 複数のメッセージをグループ単位にまとめる機能。Androidでのみサポート |
| richMessage.group.key | Required, String | グループのキー |
| richMessage.group | Required, String | グループの説明 |

#### 8. FCM notificationフィールドを使用したメッセージ
メッセージ送信時、'content'に'notification'フィールドを定義すると、FCM形式に合わせてメッセージを送信できます。

##### Request Body
```
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "notification" : {
                "title" : "タイトル",
                "body" : "内容",
                "android_channel_id": "Androidチャンネル",
                "sound" : "音",
                "click_action" : "メッセージクリック時のアクション",
                "title_loc_key" : "文字列キー",
                "title_loc_args" : [
                    "文字列"
                ],
                "body_loc_key" : "文字列キー",
                "body_loc_args" : [
                    "文字列"
                ],
                "icon" : "notification_off",
                "color" : "#4286f4",
                "tag" : "tag"
            }
        }
    },
    "messageType" : "NOTIFICATION"
}
```

| Reserved Word | Platform | Usage | FCM | Description | 
| - | - | - | - | - | 
| notification | Android | Optional, Object | notification | |
| notification.title | Android | Optiontal, String | notification.title | notification.titleを設定するとdata.titleは無視されます。 |
| notification.body | Android | Optiontal, String | notification.body | notification.body設定するとdata.bodyは無視されます。 |
| notification.sound | Android | Optiontal, String | notification.sound | メッセージ受信時に再生される通知音です。 Android Oreo (8.0)以上で動作しません。 |
| notification.clickAction | Android | Optiontal, String | notification.click_action | メッセージをクリックした時に移動するActivityです。 |
| notification.titleLocKey | Android | Optiontal, String | notification.title_loc_key | 文字列リソースです。メッセージをローカライズするのに使用できます。 |
| notification.titleLocArgs | Android | Optiontal, String | notification.title_loc_args | 文字列の日本語識別子に入る文字列です。メッセージをローカライズするのに使用できます。 |
| notification.bodyLocKey | Android | Optiontal, String | notification.body_loc_key | 文字列リソースです。メッセージをローカライズするのに使用できます。 |
| notification.bodyLocArgs | Android | Optiontal, String Array | notification_body_loc_args | 文字列の日本語識別子に入る文字列です。メッセージをローカライズするのに使用できます。 |
| notification.icon | Android | Optiontal, String | notification.icon | 通知アイコンです。 |
| notification.color | Android | Optiontal, String | notification.color | 通知アイコンの色です。 |
| notification.tag | Android | Optiontal, String | notification.tag | 同じタグのメッセージを受信すると、既存メッセージを代替します。 |
| notification.badge | Android, iOS | Optiontal, Number | notification.badge | 確認していない、新しい通知の数です。 |

##### FCM(Android)で受信するメッセージ
```json
{
    "registration_ids" : [
        "トークン"
    ],
    "notification" : {
        "title" : "タイトル",
        "body" : "内容",
        "android_channel_id": "Androidチャンネル",
        "sound" : "音",
        "click_action" : "メッセージクリック時のアクション",
        "title_loc_key" : "文字列キー",
        "title_loc_args" : [
            "文字列"
        ],
        "body_loc_key" : "文字列キー",
        "body_loc_args" : [
            "文字列"
        ],
        "icon" : "notification_off",
        "color" : "#4286f4",
        "tag" : "tag"
    }
}
```



### 照会
#### リスト照会
※コンソールで送信したプッシュメッセージのみリスト照会APIで照会できます。 APIで送信したプッシュメッセージはLogging機能を有効にしてからログ照会APIを利用してください。
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/messages?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&deliveryType={deliveryType}&messageStatus={messageStatus}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |
| pageIndex | Optional, Number | 基本値0 |
| pageSize | Optional, Number | 基本値25、最大値100 |
| from | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| deliveryType | Optional, String | 'INSTANT'(即時送信), 'RESERVATION'(予約送信) |
| messageStatus | Optional, String | 'READY', 'PROCESSING', 'COMPLETE', 'CANCEL_NO_TARGET', 'CANCEL_INVALID_CERTIFICATE', 'CANCEL_INVALID_MESSAGE', 'CANCEL_UNSUPPORTED_MESSAGE_TYPE', 'CANCEL_UNAUTHORIZED', 'CANCEL_UNKNOWN' |

##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/messages' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" : true,
        "resultCode": 0,
        "resultMessage" : "SUCCESS"
    },
    "messages" : [{
        "messageId" : 0,
        "messageIdString": "0",
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
        "contact": "1588-1588",
        "removeGuide": "メニュー > 設定",
        "timeToLiveMinute": 60,
        "createdDateTime": "2017-02-13T09:30:00.000+09:00",
        "completedDateTime": "2017-02-13T09:30:00.000+09:00",
        "targetCount": 1000,
		"sentCount": 1000,
        "messageStatus": "COMPLETE",
        "provisionedResourceId": "[a-zA-Z0-9]{16}"
    }],
    "toatalCount": 1
}
```


| Field | Usage | Description |
| - | - | - |
| createdDateTime | - | メッセージが作成された日時(ISO 8601) |
| completedDateTime | - | メッセージ送信が完了した日時(ISO 8601) |
| targetCount | - | 送信されるターゲットトークン数 |
| sentCount | - | 実際に送信されたトークン数 |
| provisionedResourceId | - | メッセージが送信された専用リソースID |
| totalCount | - | フィルタリングされたメッセージ総数 |

- "messageStatus"フィールドは、メッセージ状態を表します。次のような状態があります。
    - READY：メッセージ送信リクエストが登録された状態です。
    - PROCESSING：メッセージ作成が完了し、待機または送信中です。
- COMPLETE：メッセージの送信が完了した状態です。
- CANCEL_NO_TARGET：メッセージ送信対象が存在しないためキャンセルされた状態です。次のような理由で送信がキャンセルされることがあります。
 登録されたトークンがない時
 広告プッシュメッセージの場合、受信に同意したユーザーがいない時
 夜間広告プッシュメッセージ(21時～8時)の場合、夜間広告受信に同意したユーザーがいない時
 登録されたトークンが削除され、トークンがない時
    - CANCEL_INVALID_CERTIFICATE：証明書が無効でキャンセルされた状態です。証明書の状態を確認する必要があります。
    - CANCEL_INVALID_MESSAGE：メッセージ形式が合っておらずキャンセルされた状態。
    - CANCEL_UNSUPPORTED_MESSAGE_TYPE：メッセージ形式が合っておらずキャンセルされた状態です。
    - CANCEL_UNAUTHORIZED：証明書認証プロセスで失敗した状態です。証明書の状態を確認する必要があります。
    - CANCEL_UNKNOWN：内部エラーが発生した状態です。

#### 単件照会
※コンソールで送信したプッシュメッセージのみ、単件照会APIで照会できます。APIで送信したプッシュメッセージはLogging機能を有効にしてからログ照会APIを利用してください。
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/messages/{message-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |
| messageId | Required, Number | メッセージID |

##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/messages/'"${MESSAGE_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "message" : {
        "messageId" : 0,
        "messageIdString": "0",
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
        "contact": "1588-1588",
        "removeGuide": "メニュー > 設定",
        "timeToLiveMinute": 60,
        "createdDateTime": "2017-02-13T09:30:00.000+09:00",
        "completedDateTime": "2017-02-13T09:30:00.000+09:00",
        "targetCount": 1000,
        "messageStatus": "COMPLETE",
        "provisionedResourceId": "[a-zA-Z0-9]{16}"
    },
    "header" : {
        "isSuccessful" : true,
        "resultCode": 0,
        "resultMessage" : "SUCCESS"
    }
}
```


#### 失敗したメッセージリスト照会
送信に失敗したメッセージを照会できます。
ただし、トークンが存在しない場合(INVALID_TOKEN)は送信失敗と判断しません。

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/message-errors?messageId={messageId}&messageErrorType={messageErrorType}&messagErrorCause={messageErrorCause}&from={from}&to={to}&limit={limit}
Header
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |
| messageId | Optional, Number | メッセージID |
| messageErrorType | Optional, String | 'CLIENT_ERROR', 'EXTERNAL_ERROR', 'INTERNAL_ERROR' |
| messageErrorCause | Optional, String | 'UNSUPPORTED_MESSAGE_TYPE', 'INVALID_MESSAGE', 'INVALID_CERTIFICATE', 'UNAUTHORIZED', 'EXPIRED_TIME_OUT', 'APNS_ERROR', 'FCM_ERROR', 'TENCENT_ERROR', 'AGENT_ERROR', 'ADM_ERROR', 'DUPLICATED_MESSAGE_TOKEN'  |
| from | Optional, DateTime String | 過去30日まで、デフォルト値は過去7日前(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 過去30日まで、デフォルト値は現在(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| limit | Optional, Number | 一度に照会するリストサイズ、デフォルト値と最大値は100 |
| pageNumber | Optional, Number | ページ番号、デフォルト値1 |

##### Description
- messageErrorTypeとmessageErrorCauseは、次のような意味があります。
    - CLIENT_ERROR：クライアントの無効なリクエスト
        - UNSUPPORTED_MESSAGE_TYPE：サポートしないメッセージタイプ
        - INVALID_MESSAGE：正常ではないメッセージ
        - INVALID_CERTIFICATE：証明書満了または証明書情報が不正
        - UNAUTHORIZED：証明書満了または証明書情報が不正
        - DUPLICATED_MESSAGE_TOKEN：該当トークンに同じメッセージの重複送信がリクエストされて失敗
    - EXTERNAL_ERROR：APNS、FCM、Tencent、ADMなどプッシュと接続した外部サービスのエラー
        - APNS_ERROR：APNS(iOS)に送信失敗
        - FCM_ERROR：FCM(Google)に送信失敗
        - TENCENT_ERROR：Tencentに送信失敗
        - ADM_ERROR：ADMに送信失敗
    - INTERNAL_ERROR：プッシュ内部で発生したエラー
        - EXPIRED_TIME_OUT：送信遅延によるメッセージの有効時間切れ
        - AGENT_ERROR：Agent内部エラーによる送信失敗
- Response Bodyでheader.resultCodeが40010の場合、照会期間(from, to)を狭めて照会する必要があります。

##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/message-errors' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```


##### Response Body
```
{
	"messageErrors" : [{
			"messageId" : 0,
			"messageIdString" : "0",
			"pushType" : "FCM",
			"messageErrorType" : "ClientError",
			"messageErrorCause" : "INVALID_CERTIFICATE",
			"payload" : {
				"data" : {
					"title" : "title",
					"body" : "body"
				}
			},
			"createdDateTime" : "2017-05-18T15:47:00.000+09:00",
			"tokens" : [{
					"uid" : "uid-1",
					"token" : "token-1"
				}
			]
		}
	],
	"header" : {
		"isSuccessful" : true,
		"resultCode" : 0,
		"resultMessage" : "Success."
	}
}
```
```
{
    "messageErrors" : [{
        ...
    }],
	"header" : {
		"resultCode" :  40010,
		"resultMessage" :  "Client Error. It's too many. Please, change 'from' and 'to' shortly. totalCount<10>",
		"isSuccessful" :  false
	}
}
```
| Field | Usage | Description |
| - | - | - |
| messageId | - | 失敗したメッセージID |
| messageIdString | - | 失敗したメッセージID |
| pushType | - | 'FCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
| payload | - | 端末に送信された実際のメッセージ内容 |
| tokens | - | 送信に失敗した受信者のuidとtoken |


### ログ照会
- ログ照会APIは、Logging機能を有効にした状態でのみ呼び出せます。
- Logging機能は、[Console] > [Notification] > [Push] > [Setting]タブで有効にできます。

#### 一般ログ照会
- 最大100個まで照会可能です。

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/logs/message?messageId={messageId}&uid={uid}&token={token}&pushType={pusyType}&from={from}&to={to}&limit={limit}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |
| messageId | Optional, Number | メッセージID |
| uid | Optional, String | ユーザーID |
| token | Optional, String | ユーザートークン |
| pushType | Optional, String | プッシュタイプ |
| from | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| limit | Optional, Number | 最大照会数、基本値100 |
| pageNumber | Optional, Number | ページ番号、デフォルト値1 |

##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/logs/message?limit=10' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body

```
{
    "header" : {
        "resultCode" : 0,
        "resultMessage" : "success",
        "isSuccessful" : true
    },
    "data" : {
        "count" : 0,
        "logs" : [{
                "logType" : "message-result",
                "logSource" : "tc-push",
                "messageId" : "1",
                "body" : "{\"tokens\":[{\"uid\":\"gimbimloki\",\"token\":\"1\"}],\"payload\":{\"aps\":{\"alert\":{\"title\":\"title\",\"body\":\"body\"},\"mutable-content\":1}}}",
                "logTime" : "1",
                "pushType" : "APNS",
                "sendTime" : "1",
								"searchKey1": "1746041784729856",
						    "searchKey2": "FCM",
						    "searchKey3": "SENT",
                "sentResult" : "SENT",
                "host" : "127.0.0.1",
                "appkey" : "APP_KEY",
                "logVersion" : "v2",
                "isNeedStored" : "bulk",
                "projectName" : "L&CS_APP_KEY",
                "SinkVersion" : "-",
                "projectVersion" : "v2.2"
            }
        ]
    }
}
```

## 予約メッセージ

### 作成
#### 予約メッセージ送信スケジュールの作成
##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/schedules
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |


##### Request Body

```json
{
	"type" : "EVERY_MONTH",
	"fromDate" : "2016-12-30",
	"toDate" : "2017-01-02",
	"times" : [
		"12:00",
		"17:00"
	],
	"days" : [
		1,
		15
    ],
 	"daysOfWeek": [
 		"SUNDAY",
 		"MONDAY"
  	]
}
```

| Field | Usage | Description |
| - | - | - |
| type | Required、String | 'EVERY_DAY' (毎日)、'EVERY_WEEK' (毎週)、'EVERY_MONTH' (毎月) |
| fromDate | Required, Date String | 予約メッセージ開始年月日(YYYY-MM-DD) |
| toDate | Required, Date String | 予約メッセージ終了年月日(YYYY-MM-DD) |
| times | Required, Time String | 予約メッセージ送信時間/分(hh:mm) |
| days | Optional, Number Array | typeが'EVERY_MONTH'の時に設定します。 (1、2、...、31: 1日、2日、 …、31日) |
| daysOfWeek | Optional, String Array | typeが'EVERY_WEEK'の時に設定します。 ('SUNDAY', 'MONDAY', 'TUESDAY', 'WEDNESDAY', 'THURSDAY', 'FRIDAY', 'SATURDAY') |

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/schedules' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
	"type": "EVERY_MONTH",
	"fromDate": "2016-12-30",
	"toDate": "2017-01-02",
	"times": ["12:00", "17:00"],
	"days": [1, 15],
	"daysOfWeek": ["SUNDAY", "MONDAY"]
}'
```

##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"schedules" : [
		"2016-12-01T12:00",
		"2016-12-01T17:00",
		"2016-12-15T12:00",
		"2016-12-15T17:00",
		"2017-01-01T12:00",
		"2017-01-01T17:00",
		"2017-01-15T12:00",
		"2017-01-15T17:00",
		"2017-02-01T12:00",
		"2017-02-01T17:00",
		"2017-02-15T12:00",
		"2017-02-15T17:00"
	]
}
```

| Field | Usage | Description |
| - | - | - |
| schedules | - | 日時(ISO 8601, e.g. YYYY-MM-DDThh:mm) |


#### 予約メッセージ作成
##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/reservations
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |

##### Request Body
```json
{
	"schedules" : [
		"2016-12-30T12:40",
		"2016-12-31T12:40"
	],
	"isLocalTime" : false,
	"target" : {
        "type" : "UID",
        "to": ["uid-1", "uid-2"]
    },
    "content" : {
        "default" : {
            "title": "title",
            "body": "body"
        }
    },
    "messageType" : "AD",
    "contact": "1588-1588",
    "removeGuide": "メニュー > 設定",
    "timeToLiveMinute": 1,
	"provisionedResourceId": "id"
}
```

| Field | Usage | Description |
| - | - | - |
| schedules | Required, DateTime String Array | 予約メッセージ送信スケジュールリスト |
| isLocalTime | Required, Boolean | 現地時間で送信するかどうか |

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/reservations' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "schedules": ["2020-12-30T12:40", "2020-12-31T12:40"],
    "isLocalTime": false,
    "target": {
        "type": "UID",
        "to": ["uid"]
    },
    "content": {
        "default": {
            "title": "title",
            "body": "body"
        }
    },
    "messageType": "NOTIFICATION"
}'
```

##### Response Body

```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"reservation" : {
		"reservationId": 666810348995587,
		"reservationIdString": "666810348995587"
	}
}
```

| Field | Usage | Description |
| - | - | - |
| reservationId | Number | 予約メッセージID |
| reservationIdString | String | 予約メッセージID文字列 |


### 照会
#### リスト照会
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/reservations?pageIndex={pageIndex}&pageSize={pageSize}&reservationStatus={reservationsStatus}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |
| pageIndex | Optional, Number | 基本値0 |
| pageSize | Optional, Number | 基本値25、最大値100 |
| from | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| deliveryFrom | Optional, DateTime String | 送信スケジュール日開始値(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD)。照会された予約内部の送信スケジュールリストはフィルタなしで表示。 |
| deliveryTo | Optional, DateTime String | 送信スケジュール日終了値(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD)。照会された予約内部の送信スケジュールリストはフィルタなしで表示。 |
| reservationStatus | Optional, String | 'RESERVED', 'COMPLETE' |

##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/reservations' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body

```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"reservations" : [{
			"reservationId" : 666810348995587,
			"reservationIdString" : "666810348995587",
			"schedules" : [{
					"scheduleId" : 2455708,
                    "scheduleIdString" : "2455708",
                    "reservationId" : 666810348995587,
                    "reservationIdString" : "666810348995587",
					"deliveryDateTime" : "2016-12-30T12:40:00.000+09:00",
					"timezoneOffset" : 0,
					"scheduleStatus" : "READY"
				}
			],
			"isLocalTime" : false,
			"target" : {
				"type" : "UID",
				"to" : [
					"uid"
				]
			},
			"content" : {
				"default" : {
					"title" : "default title",
					"body" : "default body"
				},
				"ko" : {
					"title" : "韓国語タイトル",
					"body" : "韓国語内容"
				}
			},
			"messageType" : "NOTIFICATION",
			"timeToLiveMinute" : 60,
			"createdDateTime" : "2016-12-30T10:34:40.000+09:00",
			"updatedDateTime" : "2016-12-30T10:34:40.000+09:00",
			"completedDateTime" : "2016-12-30T10:34:40.000+09:00",
			"reservationStatus" : "RESERVED"
		}
	],
    "totalCount" : 1
}

```

| Field | Usage | Description |
| - | - | - |
| reservationIdString | - | 予約メッセージID文字列 |
| createdDateTime | - | 予約メッセージ登録日時(ISO 8601) |
| updatedDateTime | - | 予約メッセージ修正日時(ISO 8601) |
| completedDateTime | - | 予約メッセージ送信完了日時。完了していなければ現在時間を表示(ISO 8601) |
| reservationStatus | - | 'RESERVED', 'COMPLETED' |
| schedules.scheduleId | - | 予約メッセージ送信スケジュールID |
| schedules.scheduleIdString | - | 予約メッセージ送信スケジュールID文字列 |
| schedules.reservationIdString | - | 予約メッセージ送信スケジュールが属する予約メッセージID文字列 |
| schedules.deliveryDateTime | - | 予約メッセージ送信日時 |
| schedules.timezoneOffset | - | 予約メッセージ送信タイムゾーン。現地時間で送信時に設定 |
| schedules.scheduleStatus | - | 'READY'、'SENDING'、'CANCELED'、'DONE'予約メッセージ送信スケジュール状態 |
| totalCount | - | 登録された予約メッセージ総数 |


#### 単件照会
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/reservations/{reservation-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/reservations/'"${RESERVATION_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

#### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"reservation" : {
		"reservationId" : 666810348995587,
		"reservationIdString" : "666810348995587",
		"schedules" : [{
                "scheduleId" : 2455708,
                "scheduleIdString" : "2455708",
                "reservationId" : 666810348995587,
                "reservationIdString" : "666810348995587",
				"deliveryDateTime" : "2016-12-30T12:40:00.000+09:00",
				"timezoneOffset" : 0,
				"scheduleStatus" : "READY"
			}
		],
		"isLocalTime" : false,
		"target" : {
			"type" : "UID",
			"to" : [
				"uid"
			]
		},
		"content" : {
			"default" : {
				"title" : "default title",
				"body" : "default body"
			},
			"ko" : {
				"title" : "韓国語タイトル",
				"body" : "韓国語内容"
			}
		},
		"messageType" : "NOTIFICATION",
		"timeToLiveMinute" : 60,
		"createdDateTime" : "2016-12-30T10:34:40.000+09:00",
		"updatedDateTime" : "2016-12-30T10:34:40.000+09:00",
		"completedDateTime" : "2016-12-30T10:34:40.000+09:00",
		"reservationStatus" : "RESERVED"
	}
}
```

| Field | Usage | Description |
| - | - | - |
| updatedDateTime | DateTime String | 予約修正日時(ISO 8601) |


#### 送信された予約メッセージ照会
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/reservations/{reservation-id}/messages?pageIndex={pageIndex}&pageSize={pageSize}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |
| reservationId | Required, Number | 予約メッセージID |
| pageIndex | Optional, Number | 基本値0 |
| pageSize | Optional, Number | 基本値25、最大値100 |

##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/reservations/'"${RESERVATION_ID}"'/messages' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"messages" : [{
			"messageId" : 356125922591162,
			"messageIdString" : "356125922591162",
			"target" : {
				"type" : "ALL",
				"pushTypes" : ["FCM", "APNS", "APNS_SANDBOX", "TENCENT", "ADM"]
			},
			"content" : {
				"default" : {
					"title" : "6時55分予約メッセージ",
					"body" : "API v2"
				}
			},
			"messageType" : "NOTIFICATION",
			"createdDateTime" : "2017-04-05T18:55:00.000+09:00",
			"completedDateTime" : "2017-04-05T18:55:00.000+09:00",
			"targetCount" : 38,
			"sentCount" : 38,
			"messageStatus" : "COMPLETE"
		}
	],
	"totalCount" : 1
}
```

| Field | Usage | Description |
| - | - | - |
| totalCount | - | 送信されたメッセージ総数 |


### 修正
#### 予約メッセージの修正
##### Method, URL, Headers
```
PUT /push/v2.4/appkeys/{appkey}/reservations/{reservationId}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

##### Request Body
```json
{
	"schedules" : [
		"2016-12-30T10:05",
		"2016-12-31T12:40"
	],
	"target" : {
		"type" : "UID",
		"to" : [
			"uid"
		]
	},
	"content" : {
		"default" : {
			"title" : "default title",
			"body" : "default body"
		},
		"ko" : {
			"title" : "韓国語タイトル",
			"body" : "韓国語内容"
		}
	},
	"isLocalTime" : false,
	"messageType" : "NOTIFICATION"
}
```

##### cURL
```
curl -X PUT \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/reservations/'"${RESERVATION_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "schedules": ["2020-12-30T10:05", "2020-12-31T12:40"],
    "target": {
        "type": "UID",
        "to": ["uid"]
    },
    "content": {
        "default": {
            "title": "default title",
            "body": "default body"
        },
        "ko": {
            "title": "한국어 제목",
            "body": "한국어 내용"
        }
    },
    "isLocalTime": false,
    "messageType": "NOTIFICATION"
}'
```

##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	}
}
```


### 削除
#### 予約メッセージの削除
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/reservations?reservationIds={reservationId,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |
| reservationIds | Required, Number Array | ','で区切る、 e.g. reservationIds=1,2 |

##### Request Body
```
なし
```

##### cURL
```
curl -X DELETE \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/reservations?reservationIds='"${RESERVATION_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	}
}
```


## タグ

### 作成
#### タグの作成
##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/tags
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body

```json
{
    "tagName" :  "30"
}
```

| Field | Usage | Description |
| - | - | - |
| tagName | Required, String | タグ名、最長255、空白(Space)文字不可 |

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "tagName" :  "30"
}'
```


##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "tag" : {
        "tagId" :  "12345678"
    }
}
```

| Field | Usage | Description |
| - | - | - |
| tagId | Required, String | 作成されたタグID。長さ8 |

##### Description
- タグは最大2,048個まで作成できます。


#### タグにUID追加作成
- タグにUIDを追加(append)すること。既存のUIDを追加するとUIDのタグは増えます。
- 1つのUIDの最大タグ数は16個。
##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/tags/{tag-id}/uids
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```json
{
    "uids" : [
         "uid-01",
         "uid-02"
    ]
}
```
| Field | Usage | Description |
| - | - | - |
| uids | Required, String Array | UID配列。最長16、UID最長64 |

##### cURL
```
curl -X POST \
https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags/'"${TAG_ID}"'/uids' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "uids" : [
         "uid-01",
         "uid-02"
    ]
}'
```

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags/'"${TAG_ID}"'/uids' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "uids" : [
         "uid-01",
         "uid-02"
    ]
}'
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```


#### UIDにタグリスト設定
- UIDのタグを交換(replace)することです。既に設定されているタグは削除され、新しいタグに設定されます。
##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/uids
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```json
{
    "uid" :  "uid-01",
    "tagIds" : [
         "12345678",
         "23456789"
    ]
}
```

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/uids' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "uid" :  "uid-01",
    "tagIds" : [
         "12345678",
         "23456789"
    ]
}'
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

### 照会
#### タグリスト照会
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/tags?tagName={tagName}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| tagName | Optional, String | タグ名 |

##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```


##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "tags" : [
        {
            "tagId" :  "12345678",
            "tagName" :  "tagName",
            "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
            "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
        }
    ]
}
```

| Field | Usage | Description |
| - | - | - |
| createdDateTime | Required, Date Time String | 作成日時(ISO 8601) |
| updatedDateTime | Required, Date Time String | 修正日時(ISO 8601) |

#### タグ単件照会
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags/'"${TAG_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "tag" : {
        "tagId" :  "12345678",
        "tagName" :  "30",
        "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
        "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
    }
}
```

#### タグのUIDリスト照会
- タグがついているUIDリストを照会します。

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/tags/{tag-id}/uids?offsetUid={uid}&limit={limit}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| offsetUid | Optional, String | 設定されたUIDの次から照会 |
| limit | Optional, Number | 照会するUID数 |

##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags/'"${TAG_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "uids" : [
        {
            "uid" :  "uid-01",
            "tags" : [
                {
                    "tagId" :  "tag-id-01",
                    "tagName" :  "tag-name-01",
                    "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
                    "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
                }
            ],
            "contacts" : [
                {   
                    "contactType" :  "TOKEN_FCM",
                    "contact" :  "token",
                    "createdDateTime" :  "2017-07-07T07:07:07.777+09:00"
                }
            ]
        }
    ]
}
```

| Field | Usage | Description |
| - | - | - |
| contacts | -, Object Array | UIDの連絡先。トークン情報リスト |
| contactType | -, String | トークンタイプ、 'TOKEN_FCM', 'TOKEN_APNS', 'TOKEN_APNS_SANDBOX', 'TOKEN_TENCENT', 'TOKEN_ADM' |
| contact | -, String | トークン |
| createdDateTime | Required, Date Time String | 作成日時(ISO 8601) |


#### UID照会
- UIDを照会します。
- トークン登録時、Contact(連絡先)が登録されます。
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/uids/{uid}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/uids/'"${USER_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
"header" : {
"isSuccessful" :  true,
"resultCode" :  0,
"resultMessage" :  "SUCCESS"
},
"uid" : {
    "uid" :  "uid-01",
        "tags" : [
            {
                "tagId" :  "12345678",
                "tagName" :  "tag-name-01",
                "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
                "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
            }
            ],
            "contacts" : [
            {
                "contactType" :  "TOKEN_FCM",
                "contact" :  "token",
                "createdDateTime" :  "2017-07-07T07:07:07.777+09:00"
            }
        ]
    }
}
```


### 修正
#### タグの修正
##### Method, URL, Headers
```
PUT /push/v2.4/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```json
{
    "tagName" :  "30代"
}
```

##### cURL
```
curl -X PUT \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags/'"${TAG_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "tagName": "30代"
}'
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```


### 削除
#### タグの削除
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
なし
```

##### cURL
```
curl -X DELETE \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags/'"${TAG_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```


#### UID削除
- UIDを削除するとContact、Tokenも一緒に削除されます。
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/uids?uids={uid,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| uids | -, Object Array | 削除するUIDリスト。カンマ(,)で区切ります。一度に16個まで削除できます。 |

##### Request Body
```
なし
```

##### cURL
```
curl -X DELETE \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/uids?uids='"${USER_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```


#### タグのUID削除
- TagとUIDの関係のみ削除します。
- Contact、Tokenは削除されません。
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/tags/{tagId}/uids?uids={uid,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
なし
```

##### cURL
```
curl -X DELETE \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags/'"${TAG_ID}"'/uids?uids='"${USER_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

## UID

### 作成

#### タグの追加
- UIDにタグIDでタグを追加します。
- Secret Keyが必要ない。アプリで呼び出し可能です。
##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```json
{
    "tagIds": ["TAG_ID01"]
}
```

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/uids/'"${USER_ID}"'/tag-ids' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "tagIds": ["12345678"]
}'
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```


### 照会

#### UIDのタグID照会
- UIDのタグIDを照会します。
- Secret Keyが必要ない。アプリで呼び出し可能です。
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```
なし
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/uids/'"${USER_ID}"'/tag-ids' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "tagIds": ["TAG_ID01"]
}
```


### 修正
#### UIDのタグ修正
- UIDにタグIDでタグを修正します。
- Secret Keyが必要ない。アプリで呼び出し可能です。
##### Method, URL, Headers
```
PUT /push/v2.4/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```json
{
    "tagIds": ["TAG_ID02"]
}
```

##### cURL
```
curl -X PUT \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/uids/'"${USER_ID}"'/tag-ids' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
	"tagIds": ["12345678"]
}'
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" : "SUCCESS"
    }
}
```


### タグの削除
- UIDのタグIDを照会します。
- Secret Keyが必要ない。アプリで呼び出し可能です。
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/uids/{uid}/tag-ids?tagIds={tagId,}
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```
なし
```

##### cURL
```
curl -X DELETE \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/uids/'"${USER_ID}"'/tag-ids?tagIds='"${TAG_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" : "SUCCESS"
    }
}
```

| Field | Usage | Description |
| - | - | - |
| tagIds | Required, String Array | Query String。削除するタグID。カンマ(,)で区切る |


<span id="stats-api"></span>
## 統計
### 統計照会
- 統計イベントキーを基準に統計を照会できます。

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/stats?eventCategory={eventCategory}&statisticsType={statisticsType}&timeUnit={timeUnit}&from={from}&to={to}&extra1s={extra1,}&messageId={messageId}&statsIds={statsId,}
Content-Type: application/json;charset=UTF-8
```
| Field | Usage | Description |
| - | - | - |
| eventCategory | Required、String | イベントのカテゴリー。MESSAGE、TOKEN_REGISTRATION、TOKEN_LANGUAGE、TOKEN_COUNTRY、TOKEN_AGREEMENT |
| statisticsType | Optional、String | 検索された統計データの表現形式。 NORMAL(デフォルト値)、MINUTELY、HOURLY、DAILY、BY_DAY |
| timeUnit | Optional、String | 統計データの時間単位。デフォルト値は照会期間に応じて決定、 MINUTES、HOURS、DAYS |
| from | Optional、DateTime String | 過去30日まで(ISO 8601、e.g. YYYY-MM-DDThh:mm:ss.SSSTZD、2018-04-24T06:00:00.000%2B09:00) |
| to | Optional、DateTime String | 過去30日まで(ISO 8601、e.g. YYYY-MM-DDThh:mm:ss.SSSTZD、2018-04-24T06:00:00.000%2B09:00) |
| extra1s | Optional、String Array | eventCategoryがMESSAGEの場合、プッシュタイプでフィルタリング可能。 FCM、APNS、APNS_SANDBOX、APNS_VOIP、APNS_SANDBOXVOIP、ADM、TENCENT |
| messageId | Optional, String | メッセージID |
| statsIds | Optional, String Array | 統計イベントキーID |

##### cURL
```
curl -X GET \
https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/stats?eventCategory='"${EVENT_CATEGORY}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```
{
	"header": {
		"resultCode": 0,
		"resultMessage": "success",
		"isSuccessful": true
	},
	"stats": [{
			"eventDateTime": "2020-08-12T00:00:00.000+09:00",
			"events": {
				"RECEIVED": 0,
				"SENT_FAILED": 0,
				"SENT": 0,
				"OPENED": 0
			}
		}
	]
}
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/stats?eventCategory='"${EVENT_CATEGORY}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```
{
	"header": {
		"resultCode": 0,
		"resultMessage": "success",
		"isSuccessful": true
	},
	"stats": [{
			"eventDateTime": "2020-08-12T00:00:00.000+09:00",
			"events": {
				"RECEIVED": 0,
				"SENT_FAILED": 0,
				"SENT": 0,
				"OPENED": 0
			}
		}
	]
}
```

* *文書修正履歴*
    * *(2020.03.24)統計API追加*
