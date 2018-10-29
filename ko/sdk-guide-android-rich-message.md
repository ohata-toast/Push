## Notification > Push > Android SDK 가이드 > Rich message
Push SDK를 적용하면 풍부한 정보를 담은 알림을 사용자에게 노출할 수 있습니다.

## 지원하는 리치 메시지 기능
### 버튼
* 알림에 아래 기능을 사용할 수 있는 버튼을 추가할 수 있습니다.
    * 알림 제거 : 현재 알림을 제거합니다.
    * 앱 열기 : 앱을 실행합니다.
    * URL 열기 : 특정 URL로 이동합니다.
        * Custom scheme을 이용한 Activity/BroadcastReceiver 이동 가능
    * 답장 전송 : 알림에서 바로 답장을 보냅니다.
        * 안드로이드 7.0(API 레벨 24) 이상에서만 사용 가능합니다.

### 미디어
* 알림에 미디어를 추가할 수 있습니다.
    * 이미지 : 알림에 이미지를 추가합니다. (내부, 외부 이미지 모두 지원)
    * 그 외 : 그 외의 미디어(동영상, 소리 등)는 지원하지 않습니다.

### 큰 아이콘
* 알림에 큰 아이콘을 추가할 수 있습니다. (내부, 외부 이미지 모두 지원)

### 그룹
* 같은 키의 알림들을 하나로 묶습니다.
    * 안드로이드 7.0(API 레벨 24) 이상에서만 사용가능합니다.

## ToastPushMessage
- Push 제공자별로 다른 데이터 타입을 통합한 데이터 구조입니다.
- TOAST Push 서버에서 발송한 알림의 제목, 내용 외에 리치 메시지가 담겨 있습니다.
- 아래 입력 타입을 지원합니다.
    - GCM(Google Cloud Messaging) : Bundle
    - ADM(Amazon Device Messaging) : Bundle
    - Tencent : XGPushTextMessage

### Notification 으로 변환
- 사용자는 ToastPushMessage의 데이터를 이용해서 직접 Notification 객체를 만들 수 있습니다.
- Push SDK에서는 좀 더 편하게 변환할 수 있도록 NotificationConverter를 제공합니다.
- NotificationConverter에 Extender를 추가해서 Notification을 사용자 정의할 수 있습니다.
    - 추가된 Extender는 NotificationConverter의 내부 처리가 완료된 이후에 추가된 순서대로 처리됩니다.
- 예제)

```java
final ToastPushMessage message = ToastPushMessage.fromBundle(bundle);
final NotificationManagerCompat manager = NotificationManagerCompat.from(context);
final NotificationConverter converter = new NotificationConverter("YOUR_NOTIFICATION_CHANNEL", message);
// Extends NotificationCompat.Builder when you need
converter.addExtender(new NotificationCompat.Extender() {
    @Override
    public NotificationCompat.Builder extend(NotificationCompat.Builder builder) {
        // Extends builder
        // ex) return builder.setAutoCancel(false);
        return builder;
    }
});
converter.convert(context, new NotificationConverter.ConvertCallback() {
    @Override
    public void onConvert(int notificationId, @NonNull Notification notification) {
        manager.notify(notificationId, notification);
    }
});
```