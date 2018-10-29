## Notification > Push > Console Guide

## Certificate Management

### Create and Import Firebase Cloud Messaging(FCM) API Key

1. Sign in the [Google Firebase Console](https://console.firebase.google.com/).
2. Select **Add Project**.
3. Enter a project name with information and click **Create Project**.
4. Click the Gear icon on the first page of the project.
5. Click **Project Settings**.
6. Select the **Cloud Messaging** tab on the **Settings** page.
7. Check your **Server Key** (API Key) and **Sender ID**.

### Register FCM API Key

1. Go to **Console > Notification > Push** and click the **Certificates** tab.
2. Copy the FCM API Key just created and paste it to the  **GCM API Key**, and press **Register** to complete.

### Caution for Google Project
- Google is scheduled to [close the GCM service]((https://developers.google.com/cloud-messaging/faq)) as of April 11, 2019.

- If a key created from Google Project (now, Google Cloud Platform) is currently used as GCM API Key, the usage may not be available due to different setting. In such case, the Google Project must be relocated to Firebase and create a new server key at Firebase.
	- Google provides [Firebase Migration Guide](https://developers.google.com/cloud-messaging/android/android-migrate-fcm).

#### Relocate from Google Project to Firebase

1. Access [Google Firebase Console](https://console.firebase.google.com/).
2. Select **CREATE NEW PROJECT**.
3. When there is a Google Project under service, the Project Name shows it on the list; choose a Google Project to relocate.
4. The Project ID shall be same with the existing Google Project; create a project.  
5. Click the Gear icon on the first page of the project.
6. Click **Project Settings**.
7. Click the **CLOUD MESSAGING** tab on the page.
8. Check your Server Key (API Key) and Sender ID.
9. Copy the Server Key at Firebase to **GCM API Key** on the **Certificates** tab.

### Create and Import APNS Certificate

1. Implement **Keychain** on Mac.
2. Choose **Access Keychain > Support Certificates > Request a Certificate** from **Certificate Authority**.
3. Enter information as required such as Apple Developer’s ID at **User's Email Address**, choose **Disk Saved**, and **I have saved key pair information**, and click **Continue**.
4. Choose the key size and algorithm at **Key Pair Information** and click **Continue**. Then, a Certificate Signing Request (CSR) file is created.
5. Go to [Apple Developer Certificates](https://developer.apple.com/account/ios/certificate/certificateList.action).
6. Click **+**(Add) at the top right to go to the page issuing Certificates.
7. Select the **Select Type > Request > Generate** tab to upload CSR files made through **Keychain** and click **Generate**.
8. You can create and download a certificate. If not authorized, you can obtain an authority to download.
9. Download a certificate and choose it at **Keychain > Certificates**.
10. Click the right button on the certificate and choose **Send**.
11. Set where to save it and press **Save** (file format: .p12).
12. Enter password to protect exported items, and then it is completed.


### Register APNS Certificate

1. Click **Notification > Push > Certificates** on Console.
2. Click **Select File** at **APNS Certificate > Certificates**.
3. Enter certificate password at **Password** .
4. Click **REGISTER**.

### Caution for APNS
#### Differences between APNS (Production) and APNS_SANDBOX (Development)

- Applications built with Production Provisioning Profile must use APNS (Production) while those built with Development Provisioning Profile must use APNS_SANDBOX (Development). Otherwise, push messages may not be properly received.  

- In most failure cases of message delivery, the APNS certificate registered at TOAST Cloud Push has a different provisioning profile for application buildup.
   APNS(Production) = Production Provisioning Profile
   APNS_SANDBOX(Development) = Development Provisioning Profile  

- For more details, refer to the following:  
  [APNS Overview](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)  
  [App Distribution Quick Start](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppStoreDistributionTutorial/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013839)

#### Causes of Delivery Failure of APNS Messages

- When a certificate has been expired
- When a wrong certificate has been registered
- When a push message has been rejected by the application
- When there’s no internet access

### Join Tencent

1. Access [[Tencent Push Service Website](http://xg.qq.com/)].
2. Click Login(登录) at the top right of the page.
3. Click Join (注册新帐号) at the bottom right of the pop-up page.
4. Enter information required for **Email Account**, including Email Account, Nickname, Password, and Verification Code, and click **Sign up now**.
5. Click **Select your country/region** under **Send Verification Code**.
6. Select **South Korea 0082** for **region**, and your mobile number for **Mobile Number** in the 010AAAABBBB format.
7. Enter verification code and move on to the next step.
8. Check **Activation Email** entered for subscription and enable it.

### Register Tencent Application

1. Login and access [Tencent Push Service Dashboard](http://xg.qq.com/xg/ctr_index/login?go_to_url=http%3A%2F%2Fxg.qq.com%2Fxg%2Fapps%2Fctr_app%2Findex).
2. Click Register Application (**接入推送应用**) at the top right of the page.
3. Enter Application Name (**应用名称**).
4. Select Category (**所属分类**).
5. Choose Android for Application Platform (**应用平台**) and enter Package Name.
6. Click Create Application (**接入应用**).

### Register Tencent ACCESS ID and SECRET KEY

1. Login and access [Tencent Push Service Dashboard](http://xg.qq.com/xg/ctr_index/login?go_to_url=http%3A%2F%2Fxg.qq.com%2Fxg%2Fapps%2Fctr_app%2Findex).
2. Click Statistics (**应用统计**) and enter into Application Dashboard.
3. Click Setting(**配置管理**) > Application Setting (**应用配置**) on the left. Check ACCESS ID, ACCESS KEY, and SECRET KEY.
4. Come back to TOAST and click **Notification > Push > Certificates** at Console.
5. Enter ACCESS ID and SECRET KEY to **Tencent Credential** as confirmed.

### Register ADM Application and Profiles (Acquire Client Id and Client Secret)
1. Access [ADM Developer Console](https://developer.amazon.com/home.html).
2. Click **APP & SERVICES** at the top left and **ADD a New App** at the bottom.
3. After app is created, click **Device Messaging** in the middle. Then, click **Create a New Security Profile**.
4. After profile is completely created, click **Security Profiles** in the middle and **View Security Profile**.
5. Go to the **General** tab to check Client ID and Client Secret.

### Register Setting Information for ADM Kindle (Acquire API Key)
1. Click **Security Profiles** and then **Android/Kindle Setting** in the middle.
2. Enter information such as App Key Name, Package, MD5 Signature, and SHA256 Signature.
3. You can query information of MD5 or SHA256, by using commands like below:
```
> keytool -list -v -keystore {keystoreFileName}

Enter password for key repository:
Type of key repository: JKS
Key repository provider: SUN

One item is included to a key repository.

Nickname: androiddebugkey
Date of Creation: May 9, 2018
Item Type: PrivateKeyEntry
Chain Lengh of Certificate: 1
Certificate [1]:
Owner: C=US, O=Android, CN=Android Debug
Issuer: C=US, O=Android, CN=Android Debug
Serial Number: 1
Appropriate Start Date: Wed. May 09 19:59:46 KST 2018
End Date: Fri. May 01 19:59:46 KST 2048
Certificate Fingerprint:
         MD5:  xxxx
         SHA1: xxxx
         SHA256: xxxx
Signature Algorithm Name: SHA1withRSA
Subject Common Key Algorithm: 1024 bits RSA Key
Version: 1
```
- After registration is completed, click Show and query API key information.


## Immediate Delivery

After certificate and token are properly registered, go to Console and click **Notification > Push > Messages > ADD **and end push messages immediately.

Here are the formats for immediate delivery:

| Name                   | Description                                                  |
| ---------------------- | ------------------------------------------------------------ |
| Target                 | You can configure who to receive push messages.              |
| Target > Type          | Choose one out of ALL, UID, and TAG.                         |
| Target > To            | You can enter UID. Conversion to UID is available by choosing UID in Target > Type: use comma (',') as delimiter to enter many. |
| Target > UIDs          | Use comma (',') as delimiter to enter many.                  |
| Target > Tags          | Click Select and choose tags.                                |
| Target > Selected Tags | Display selected tags: click again to unselect.              |
| Target > Countries     | Enter a country code: use comma (',') as delimiter to enter many. |
| Target > Push Types    | You can choose many, among GCM, APNS, APNS Sandbox, and Tencent. |
| Option > Time To Live  | Refers to effective time for message delivery. If a delivery is delayed during the time set, it is automatically processed as a failure. It is set by the minute: '0' is not deemed as failure due to delivery delay. |
| Message > Editor Type  | Categorized into two types: SIMPLE and JSON. In the case of JSON, you can type in your messages. |
| Message > Message Type | Categorized into two types: NOTIFICATION and AD. In the case of AD, the messages are delivered only to the users who agreed to receive advertising push messages. |

## Scheduled Delivery  

Go to Console and click **Notification > Push > Reservation**. You can manage your delivery on schedule, using **ADD**, **EDIT**, and **DELETE**.

Here are the formats for scheduled delivery:

| Name                                                    | Description                                                  |
| ------------------------------------------------------- | ------------------------------------------------------------ |
| Schedule Type <br /> | EVERY_DAY: Send messages at a specific time, on a daily basis between StartDate and EndDate.<br />EVERY_WEEK: Send messages on a specific day as set in the Reservation Condition, on a weekly basis.<br />EVERY_MONTH: Send messages on a specific date as set in the Reservation Condition, on a monthly basis. |
| Day of Week                                | You may choose a day of the week.                            |
| Day of Month                                            | You can enter date (month), using comma (',') as delimiter.  |
| Delivery Time                                   | You can enter time to deliver messages, in the format of 'HH:mm'. |
| Start Date                                              | You can enter a date to start delivering messages in the format of 'YYYY-MM-DD'. You may use the calendar. |
| End Date                                                | You can enter the last date to deliver messages in the format of 'YYYY-MM-DD'. You may use the calendar. |
| Local Time                                              | When it is set 'true', the messages are delivered on the local time basis. |

## Manage Tokens

Go to **Console > Notification > Push** > and click **Token**, and click **ADD** or **DELETE** to add or delete tokens.  

### Search Tokens

- Select 'TOKEN' for Search Type and choose an appropriate push type to search tokens.

### Search UIDs

- Select 'UID' for Search Type to search UIDs.
- Only a partial match can show on the search result.

## Manage Tags

Go to **Console > Notification > Push** > and click **Tag**, and click **ADD** or **DELETE** to add or delete tags.  

### Manage UIDs with Tags Attached
- Click Tag to check the list of UIDs with the tag attached.
- Click **ADD**or **DELETE** to add or delete UIDs.  
- Up to 1,000 UIDs can be added.

## Token Setting
Go to **Console > Notification > Push** > and click **Setting** to set Token Expiration Period and App Type.  

### Token Expiration Period
- Exclude tokens that are not requested to register during set period from the target of message delivery.
- Default is 12 months.
- Tokens of the users who do not use the app during the period are automatically excluded from the message delivery target, so as to save delivery costs.  

### App Type
- Manage tokens by the type of interfaced app.
- Multiple Tokens
    - The setting is default.
    - User can use the app installed on many devices all at once, and one user can have many tokens.  
    - For instance, if a user has a mobile phone and a tablet PC, he can have two tokens, and push messages are delivered to the two locations: mobile phone and tablet PC.
- Single Token
    - User can use app on one device only. One user can have only one token.
    - For instance, if a user has a mobile phone and a tablet PC, he can have only one token, and push messages are delivered to one of the two.

## Collect Message Receipt/Check Data

Go to **Console > Notification > Push** and **Setting** to enable Collect Message Receipt/Check Data. To operate the enabled function, the SDK must be v1.4 or higher. Check collected data in **Statistics**.     

### Query Statistics
- Check collected data in **Statistics**.
- You can retrieve data collected in the latest 30 days, and the period and time can be configured.

## Log Message Delivery History
- Send message delivery history to Log & Crash Search as specified.


- Click **Console > Notification > Push > Setting**  to enable Logging.

- Enter Appkey of Log & Crash Search used currently, for Appkey.

- For Log Source, enter a value to log along with history logging, as a separator from other logs.

- Enter specific parts only, of the delivery history:

      ALL: Log all history, including success/failure of delivery.

      INFO: Log delivery, and failed delivery of expired tokens only: do not log successful deliveries.

      ERROR: Log history of failed deliveries only.

### Log Type for Delivery History
#### Body
```
{
	"tokens" : [{
			"uid" : "User Id",
			"token" : "Device Token",
            "newToken": "New Deivce Token",
            "message": "Result Message"
		}
	],
	"payload" : {
		"priority" : "high",
		"data" : {
			"messageDeliveryReceipt" : true,
			"title" : "Title",
			"body" : "Body",
			"messageDeliveryReceiptData" : {
				"messageId" : 963854842757578,
				"sentDateTime" : "2017-11-06T10:41:55.619+09:00"
			}
		},
		"registration_ids" : ["Token"]
	}
}
```
- tokens: Delivered token information
    - uid: User ID
    - token: Token
    - newToken: Newly-issued token (shows only when a new token exists)
    - message: Result message (shows only for abnormal responses)
- payload: Messages actually delivered to GCM, APNS, or TENCENT (with different content for each push type)

#### Fields
- Appkey: Push Appkey delivering messages
- messageId: Message ID
- pushType: Push types (GCM, APNS, APNS_SANDBOX, or TENCENT)
- sentResult: Result of delivery (SENT, INVALID_TOKEN, or ERROR)
- messageErrorType: Type of delivery failure
    - CLIENT_ERROR: Delivery failed due to wrong delivery request out of client error
    - EXTERNAL_ERROR: Delivery failed out of external error, due to abnormal responses from Google, Apple, or Tencent server
    - INTERNAL_ERROR: Delivery failed out of internal error
- messageErrorCause: Cause of failed delivery
    - SKIP: Invalid token or certificate
    - NO_TOKEN: No target of delivery
    - INVALID_TOKEN: Delivery request with invalid token
    - INVALID_MESSAGE: Delivery request with invalid message
    - INVALID_CERTIFICATE: Certificate expired
    - UNAUTHORIZED: Certificate expired
    - EXPIRED_TIME_OUT: Message requested for delivery expired
    - APNS_ERROR: Abnormal response from APNS
    - GCM_ERROR:Abnormal response from GCM
    - TENCENT_ERROR: Abnormal response from TENCENT
    - AGENT_ERROR: Abnormal communication with Google, Apple, or Tencent server
    - UNKOWN: Unknown internal error

## Guide for Notice of Personal Information Assignor

When the Customer uses TOAST Push Service, assignment of personal information between the Customer and the Company arises, and the assignee, the Customer, is obliged to disclose the status (assignor and content of business) of his assignment of personal information to the Company, through the personal information handling policy, in accordance with Act on Promotion of Information and Communications Network Utilization and Information Protection. Accordingly, the Company may provide guidelines as below for the Customer, to abide by relevant regulations in the use of TOAST Push Service and not to be adversely affected for not disclosing his assignment status:

**Notice of Personal Information Assignor** To use Push Service, make sure the following is displayed for 'Personal Information Handling Policy' > Assignment Status of the Customer.  
```
Assignor: NHN Entertainment
Content of Business:Deliver Push Messages in lieu of Customers
```


* Document Updates
    * *(July 24, 2018) Added description of APNS Certificate*
    * *(June 15, 2018) Added description of relocating GCM to Firebase*
    * *(Nov. 23, 2017) Added saving history of message delivery*
    * *(Sept. 21, 2017) Modified description of immediate delivery*
    * *(July 20, 2017) Added Guide for Notice of Personal Information Assignor*
