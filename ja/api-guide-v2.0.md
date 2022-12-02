## Notification > Push > APIガイド

### v2.0 API紹介

#### 追加
- 詳細なresultMessageを返します。APIの呼び出しに失敗した時は、問題になるフィールドや値を返します。
    - 例：無効なメッセージIDで照会した時、次のようにmessageIdフィールドと値がresultMessageに含まれます。
```
{
    "header" : {
		"resultCode" : 40401,
		"resultMessage" : "Client Error. Not found. messageId<3496615188236841>",
		"isSuccessful" : false
    }
}
```
- v2.0トークン登録、プロパティ統計APIが追加されました。
- v2.0メッセージ照会APIで、期間(from, to)とメッセージ状態(messageStatus)で照会できます。 From, toの形式はISO 8601(YYYY-MM-DDThh:mm:ss.SSSTZD)形式に従います。
  '2018-04-11T18:39:04.000+09:00'のプラス(+)文字はURLエンコード後、'2018-04-11T18:39:04.000%2B09:00'と入力する必要があります。
- v2.0メッセージ照会APIで登録日時(createdDateTime)、完了日時(completedDateTime)フィールドが追加されました。
- v2.0メッセージ受信、確認統計照会APIが追加されました。
- v2.0有効ではないトークンAPIで、ページング(PageIndex, PageSize)、期間(from, to)、メッセージIDで照会できます。

#### 修正
- v1.3 Uidでトークン照会APIのURI '/push/v1.3/appkey/{appkey}/uids/{uid}/tokens'から'/push/v2.0/appkeys/{appkey}/tokens?uid={uid}'に変更されました。
- v1.3フィードバックAPIのURIが'/push/v1.3/appkey/{appkey}/feedback'から'/push/v2.0/appkeys/{appkey}/invalid-tokens'に変更されました。

#### 削除
- v2.0メッセージ送信APIで送信されたメッセージは、送信履歴を残しません。コンソールから送信するメッセージは履歴を残します。
  2017年6月に追加予定の'Log&Crash Search'連携機能が追加されると、ユーザーの'Log&Crash Search'にメッセージ送信履歴を残すことができます。
- v2.0 APIでチャンネル(Channel)が削除されました。チャンネル機能はトークンのグループ化を担当していた機能で、1つのトークンは1つのチャンネルにのみ属すことができる制限がありました。
  2017年6月追加予定のタグ機能に替わる予定です。タグは1つのトークンに複数のタグを追加できます。
    - トークン登録API、channelフィールドの削除
    - メッセージ送信API、target.typeから'CHANNEL'タイプの削除
    - チャンネルAPIの削除

### 基本情報
#### Endpoint
```
API Endpoint: https://api-push.cloud.toast.com
メッセージ受信/確認したかどうかを収集Endpoint：https://collector-push.cloud.toast.com
```

#### Secret Key
- コンソールで確認できます。
- Secret Keyが必要なAPIを呼び出す時、ヘッダに下記のように設定して呼び出す必要があります。
```
Header
X-Secret-Key: [a-zA-Z0-9]{8}
```

コンソールで**Notification > Push**をクリックして、右下にある**URL & AppKey**をクリックすると作成できます。

#### Response

##### Response HTTP Status Code
200 OK.  
すべてのAPIリクエストに対して200 OKレスポンスを返します。
詳細なレスポンス結果は、Response BodyのHeaderで確認できます。

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
##### resultCode, resultMessage

| isSuccessful | resultCode    | resultMessage                            |
| ------------ | ------------- | ---------------------------------------- |
| true         | 0             | SUCCESS                                  |
| false        | 40001         | Client Error. Parameter is invalid.      |
| false        | 40002         | Client Error. Parameter is invalid format. |
| false        | 40003         | Client Error. Parameter is empty or null. |
| false        | 40004         | Client Error. Duplicate certificate.     |
| false        | 40005         | Client Error. Expired certificate.       |
| false        | 40006         | Client Error. Already registered.        |
| false        | 40007         | Client Error. Maximum limit exceeded.    |
| false        | 40008         | Client Error. Already completed.         |
| false        | 40101         | Client Error. Access is not allowed.     |
| false        | 40102         | Client Error. Unavailable key.           |
| false        | 40401         | Client Error. Not found.                 |
| false        | 50001 ～ 50501 | Internal Error. Please report this. 'http://cloud.toast.com/support/faq'. |
| false        | 400           | Client Error。タグAPIで発生したクライアントエラー。     |
| false        | 500           | Internal Error。タグAPIで発生した内部エラー。      |

## トークン
### 作成
- クライアントで確認できます。

##### Method、URL
```
POST /push/v2.0/appkeys/{appkey}/tokens
Content-Type: application/json;charset=UTF-8
```

| Field  | Usage            | Description                     |
| ------ | ---------------- | ------------------------------- |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |

##### Request Body

```
{
  "oldToken": "oldToken",
  "token": "token",
  "isNotificationAgreement": true,
  "isAdAgreement": true,
  "isNightAdAgreement": true,
  "pushType": "GCM",
  "timezoneId": "Asia/Seoul",
  "uid": "uid",
  "country": "KR",
  "language": "ko",
  "deviceId": "X3LOdJSQdNzCCvcbiSPZTGK1M9srPU5EumRD"
}
```

| Field                   | Usage             | Description                              |
| ----------------------- | ----------------- | ---------------------------------------- |
| token                   | Required, String  | トークン、最大1,600文字                      |
| oldToken                | Optional, String  | 既存トークン、最大1,600文字                   |
| pushType                | Required, String  | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
| isNotificationAgreement | Required, Boolean | true or false                            |
| isAdAgreement           | Required, Boolean | true or false                            |
| isNightAdAgreement      | Required, Boolean | true or false                            |
| timezoneId              | Required, String  | Area/Name. IANA time zone database.      |
| country                 | Required、String  | ISO 3166-1 alpha-2、ISO 3166-1 alpha-3、3文字 |
| language                | Required, String  | ISO 639-1, ISO 639-2, iOS(language code + script code), 8文字 |
| uid                     | Required, String  | ユーザーID。emoji不可。最大64文字    |
| deviceId                | Optional, String  | デバイスID。36文字                    |


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

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tokens -d '{"oldToken":"oldToken","token":"token","isNotificationAgreement":true,"isAdAgreement":true,"isNightAdAgreement":true,"pushType":"GCM","timezoneId":"Asia/Seoul","uid":"uid","country":"KR","language":"ko"}'
```

##### Description

- トークンがすでに登録されている場合に再度登録すると、既存情報をアップデートします。
- もしトークンが変更された場合は、oldTokenに既存トークンを、tokenに新しいトークンを設定して登録すると新しいトークンにアップデートされます。
- "isNotificationAgreement"はプッシュメッセージの受信に同意するかどうか、"isAdAgreement"は広告性プッシュメッセージを受信するかどうか、isNightAdAgreement"は夜間広告性プッシュメッセージを受信するかどうかを表します。
- 例えば、すべてのプッシュメッセージの受信を希望する場合は、フィールド3個をすべてtrueに設定してください。プッシュメッセージのみ受信する場合は、 "isNotificationAgreement"のみtrueに設定してください。
- 受信に同意するかどうかは、韓国情報通信網法の規定(第50条から第50条の8)に従います。
    - [KISAガイドリンク](https://spam.kisa.or.kr/spam/na/ntt/selectNttInfo.do?mi=1020&nttSn=1171&bbsId=1002)    
    - [法令リンク](http://www.law.go.kr/lsEfInfoP.do?lsiSeq=123210#)  
- ネットワーク状態が良くないか、複数の理由によるレスポンス遅延が発生することがあります。モバイルアプリケーション起動への影響を最小化するために制限時間(timeout)を短く設定し、起動するたびにトークンを登録することを推奨します。
- トークンはセキュリティ的なイシュー、アプリアップデート、削除など、さまざまな理由で再発行されることがあります。頻繁に変更されることはないですが、受信率を高めるには、起動するたびに最新トークンを登録することを推奨します。
- アプリ削除などでトークンが満了してもすぐにGCM、APNSサーバーに適用されず、アプリ削除後にプッシュメッセージを送信した時、送信に成功することがあります。

### 照会
#### トークンとプッシュタイプで照会
- クライアントから照会できます。
##### Method, URL

```
GET /push/v2.0/appkeys/{appkey}/tokens/{token}?pushType={pushType}
Content-Type: application/json;charset=UTF-8
```

| Field    | Usage            | Description                              |
| -------- | ---------------- | ---------------------------------------- |
| appkey   | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー   |
| pushType | Required, String | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |

##### Response Body

```
{
	"token" : {
		"pushType" : "GCM",
		"isNotificationAgreement": true,
		"isAdAgreement": true,
		"isNightAdAgreement": true,
		"timezoneId" : "Asia/Seoul",
		"country": "KR",
		"language": "ko",
		"uid" : "User ID",
		"token" : "Token",
		"updateDateTime": "2017-08-12T01:04:18.000+09:00",
		"adAgreementDateTime": "2017-08-12T01:04:19.000+09:00",
		"nightAdAgreementDateTime": "2017-08-12T01:04:19.000+09:00"        
	},
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```

| Field                    | Usage              | Description            |
| ------------------------ | ------------------ | ---------------------- |
| updateDateTime           | -, DateTime String | トークンアップデート日時      |
| adAgreementDateTime      | -, DateTime String | 広告性プッシュメッセージ受信同意日時 |
| nightAdAgreementDateTime | -, DateTime String | 夜間広告性プッシュメッセージ受信同意日時 |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tokens/token?pushType=GCM
```

#### ユーザーIDで照会
- Secret Keyが必要なAPIで、サーバーから呼び出す必要があります。
##### Method, URL

```
GET /push/v2.0/appkeys/{appkey}/tokens?uid={uid}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field  | Usage            | Description                     |
| ------ | ---------------- | ------------------------------- |
| appkey | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |
| uid    | Required, String | 照会するユーザーID                     |

##### Response Body

```
{
	"tokens": [{
		"pushType" : "GCM",
		"isNotificationAgreement": true,
		"isAdAgreement": true,
		"isNightAdAgreement": true,
		"timezoneId" : "Asia/Seoul",
		"country": "KR",
		"language": "ko",
		"uid" : "User ID",
		"token" : "Token",
        "updateDateTime": "2017-08-12T01:04:18.000+09:00",
        "adAgreementDateTime": "2017-08-12T01:04:19.000+09:00",
        "nightAdAgreementDateTime": "2017-08-12T01:04:19.000+09:00"
	}],
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tokens?uid=uid
```

#### 有効ではないトークン照会
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/invalid-tokens?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&messageId={messageId}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field     | Usage                     | Description                              |
| --------- | ------------------------- | ---------------------------------------- |
| appkey    | Required, String          | Path Variable、サービス利用時に発行されたアプリケーションキー  |
| pageIndex | Optional, Number          | デフォルト値0                                    |
| pageSize  | Optional, Number          | デフォルト値25、最大値100                          |
| from      | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to        | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| messageId | Optional, Number          | 有効ではないトークンが発生したメッセージID                  |

##### Request Body
```
なし
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
			"pushType" : "GCM",
			"createdDateTime" : "2017-02-08T19:39:04.000+09:00"
		}
	]
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/invalid-tokens
```


#### トークンプロパティ統計照会API

**Fade-outしたAPIです。 v2.4以上のAPIをご利用ください。**

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/statistics/token-properties?from={from}&to={to}&tokenProperties={tokenProperties}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field           | Usage                     | Description                              |
| --------------- | ------------------------- | ---------------------------------------- |
| appkey          | Required, String          | Path Variable、サービス利用時に発行されたアプリケーションキー   |
| from            | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to              | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| tokenProperties | Optional, String Array    | 'agreement', 'country', 'language', 'timezoneId'<br/>','で区切る、 e.g. tokenProperties=country,language |

##### Request Body
```
なし
```

##### Response Body
```json
{
	"tokenPropertiesStatistics" : [{
			"dateTime" : "2016-07-11 17:50:00.00+9:00",
			"countries" : {
				"KR" : 100,
				"JP" : 60,
				"CN" : 100
			},
			"languages" : {
				"ko" : 90,
				"ja" : 60,
				"zh" : 100
			},
			"timezoneIds": {
				"Asia/Seoul": 260
			},
			"agreements": {
				"ON": 260
			}
		}, {
			"dateTime" : "2016-07-11 17:51:00.00+9:00",
			"countries" : {
				"KR" : 100,
				"JP" : 60,
				"CN" : 100
			},
			"languages" : {
				"ko" : 90,
				"ja" : 60,
				"zh" : 100
			},
			"timezoneIds": {
				"Asia/Seoul": 260
			},
			"agreements": {
				"ON": 260
			}
		}
	],
	"header" : {
		"isSuccessful" : true,
		"resultCode" : 0,
		"resultMessage" : "SUCCESS"
	}
}
```

| Field          | Usage  | Description                              |
| -------------- | ------ | ---------------------------------------- |
| dateTime       | String | データが収集された日時                       |
| agreements     | String | 'ON'(すべて受信)、'NIGHT_AD_OFF'(夜間広告受信拒否)、'AD_OFF'(広告受信拒否)、'OFF'(すべて受信拒否) |
| countries.XX   | String | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3, 3文字 |
| languages.XX   | String | ISO 639-1, ISO 639-2, iOS(language code + script code), 8文字 |
| timezoneIds.XX | String | Area/Name. IANA time zone database       |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/statistics/token-properties
```

#### トークン登録統計照会

**Fade-outしたAPIです。 v2.4以上のAPIをご利用ください。**

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/statistics/token-registrations?from={from}&to={to}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field  | Usage                     | Description                              |
| ------ | ------------------------- | ---------------------------------------- |
| appkey | Required, String          | Path Variable、サービス利用時に発行されたアプリケーションキー  |
| from   | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to     | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |

##### Request Body
```
なし
```

##### Response Body
```json
{
	"tokenRegistrationStatistics" : [{
			"dateTime" : "2016-07-11 17:50:00.00+9:00",
			"registered" : 90,
			"deleted" : 20
		}, {
			"dateTime" : "2016-07-11 17:51:00.00+9:00",
			"registered" : 45,
			"deleted" : 10
		}
	],
	"header" : {
		"isSuccessful" : true,
		"resultCode" : 0,
		"resultMessage" : "SUCCESS"
	}
}
```

| Field           | Usage  | Description |
| --------------- | ------ | ----------- |
| dateTime        | String | データが収集された日時 |
| registered | Number | 登録されたトークン数 |
| deleted    | Number | 削除されたトークン数 |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/statistics/token-registrations
```

## メッセージ
### 送信
※ APIで送信したプッシュメッセージはコンソールと単件、リスト照会APIで照会できません。
##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/messages
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
	"provisionedResourceId": "id"
}
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

| Field                 | Usage                  | Description                              |
| --------------------- | ---------------------- | ---------------------------------------- |
| appkey                | Required, String       | Path Variable、サービス利用時に発行されたアプリケーションキー  |
| target.type           | Required, String       | 'ALL', 'UID', 'TAG'受信ターゲットタイプ      |
| target.to             | Optional, String Array | target.typeが受信者UIDリスト(最大10,000個)またはTAG条件 |
| target.pushTypes      | Optional, String Array | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
| target.countries      | Optional, String Array | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3 (最大3文字) |
| content               | Required, Map          | 受信者に伝達される内容(最大8,192文字)               |
| content.default       | Required, Map          | '詳細は、下記共通メッセージ形式'を参照         |
| content.default.title | Optional, String       |                                          |
| content.default.body  | Optional, String       |                                          |
| messageType           | Required, String       | NOTIFICATION, AD                         |
| contact               | Optional, String       | messageTypeがADの場合は必須、数字(0-9)とハイフン(Hypen, -)のみ可能。 |
| removeGuide           | Optional, String       | messageTypeがADの場合は必須           |
| timeToLiveMinute      | Optional, Number       | 単位は分です。範囲は1から60まで、デフォルト値は10です。      |
| provisionedResourceId | Optional, String       | 割り当てられた専用リソース(provisioned Resource) IDです。お問い合わせ：support@cloud.toast.com |

##### Description
- "target.type"に'UID'を設定した時、"target.to"に最大10,000個までUIDを設定できます。
- "target.type"に'TAG'を設定した時、"target.to"にタグIDと3個の条件と1個の括弧('()')を入れた条件を設定できます。
    - 例、男性、30代タグがついているか、女性タグがついている対象にメッセージを送信するなら、
      "target.to=(,男性_ID,AND,30代_ID,),OR,女性_ID"で設定できます。
- "target.pushTypes"フィールドに特定プッシュタイプでのみメッセージを送信できます。
 定義しなければすべてのプッシュタイプ、GCM、APNS、APNS_SANDBOX、TENCENT、ADMで送信します。
- "target.countries"フィールドが"['KR', 'JP']"の場合、トークン国コードが"KR"または"JP"のTokenに送信します。
- "content.default"フィールドは必須で、"content"フィールドの詳細は下記""共通メッセージ"形式を参照してください。
- メッセージを広告タイプ、 "messageType"："AD"で送る場合、 "contact"、"removeGuide"フィールドを必ず含める必要があります。
  "contact"フィールドに連絡先、"removeGuide"フィールドに受信解除方法について入力する必要があります。
- timeToLiveMinuteフィールドを設定すると、設定した時間以上に送信が遅延する場合は自動的に失敗処理されます。

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/messages -d '{"target":{"type":"UID","to":["uid"]},"content":{"default":{"title":"title","body":"body","customKey1":"It is default"},"ko":{"title":"タイトル","body":"内容","customKey2":"韓国語です。"}},"messageType":"AD","contact":"1588-1588","removeGuide":"メニュー > 設定","timeToLiveMinute":1}'
```

### 共通メッセージ
"content"に下記の表通りにメッセージを作成すると、各プッシュタイプに合わせてメッセージが作成され、送信されます。

| Reserved Word              | Platform                                 | Usage                      | GCM        | APNS                     | TENCENT              | ADM              |
| -------------------------- | ---------------------------------------- | -------------------------- | ---------- | ------------------------ | -------------------- | ---------------- |
| title                      | Android, <br/> iOS Watch, <br/> Tencent, <br/> ADM | Optional, String           | data.title | aps.alert.title          | title                | data.title       |
| body                       | Android, <br/> iOS, <br/> Tencent, <br/> ADM | Optional, String           | data.body  | aps.alert.body           | content              | data.body        |
| title-loc-key              | iOS                                      | Optional, String           | -          | aps.alert.title-loc-key  | -                    | -                |
| title-loc-args             | iOS                                      | Optional, Array of Strings | -          | aps.alert.title-loc-args | -                    | -                |
| action-loc-key             | iOS                                      | Optional, String           | -          | aps.alert.action-loc-key | -                    | -                |
| loc-key                    | iOS                                      | Optional, String           | -          | aps.alert.loc-key        | -                    | -                |
| loc-args                   | iOS                                      | Optional, Array of String  | -          | aps.alert.loc-args       | -                    | -                |
| launch-image               | iOS                                      | Optional, String           | -          | aps.alert.launch-image   | -                    | -                |
| badge                      | iOS                                      | Optional, Number           | -          | aps.badge                | -                    | -                |
| sound                      | Android, <br/> iOS, <br/> Tencent, <br/> ADM | Optional, String           | data.sound | aps.sound                | custom_content.sound | data.sound       |
| content-available          | iOS                                      | Optional, String           | -          | aps.content-available    | -                    | -                |
| category                   | iOS                                      | Optional, String           | -          | aps.category             | -                    | -                |
| mutable-content            | iOS                                      | Optional, String           | -          | aps.mutable-content      | -                    | -                |
| consolidationKey           | ADM                                      | Optional, String           | -          | -                        | -                    | consolidationKey |
| expiresAfter               | ADM                                      | Optional, Number           | -          | -                        | -                    | expiresAfter     |
| messageDeliveryReceipt     | Android, <br/>iOS, <br/> Tencent         | Unnecessary                | -          | -                        | -                    | -                |
| messageDeliveryReceiptData | Android, <br/>iOS, <br/> Tencent         | Unnecessary                | -          | -                        | -                    | -                |

予約語はメッセージ作成時にプラットフォームごとに適切な位置に設定されます。ユーザーが任意でデータタイプや位置などを変更できません。
その他のユーザーが定義した単語は、次のようにCustom Key/Valueフィールドに入ります。

| Word      | Platform                          | Usage                                    | GCM            | APNS      | TENCENT                  | ADM            |
| --------- | --------------------------------- | ---------------------------------------- | -------------- | --------- | ------------------------ | -------------- |
| customKey | Android, <br/> iOS, <br/> Tencent | Optional, <br/> Object, <br/> Array, <br/> String, <br/> Number | data.customKey | customKey | custom_content.customKey | data.customKey |

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
- target.typeを'ALL'に設定すると、すべてのトークンにメッセージを送信します。

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
- target.typeを'UID'に設定し、target.toにユーザーIDを設定して、特定ユーザーにメッセージを送信します。

#### 3. 一部の国やプッシュタイプのユーザーに送信
特定の国や端末(Android、iOS…)を使用するユーザーにのみメッセージを送信する例です。

##### Request Body
```json
{
    "target" : {
        "type" : "ALL",
        "countries": ["KR", "JP"],
        "pushTypes": ["GCM", "APNS"]
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

##### GCM(Android)に受信するメッセージ
```json
{
	"data": {
		"title": "title",
		"body": "body",
		"customKey": "value"
	}
}
```
##### APNS(iOS)に受信するメッセージ
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
##### TENCENT(Android)に受信するメッセージ
```json
 {
    "title": "title",
    "body": "body",
    "custom_content": {
        "customKey": "value"
    }
}
```

##### ADM(Fire OS)に受信するメッセージ
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
- title、bodyなどの予約語は、プッシュタイプに合ったメッセージに変換する時、指定された位置に設定され、送信されます。
 その他のユーザーが定義したフィールドは、各プッシュタイプのCustom Keyに設定されます。
- badge、consolidationKeyなどの特定プッシュタイプにのみ定義されている予約語は、他のプッシュタイプからは除外されます。
 例えばbadgeはAPNS(iOS)メッセージにのみ設定され、GCM、TENCENT、ADMでは除外されます。

#### 5. 広告性メッセージ
広告性メッセージで送信時にメッセージに追加される広告文言例です。

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

##### GCM(Android)、ko(韓国語)に受信するメッセージ
```json
 {
    "data": {
        "title": "(広告)金曜日特別イベント1588",
        "body": "今すぐご注文で50%OFF！\nメニュー > 通知設定"
    }
}
```
##### APNS(iOS)、ko(韓国語)に受信するメッセージ
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
##### GCM(Android)、ja(日本語)に受信するメッセージ
```json
 {
    "data": {
        "title": "金曜日特別イベント",
        "body": "今すぐご注文で50%OFF！"
    }
}
```
##### APNS(iOS), ja(日本語)に受信するメッセージ
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
- 各プッシュタイプにメッセージが送信される時、titleに広告表示文言と代表番号が、bodyに受信同意撤回方法が追加されて送信されます。
- 広告性メッセージは、言語コードが韓国語(ko, ko-)のユーザーにのみ追加されます。上の例のように海外ユーザー(日本語)には広告文言が追加されません。

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

##### GCM(Android)、ko(韓国語)に受信するメッセージ
```json
{
    "data": {
        "title": "タイトル",
        "body": "内容",
        "customKey": "'ko', 'ko-'で始まる言語コードに設定されます。"
    }
}
```
##### GCM(Android), ko-KR(韓国語)に受信するメッセージ
```json
{
    "data": {
        "title": "タイトル",
        "body": "内容",
        "customKey": "'ko', 'ko-'で始まる言語コードに設定されます。"
    }
}
```
##### GCM(Android), ja(日本語)に受信するメッセージ
```json
{
    "data": {
        "title": "タイトル",
        "body": "プッシュ・メッセージ",
        "customKey": "value"
    }
}
```
##### GCM(Android), en(英語)に受信するメッセージ
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
                        "submitName": "転送ボタン名",
                        "buttonType" : "REPLY | OPEN_APP | OPEN_URL | DISMISS",
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
| richMessage.button.submitName | Optional, String | 転送ボタン名。iOSでボタンタイプがREPLYの時に表示 |
| richMessage.button.buttonType | Required、String | ボタンタイプ、 REPLY、OPEN_APP、OPEN_URL、DISMISS |
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

### 照会
#### リスト照会
※コンソールで送信したプッシュメッセージのみリスト照会APIで照会できます。
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/messages?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&deliveryType={deliveryType}&messageStatus={messageStatus}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field         | Usage                     | Description                              |
| ------------- | ------------------------- | ---------------------------------------- |
| appkey        | Required, String          | Path Variable、サービス利用時に発行されたアプリケーションキー  |
| pageIndex     | Optional, Number          | デフォルト値0                                    |
| pageSize      | Optional, Number          | デフォルト値25、最大値100                          |
| from          | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to            | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| deliveryType  | Optional, String          | 'INSTANT'(即時送信), 'RESERVATION'(予約送信)   |
| messageStatus | Optional, String          | 'READY', 'PROCESSING', 'COMPLETE', 'CANCEL_NO_TARGET', 'CANCEL_INVALID_CERTIFICATE', 'CANCEL_INVALID_MESSAGE', 'CANCEL_UNSUPPORTED_MESSAGE_TYPE', 'CANCEL_UNAUTHORIZED', 'CANCEL_UNKNOWN' |

##### Request Body
```
なし
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

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/messages
```

| Field                 | Usage | Description               |
| --------------------- | ----- | ------------------------- |
| createdDateTime       | -     | メッセージが作成された日時(ISO 8601)    |
| completedDateTime     | -     | メッセージ送信が完了した日時(ISO 8601) |
| targetCount           | -     | 送信される目標(target)トークン数          |
| sentCount             | -     | 実際に送信されたトークン数          |
| provisionedResourceId | -     | メッセージが送信された専用リソースID       |
| totalCount            | -     | フィルタリングされたメッセージ総数       |

- "messageStatus"フィールドはメッセージ状態を表します。次のような状態があります。
    - READY：メッセージ送信リクエストが登録された状態です。
    - PROCESSING：メッセージ作成が完了し、待機または送信中です。
- COMPLETE：メッセージの送信が完了した状態です。
- CANCEL_NO_TARGET：メッセージ送信対象が存在しないためキャンセルされた状態です。次のような理由で送信がキャンセルされることがあります。
       登録されたトークンがない時
         広告プッシュメッセージの場合、受信に同意したユーザーがいない時
        夜間広告プッシュメッセージ(21時～ 8時)の場合、夜間広告受信に同意したユーザーがいない時
        登録されたトークンが削除され、トークンがない時
    - CANCEL_INVALID_CERTIFICATE：証明書が無効でキャンセルされた状態です。証明書の状態を確認する必要があります。
    - CANCEL_INVALID_MESSAGE：メッセージ形式が合っておらずキャンセルされた状態。
    - CANCEL_UNSUPPORTED_MESSAGE_TYPE：メッセージ形式が合っておらずキャンセルされた状態です。
    - CANCEL_UNAUTHORIZED：証明書認証プロセスで失敗した状態です。証明書の状態を確認する必要があります。
    - CANCEL_UNKNOWN：内部エラーが発生した状態です。

#### 単件照会
※コンソールで送信したプッシュメッセージのみ、単件照会APIで照会できます。
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/messages/{message-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field     | Usage            | Description                      |
| --------- | ---------------- | -------------------------------- |
| appkey    | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |
| messageId | Required, Number | メッセージID                          |

##### Request Body
```
なし
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

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/messages/{messageId}
```

#### 失敗したメッセージリスト照会
送信に失敗したメッセージを照会できます。
ただし、トークンがない場合、(INVALID_TOKEN)は送信失敗と判断しません。

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/message-errors?messageId={messageId}&messageErrorType={messageErrorType}&messagErrorCause={messageErrorCause}&from={from}&to={to}&limit={limit}
Header
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field             | Usage                     | Description                             |
|-------------------|---------------------------| --------------------------------------- |
| appkey            | Required, String          | Path Variable、サービス利用時に発行されたアプリケーションキー |
| messageId         | Optional, Number          | メッセージID                                 |
| messageErrorType  | Optional, String          | 'CLIENT_ERROR', 'EXTERNAL_ERROR', 'INTERNAL_ERROR' |
| messageErrorCause | Optional, String          | 'UNSUPPORTED_MESSAGE_TYPE', 'INVALID_MESSAGE', 'INVALID_CERTIFICATE', 'UNAUTHORIZED', 'EXPIRED_TIME_OUT', 'APNS_ERROR', 'GCM_ERROR', 'TENCENT_ERROR', 'AGENT_ERROR', 'ADM_ERROR' |
| from              | Optional, DateTime String | 過去30日まで、デフォルト値は過去7日前(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to                | Optional, DateTime String | 過去30日まで、デフォルト値は現在(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| limit             | Optional, Number          | 一度に照会するリストサイズ、デフォルト値と最大値は100         |
| pageNumber        | Optional, Number          | 페이지 번호, 기본값 1 |

##### Description
- messageErrorTypeとmessageErrorCauseは次を意味します。
    - CLIENT_ERROR：クライアントの無効なリクエスト
        - UNSUPPORTED_MESSAGE_TYPE：サポートしていないメッセージタイプ
        - INVALID_MESSAGE：正常ではないメッセージ
        - INVALID_CERTIFICATE：証明書満了または証明書情報が不正
        - UNAUTHORIZED：証明書満了または証明書情報が不正
    - EXTERNAL_ERROR：APNS、GCM、Tencent、ADMなどプッシュと接続した外部サービスのエラー
        - APNS_ERROR：APNS(iOS)に送信失敗
        - GCM_ERROR：GCM(Google)に送信失敗
        - TENCENT_ERROR：Tencentに送信失敗
        - ADM_ERROR：ADMに送信失敗
    - INTERNAL_ERROR：プッシュ内部で発生したエラー
        - EXPIRED_TIME_OUT：送信遅延によるメッセージの有効時間切れ
        - AGENT_ERROR：Agent内部エラーによる送信失敗
- Response Bodyでheader.resultCodeが40010の場合、検索期間(from, to)を狭めて検索する必要があります。

##### Request Body
```
なし
```

##### Response Body
```
{
	"messageErrors" : [{
			"messageId" : 0,
			"messageIdString" : "0",
			"pushType" : "GCM",
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
| Field           | Usage | Description                              |
| --------------- | ----- | ---------------------------------------- |
| messageId       | -     | 失敗したメッセージID                              |
| messageIdString | -     | 失敗したメッセージID                              |
| pushType        | -     | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
| payload         | -     | 端末に送信された実際のメッセージ内容                 |
| tokens          | -     | 送信に失敗した受信者のuidとtoken                  |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/message-errors
```

#### メッセージ受信、確認統計照会

**Fade-outしたAPIです。 v2.4以上のAPIをご利用ください。**

メッセージ受信、確認収集(message delivery receipt)機能を有効化して、v1.4以上のSDKを適用すると、送信したメッセージの受信、確認情報を照会できます。
収集された情報を統計APIで照会できます。機能はコンソールの**設定**タブで有効にできます。

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/statistics/message-delivery-receipts?from={from}&to={to}&event={event}&timeUnit={timeUnit}&messageId={messageId}
Header
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field     | Usage                     | Description                              |
| --------- | ------------------------- | ---------------------------------------- |
| appkey    | Required, String          | Path Variable、サービス利用時に発行されたアプリケーションキー   |
| from      | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to        | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| event     | Optional, String          | 'SENT', 'SENT_FAILED', 'RECEIVED', 'OPENED' |
| timeUnit  | Optional, String          | 'MINUTES', 'HOURS', 'DAYS'<br>値がない場合は、照会期間に応じて任意で統計が提供されます。<br>照会期間が1日以上は日単位、 1時間～24時間の場合は時間単位、 1時間以下は分単位で表示されます。 |
| messageId | Optional, Number          | メッセージID                                  |

##### Request Body
```
なし
```

##### Response Body

```
{
	"messageDeliveryReceiptStatistics" : [{
			"dateTime" : "2016-07-11 17:50:00.00+9:00",
			"sent" : 13,
			"sentFailed": 0,
			"received" : 12,
			"opened" : 10
		}
	],
	"header" : {
		"isSuccessful" : true,
		"resultCode" : 0,
		"resultMessage" : "SUCCESS"
	}
}
```

| Field      | Usage                     | Description         |
| ---------- | ------------------------- | ------------------- |
| dateTime   | Optional, DateTime String | ISO 8601            |
| sent       | Optional, Number          | サーバーから送信した数     |
| sentFailed | Optional, Number          | サーバーから送信失敗した数  |
| received   | Optional, Number          | 端末で受信した数     |
| opened     | Optional, Number          | 端末でユーザーがクリックしてオープンした数 |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/statistics/message-delivery-receipts
```

## 予約メッセージ

### 作成
#### 予約メッセージ送信スケジュールの作成
##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/schedules
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field  | Usage            | Description                     |
| ------ | ---------------- | ------------------------------- |
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

| Field      | Usage                  | Description                              |
| ---------- | ---------------------- | ---------------------------------------- |
| type       | Required, String       | 'EVERY_DAY'(毎日), 'EVERY_WEEK'(毎週), 'EVERY_MONTH'(毎月) |
| fromDate   | Required, Date String  | 予約メッセージ開始年月日(YYYY-MM-DD)               |
| toDate     | Required, Date String  | 予約メッセージ終了年月日(YYYY-MM-DD)               |
| times      | Required, Time String  | 予約メッセージ送信時間、分(hh:mm)                     |
| days       | Optional, Number Array | typeが'EVERY_MONTH'の時に設定します。(1, 2, ..., 31: 1日、 2日、 ..., 31日) |
| daysOfWeek | Optional, String Array | typeが'EVERY_WEEK'の時に設定します。 ('SUNDAY', 'MONDAY', 'TUESDAY', 'WEDNESDAY', 'THURSDAY', 'FRIDAY', 'SATURDAY') |

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

| Field     | Usage | Description                         |
| --------- | ----- | ----------------------------------- |
| schedules | -     | 日時(ISO 8601, e.g. YYYY-MM-DDThh:mm) |

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/schedules -d '{"type":"EVERY_MONTH","fromDate":"2016-12-30","toDate":"2017-01-02","times":["12:00","17:00"],"days":[1,15],"daysOfWeek":["SUNDAY","MONDAY"]}'
```

#### 予約メッセージ作成
##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/reservations
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field  | Usage            | Description                     |
| ------ | ---------------- | ------------------------------- |
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

| Field       | Usage                           | Description      |
| ----------- | ------------------------------- | ---------------- |
| schedules   | Required, DateTime String Array | 予約メッセージ送信スケジュールリスト |
| isLocalTime | Required, Boolean               | 現地時間送信するかどうか |

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

| Field               | Usage  | Description    |
| ------------------- | ------ | -------------- |
| reservationId       | Number | 予約メッセージID     |
| reservationIdString | String | 予約メッセージID文字列 |

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations -d '{"schedules":["2016-12-30T12:40","2016-12-31T12:40"],"isLocalTime":false,"target":{"type":"UID","to":["uid"]},"content":{"default":{"title":"title","body":"body"}},"messageType":"AD","contact":"1588-1588","removeGuide":"メニュー > 設定","timeToLiveMinute":1}'
```

### 照会
#### リスト照会
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/reservations?pageIndex={pageIndex}&pageSize={pageSize}&reservationStatus={reservationsStatus}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field             | Usage                     | Description                              |
| ----------------- | ------------------------- | ---------------------------------------- |
| appkey            | Required, String          | Path Variable、サービス利用時に発行されたアプリケーションキー  |
| pageIndex         | Optional, Number          | デフォルト値0                                    |
| pageSize          | Optional, Number          | デフォルト値25、最大値100                          |
| from              | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to                | Optional, DateTime String | 過去30日まで(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| reservationStatus | Optional, String          | 'RESERVED', 'COMPLETE'                   |

##### Request Body
```
なし
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

| Field                         | Usage | Description                              |
| ----------------------------- | ----- | ---------------------------------------- |
| reservationIdString           | -     | 予約メッセージID文字列                    |
| createdDateTime               | -     | 予約メッセージ登録日時(ISO 8601)                  |
| updatedDateTime               | -     | 予約メッセージ修正日時(ISO 8601)                  |
| completedDateTime             | -     | 予約メッセージ送信完了日時、完了していない場合は現在の時間を表示(ISO 8601) |
| reservationStatus             | -     | 'RESERVED', 'COMPLETED'                  |
| schedules.scheduleId          | -     | 予約メッセージ送信スケジュールID                        |
| schedules.scheduleIdString    | -     | 予約メッセージ送信スケジュールID文字列             |
| schedules.reservationIdString | -     | 予約メッセージ送信スケジュールに属す予約メッセージID文字列  |
| schedules.deliveryDateTime    | -     | 予約メッセージ送信日時                      |
| schedules.timezoneOffset      | -     | 予約メッセージ送信標準時間帯、現地時間送信時の設定   |
| schedules.scheduleStatus      | -     | 'READY', 'SENDING', 'CANCELED', 'DONE'予約メッセージ送信スケジュール状態 |
| totalCount                    | -     | 登録された予約メッセージ総数                     |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations
```

#### 単件照会
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/reservations/{reservation-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

##### Request Body
```
なし
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

| Field           | Usage           | Description        |
| --------------- | --------------- | ------------------ |
| updatedDateTime | DateTime String | 予約修正日時(ISO 8601) |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations/{reservationId}
```

#### 送信された予約メッセージ照会
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/reservations/{reservation-id}/messages?pageIndex={pageIndex}&pageSize={pageSize}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field         | Usage            | Description                     |
| ------------- | ---------------- | ------------------------------- |
| appkey        | Required, String | Path Variable、サービス利用時に発行されたアプリケーションキー |
| reservationId | Required, Number | 予約メッセージID                      |
| pageIndex     | Optional, Number | デフォルト値0                           |
| pageSize      | Optional, Number | デフォルト値25、最大値100                 |

##### Request Body
```
なし
```

##### Response Body
```
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "success",
		"isSuccessful" : true
	},
	"messages" : [{
			"messageId" : 356125922591162,
			"messageIdString" : "356125922591162",
			"target" : {
				"type" : "ALL",
				"pushTypes" : ["GCM", "APNS", "APNS_SANDBOX", "TENCENT", "ADM"]
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

| Field      | Usage | Description   |
| ---------- | ----- | ------------- |
| totalCount | -     | 送信されたメッセージ総数 |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations/{reservationId}/messages
```

### 修正
#### 予約メッセージの修正
##### Method, URL, Headers
```
PUT /push/v2.0/appkeys/{appkey}/reservations/{reservationId}
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

##### Example
```
curl -X PUT -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations/{reservationId} -d '{"schedules":["2018-12-30T12:40","2018-12-31T12:40"],"isLocalTime":false,"target":{"type":"UID","to":["uid"]},"content":{"default":{"title":"title","body":"body"}},"messageType":"AD","contact":"1588-1588","removeGuide":"メニュー > 設定","timeToLiveMinute":1}'
```

### 削除
#### 予約メッセージの削除
##### Method, URL, Headers
```
DELETE /push/v2.0/appkeys/{appkey}/reservations?reservationIds={reservationId,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field          | Usage                  | Description                      |
| -------------- | ---------------------- | -------------------------------- |
| appkey         | Required, String       | Path Variable、サービス利用時に発行されたアプリケーションキー |
| reservationIds | Required, Number Array | ','で区切る、例) reservationIds=1,2 |

##### Request Body
```
なし
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

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations?reservationIds={reservationId,}
```

## タグ

### 作成
#### タグの作成
##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/tags
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body

```json
{
    "tagName" :  "30"
}
```

| Field   | Usage            | Description     |
| ------- | ---------------- | --------------- |
| tagName | Required, String | タグ名、最長255、空白(Space)文字不可 |

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

| Field | Usage            | Description      |
| ----- | ---------------- | ---------------- |
| tagId | Required, String | 作成されたタグID。長さ8 |

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags -d '{"tagName":"30"}'
```

#### タグにUID追加作成
- タグにUIDを追加(append)すること。既存のUIDを追加するとUIDのタグは増えます。
- 1つのUIDの最大タグ数は16個です。
##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/tags/{tag-id}/uids
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
| Field | Usage                  | Description                    |
| ----- | ---------------------- | ------------------------------ |
| uids  | Required, String Array | UID配列、最長16、UID最長64 |

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

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags/{tagId}/uids -d '{"uids":["uid"]}'
```

#### UIDにタグリスト設定
- UIDのタグを交換(replace)することです。既に設定されているタグは削除され、新しいタグに設定されます。
##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/uids
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

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/uids -d '{"uid":"uid","tagIds":["TAG_ID"]}'
```

### 照会
#### タグリスト照会
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/tags?tagName={tagName}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field   | Usage            | Description |
| ------- | ---------------- | ----------- |
| tagName | Optional, String | タグ名 |

##### Request Body
```
なし
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

| Field           | Usage                      | Description      |
| --------------- | -------------------------- | ---------------- |
| createdDateTime | Required, Date Time String | 作成日時(ISO 8601) |
| updatedDateTime | Required, Date Time String | 修正日時(ISO 8601) |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags
```

#### タグ単件照会
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
なし
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

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags/{tagId}
```

#### タグのUIDリスト照会
- タグがついているUIDリストを照会します。

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/tags/{tag-id}/uids?offsetUid={uid}&limit={limit}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field     | Usage            | Description     |
| --------- | ---------------- | --------------- |
| offsetUid | Optional, String | 設定されたUIDの次から照会 |
| limit     | Optional, Number | 照会するUID数  |

##### Request Body
```
なし
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
                    "contactType" :  "TOKEN_GCM",
                    "contact" :  "token",
                    "createdDateTime" :  "2017-07-07T07:07:07.777+09:00"
                }
            ]
        }
    ]
}
```

| Field           | Usage                      | Description                              |
| --------------- | -------------------------- | ---------------------------------------- |
| contacts        | -, Object Array            | UIDの連絡先、トークン情報リスト                |
| contactType     | -, String                  | トークンタイプ、 'TOKEN_GCM', 'TOKEN_APNS', 'TOKEN_APNS_SANDBOX', 'TOKEN_TENCENT', 'TOKEN_ADM' |
| contact         | -, String                  | トークン                                |
| createdDateTime | Required, Date Time String | 作成日時(ISO 8601)                         |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags/{tagId}/uids
```

#### UID照会
- UIDを照会します。
- トークン登録時、連絡先(contact)が登録されます。
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/uids/{uid}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
なし
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
                "contactType" :  "TOKEN_GCM",
                "contact" :  "token",
                "createdDateTime" :  "2017-07-07T07:07:07.777+09:00"
            }
        ]
    }
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/uids/uid
```

### 修正
#### タグの修正
##### Method, URL, Headers
```
PUT /push/v2.0/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```json
{
    "tagName" :  "30代"
}
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

##### Example
```
curl -X PUT -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags/{tagId} -d '{"tagName":"33"}'
```

### 削除
#### タグの削除
##### Method, URL, Headers
```
DELETE /push/v2.0/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
なし
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

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags/{tagId}
```

#### UID削除
- UIDを削除すると、Contact、Tokenも一緒に削除されます。
##### Method, URL, Headers
```
DELETE /push/v2.0/appkeys/{appkey}/uids?uids={uid,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage           | Description                              |
| ----- | --------------- | ---------------------------------------- |
| uids  | -, Object Array | 削除するUIDリスト。カンマ(,)で区切ります。一度に16個まで削除できます。 |

##### Request Body
```
なし
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

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/uids?uids=uid
```

#### タグのUID削除
- TagとUIDの関係のみ削除します。
- Contact、Tokenは削除されません。
##### Method, URL, Headers
```
DELETE /push/v2.0/appkeys/{appkey}/tags/{tagId}/uids?uids={uid,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
なし
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

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags/{tagId}/uids?uids=uid
```

## UID

### 作成

#### タグの追加
- タグIDでUIDにタグを追加します。
- Secret Keyが必要ありません。アプリで呼び出せます。
##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```json
{
    "tagIds": ["TAG_ID01"]
}
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

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/uids/uid/tag-ids -d '{"tagIds":["TAG_ID"]}'
```

### 照会

#### UIDのタグID照会
- UIDのタグIDを照会します。
- Secret Keyが必要ありません。アプリで呼び出せます。
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```
なし
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

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/uids/uid/tag-ids
```

### 修正
#### UIDのタグ修正
- UIDにタグIDでタグを修正します。
- Secret Keyが必要ありません。アプリで呼び出せます。
##### Method, URL, Headers
```
PUT /push/v2.0/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```json
{
    "tagIds": ["TAG_ID02"]
}
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

##### Example
```
curl -X PUT -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/uids/uid/tag-ids -d '{"tagIds":["TAG_ID"]}'
```

### タグの削除
- UIDのタグIDを照会します。
- Secret Keyが必要ありません。アプリで呼び出せます。
##### Method, URL, Headers
```
DELETE /push/v2.0/appkeys/{appkey}/uids/{uid}/tag-ids?tagIds={tagId,}
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```
なし
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

| Field  | Usage                  | Description                         |
| ------ | ---------------------- | ----------------------------------- |
| tagIds | Required, String Array | Query String、削除するタグID。カンマ(,)で区切る |

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/uids/uid/tag-ids?tagIds=TAG_ID_01,TAG_ID_02
```


* *文書修正履歴*
    * *(2018.06.26)メッセージ送信例の追加*
    * *(2018.06.26) pushType ADMの追加*
    * *(2018.05.29) v2.1トークン照会APIの追加*
    * *(2018.05.29) API curlガイドの追加*
    * *(2018.04.24) v2.0 Message Delivery Receipt APIにtimeUnitフィールド説明の追加*
    * *(2018.04.24) v2.0 APIにDateTime形式の説明を追加*
    * *(2018.03.22) v2.0 UID APIの追加*
    * *(2018.02.22) v2.0 Message照会API deliveryTypeフィールドの追加*
    * *(2018.02.22) pushType APNS_VOIP、APNS_SANDBOXVOIPの追加*
    * *(2017.11.23) v2.0 Message Error APIの説明を修正*
    * *(2017.08.24) v2.0 Token, Reservation APIの説明間違いを修正*
    * *(2017.07.20) v2.0 Tag API Referenceの追加*
    * *(2017.07.20)失敗したメッセージ照会APIの追加*
    * *(2017.04.25) v2.0 API Referenceの追加*
    * *(2017.02.23)トークン照会API文書の補強*
