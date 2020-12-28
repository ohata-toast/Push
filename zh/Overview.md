## Notification > Push > Overview

Push helps to deliver messages in various methods and query results. You can schedule a message delivery in accordance with recipient's local time and easily track the result.

Here are the main features of Push.    

### Main Features

- Deliver messages to Android OS (GCM, ADM) and iOS (APNS, Apple Push Notification Service) altogether
- Manage tokens
- Support immediate delivery
- Support for scheduled delivery for local and different time zones
- Manage certificates (APNS), API KEY (GCM), Client ID(ADM), Client Secret(ADM)
- Provide success/failure index for delivery results
- Manage tags, and deliver messages based on tokens
- Collect data on receiving/confirming messages and provide statistics

### Glossary

| Term  | Description                                                  |
| ----- | ------------------------------------------------------------ |
| Token | An original identifier of a device where the application is installed |
| Tag   | A system that classifies UID: many tags can be attached to each UID. |

### Structure

Below shows the structure of Push Service.

![](http://static.toastoven.net/prod_push/19-03-26/overview_en.png)

#### Console

Provides many functions such as certificate management, API testing, and message delivery.

#### REST APIs

Call Public APIs to register/query tokens, deliver messages, and check feedback

#### Client SDK

Can easily register/query tokens and receive push messages

### Functions

#### Save Consent to Receive Notification/Promotional Push Messages and Filter Automatically

In accordance with Act on Promotion of Information and Communications Network Utilization and Information Protection, etc. (from Article 50 to 50-8), you're also required to enter consent to receive notification/promotional/night-time promotional push messages. Messages are automatically filtered based on such consent, when delivered.

[Go to KISA Guidelines](https://spam.kisa.or.kr/spam/sub62.do)

[Check the Act](http://www.law.go.kr/lsEfInfoP.do?lsiSeq=123210#)

#### Filter by Country

Required to enter a language code when registering a token. You can also specify a country to send messages to.

#### Common Message Format

When a message is written on a common format, it is adjusted to each device before delivered.
Likewise, the ready-made common message format of a language code can be applied to any new messages for a corresponding language code and device.

#### Deliver Advertising Messages

For advertising messages, it is obliged to specifically show in the push messages.  

When delivering an advertising message (advertisement), include contact information and how to cancel subscription.
Follow each device's language setting to decide whether to insert advertising messages.
Only the devices configured in Korean (of which the language code starts with ko, or ko-KR) are required to insert advertising messages, in accordance with Act on Promotion of Information and Communications Network Utilization and Information Protection, etc.

#### Deliver Scheduled Messages  

Many types of scheduled message delivery are available, such as once, daily, weekly and monthly delivery.  

You may also deliver on local time basis.

#### Configure Time to Live (TTL)   

Your messages can be setup with Time-to-live, or TTL. When a message remains undelivered beyond TTL, it is processed as a failure.
However, ‘0’ has no TTL and hence, deemed not as a failure due to delivery delay.  

#### Monitor in Real-time  

![](http://static.toastoven.net/prod_push/img_03.png)

You can check the status of message delivery in real time on **Messages**.  

#### Provide Feedbacks

Deleted or invalid tokens are automatically deleted when a message is delivered. You can query deleted tokens, using feedback API.

#### Manage Certificates

Certificates or API keys are managed by push type.

#### Public APIs

Public APIs are available to register/query tokens, deliver messages and check feedbacks.
You may test Public APIs on **APIs**.

#### Enable Service

Click **Console > Notification > Push > Enable**.

- *Document Updates*
  - *Added Deliver Advertising Messages  (Jan.25,2018)*
