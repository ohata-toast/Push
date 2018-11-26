## Notification > Push > iOS SDK Guide
With Push SDK, mobile applications and Push can be easily integrated.

## Main Features
* Register notification tokens to OS
* Receive and display notification messages
* Receive messages and collect application execution indicators through them

## Downloads
Download file to click **iOS SDK** under **Notification > Push** from [TOAST Document](http://docs.toast.com/ko/Download/).

## Supporting Environment
* iOS 8.0 or higher

## Project Setting
### Common
* Set Capabilities <br/>
  ![Remote Notifications](http://static.toastoven.net/toastcloud/sdk/push/ios/settings_capabilities_1.png)<br/>
  ![Push Notifications](http://static.toastoven.net/toastcloud/sdk/push/ios/settings_capabilities_2.png)
* Set Linked Framework and Libraries <br/>
  ![Linked Frameworks](http://static.toastoven.net/toastcloud/sdk/push/ios/settings_libraries.png)

### VoIP
* Set info.plist
```xml
<key>UIBackgroundModes</key>
<array>
    <string>remote-notification</string>
    <string>voip</string>
</array>
```

* Set Linked Framework and Libraries<br/>
  ![Linked Frameworks](http://static.toastoven.net/toastcloud/sdk/push/ios/settings_libraries_voip.png)




## SDK Guide

### Initialize

> Initialize PushSDK first, to register or search tokens.

```
TCPushConfiguration *configuration = [[TCPushConfiguration alloc] initWithAppKey:@"INPUT_YOUR_APPKEY"
                                                                          userId:@"INPUT_USER_ID"];

configuration.channel = @"CHANNEL";                 // Channel configuration (default:@"default")
configuration.isAgreeNotifications = YES;           // Consent to notifications (default:YES)
configuration.isAgreeAdvertisement = YES;           // Consent to advertisement notifications (default:NO)
configuration.isAgreeNightAdvertisement = YES;      // Consent to night-time advertisement notifications (default:NO)

[TCPushSdk initWithConfiguration:configuration];
```

### Set Categories

> Category setting is available only in initialization.

```
TCPushConfiguration *configuration = [[TCPushConfiguration alloc] initWithAppKey:@"INPUT_YOUR_APPKEY"
                                                                          userId:@"INPUT_USER_ID"];

configuration.channel = @"CHANNEL";                 // Channel configuration (default:@"default")
configuration.isAgreeNotifications = YES;           // Consent to notifications (default:YES)
configuration.isAgreeAdvertisement = YES;           // Consent to advertisement notifications (default:NO)
configuration.isAgreeNightAdvertisement = YES;      // Consent to night-time advertisement notifications (default:NO)

UNNotificationCategory *category = ...;
NSSet *categories = [NSSet setWithObject:category];

[TCPushSdk initWithConfiguration:configuration
                      categories:categories];
```

#### Configuration

| Property                  | Description                                    | Required | Default |
| ------------------------- | ---------------------------------------------- | -------- | ------- |
| appKey                    | Push service key                               | Required | N/A     |
| userId                    | User identifier                                | Required | N/A     |
| channel                   | Channel                                        | Optional | Default |
| isAgreeNotification       | Consent to display notifications               | Optional | YES     |
| isAgreeAdvertisement      | Consent to display ad notifications            | Optional | NO      |
| isAgreeNightAdvertisement | Consent to display night-time ad notifications | Optional | NO      |

### Register Tokens

> Initialize PushSDK first, to make a request.<br>
> Only the system token is registered when requesting token registration without initialization.

```
[TCPushSdk registerWithPushType:TCPushTypeAPNs completionHandler:^(NSString *token, NSError *error) {
    if (error == nil) {
        // Success

    } else {
        if (token == nil) {
            // Fail

        } else {
            // Success only th system token
        }
    }
}];
```

#### PushType

| Type                  | Description                           |
| --------------------- | ------------------------------------- |
| TCPushTypeAPNs        | General push messages                 |
| TCPushTypeAPNsSandbox | General push messages for development |
| TCPushTypeVoIP        | VoIP push messages                    |
| TCPushTypeVoIPSandbox | VoIP push messages for development    |

### Query Token Information

> Queries the latest token information registered by the device.


```
[TCPushSdk queryWithPushType:TCPushTypeAPNs completionHandler:^(TCPushTokenInfo *tokenInfo, NSError *error) {
    if (error == nil) {
        // Success

    } else {
        // Fail
    }
}];
```

#### TokenInfo

> Properties relevant to consent to display ad nofitications (such as isAgreeAdvertisement or isAgreeNightAdvertisement) return configured values, only when the user language code is Korean (ko), and return YES for other language codes.

| Property                  | Data Type | Description                                    |
| ------------------------- | --------- | ---------------------------------------------- |
| userId                    | NSString  | User identifier                                |
| token                     | NSString  | Registered tokens                              |
| countryCode               | NSString  | Country code                                   |
| languageCode              | NSString  | Language code                                  |
| pushType                  | ENUM      | Push type                                      |
| isAgreeNotification       | BOOL      | Consent to display notifications               |
| isAgreeAdvertisement      | BOOL      | Consent to display ad notifications            |
| isAgreeNightAdvertisement | BOOL      | Consent to display night-time ad notifications |
| timezone                  | NSString  | Standard time zone                             |
| updateDate                | NSDate    | Date of the latest update                      |

### Receive Push Messages

> A delegate for user code execution can be configured to receive push messages.<br>
> The receiving delegate cannot receive general push messages when application is not running.<br>
> VoIP push messages, received while application is not running, are delivered to the receiving delegate as the application is automatically executed in the background.  

```
@interface AppDelegate () <TCPushDelegate>

@end

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    [TCPushSdk setDelegate:self];

    return YES;
}

@end

// Receive general push messages
- (void)pushSdk:(TCPushSdk *)pushSdk didReceiveAPNsNotificationWithPayload:(NSDictionary *)payload {
    
}

// Receive VOIP push messages
- (void)pushSdk:(TCPushSdk *)pushSdk didReceiveVoIPNotificationWithPayload:(NSDictionary *)payload {

}
```

### Receive Push Action

> When an action of a message received in a user-defined category occurs, it is passed to the delegate.

```
// for all actions except text input action
- (void)pushSdk:(TCPushSdk *)pushSdk
   handleAction:(NSString *)actionIdentifier
       category:(NSString *)categoryIdentifier
        payload:(NSDictionary *)payload {

}

// for text input action
- (void)pushSdk:(TCPushSdk *)pushSdk
   handleAction:(NSString *)actionIdentifier
       category:(NSString *)categoryIdentifier
        payload:(NSDictionary *)payload
       userText:(NSString *)userText {
 
}
```

### Receive Rich Push Message
> To collect receive righ push message, add Notification Service Extension (iOS 10.0+) and extends TCPushServiceExtension to your application. <br>
> **File New > Target > iOS > Notification Service Extension** <br>
> Rich push messages are based on categories and can not be used in duplicate with user categories.


```
#import <UserNotifications/UserNotifications.h>
#import <TCPushSDK/TCPushSDK.h>

@interface NotificationService : TCPushServiceExtension

@end
```

#### Receive Righ Push Message Action
 > Receive actions and messages through the delegate.

```
// for all actions except text input action
- (void)pushSdk:(TCPushSdk *)pushSdk
   handleAction:(NSString *)actionIdentifier
       category:(NSString *)categoryIdentifier
        payload:(NSDictionary *)payload {

}

// for text input action
- (void)pushSdk:(TCPushSdk *)pushSdk
   handleAction:(NSString *)actionIdentifier
       category:(NSString *)categoryIdentifier
        payload:(NSDictionary *)payload
       userText:(NSString *)userText {
 
}
```

### Collect Indicators

> Client sends whether to execute application on receiving and notifying push messages, to a server. Check more on the **Statistics** tab of the console.  

#### Received

> To collect Received Indicators, add Notification Service Extension (iOS 10.0+) to your application.<br>
**File >  New > Target > iOS > Notification Service Extension**

##### Set Analytics information

> Entering the analytics information in the extension's info.plist file automatically collects and transmits the Received indicators.

* Property List<br>
![Remote Notifications](http://static.toastoven.net/toastcloud/sdk/push/ios/analytics_settings_extension.png)<br/>

* Source Code
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>TCPushAnalytics</key>
    <dict>
        <key>AppKey</key>
        <string>INPUT_YOUR_APPKEY</string>
    </dic>
</dict>
</plist>
```

##### Notification Service Extension

> You must extended implementation of TCPushServiceExtension.

```
#import <UserNotifications/UserNotifications.h>
#import <TCPushSDK/TCPushSDK.h>

@interface NotificationService : TCPushServiceExtension

@end
```

#### Opened

> Collecting and sending Opened Indicators are automatically done within SDK.
> Initialization is required <br>
> If you do not initialize, enter the analytics information in the application's info.plist and the Opend indicator will be automatically collected and transmitted.

* Property List<br>
![Remote Notifications](http://static.toastoven.net/toastcloud/sdk/push/ios/analytics_settings_app.png)<br/>

* Source Code
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>TCPushAnalytics</key>
    <dict>
        <key>AppKey</key>
        <string>INPUT_YOUR_APPKEY</string>
    </dic>
</dict>
</plist>
```

### Error Codes

| Error Codes                    | Description                             |
| ------------------------------ | --------------------------------------- |
| TCPushErrorNotInitialized      | Not initialized                         |
| TCPushErrorInvalidParameters   | Error in parameters                     |
| TCPushErrorPermissionDenined   | Failed to acquire authority             |
| TCPushErrorSystemFail          | Failed to register system notifications |
| TCPushErrorNetworkFail         | Failed to receive/deliver via network   |
| TCPushErrorServerFail          | Failed to respond via server            |
| TCPushErrorInvalidUrl          | Invalid URL request                     |
| TCPushErrorNetworkNotReachable | Network not connected                   |
