# iOS Swift FCM Push 메시지를 설정 해본 Sample입니다.
iOS FCM(Firebase Cloud Messaging), Push 메시지 최소한의 설정해본 샘플입니다. 미디엄에 글을 쓰고 예제 소스 링크를 위해 만들었습니다.

- [미디엄 링크](https://medium.com/@jang.wangsu/ios-swift-fcm-firebase-cloud-messaging-push-%EB%A9%94%EC%8B%9C%EC%A7%80-%EC%84%A4%EC%A0%95%ED%95%B4%EB%B3%B4%EA%B8%B0-852a9af23b96) 

# 참고 사항
- CocoaPods 로 구성되어있습니다. 
	- Podfile 파일이 있는 JWSFCMSample 폴더 안에서 `Pod install` 을 하셔야됩니다.
	- [CocoaPods 관련 참고 링크](https://github.com/ClintJang/cocoapods-tips)
- GoogleService-Info.plist 파일은 제외하고 소스를 올렸습니다. 
	- 미디엄 링크 내용 중 아래 이미지 처럼 `GoogleService-Info.plist`을 내려받을 수 있는 진행이 있습니다. 해당 파일을 넣어주시면 됩니다. 
	
	<img src='/Image/readme_info_01' width='400' />

# 상세
- AppDelegate.swift

```swift
import UIKit
import Firebase
import FirebaseMessaging
import UserNotifications

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Override point for customization after application launch.
        FirebaseApp.configure()
        Messaging.messaging().delegate = self

        UNUserNotificationCenter.current().delegate = self
        
        let authOptions: UNAuthorizationOptions = [.alert, .badge, .sound]
        UNUserNotificationCenter.current().requestAuthorization(
            options: authOptions,
            completionHandler: {_, _ in })
        application.registerForRemoteNotifications()
        return true
    }

    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        Messaging.messaging().apnsToken = deviceToken
    }
}

extension AppDelegate: UNUserNotificationCenterDelegate {
    func userNotificationCenter(_ center: UNUserNotificationCenter, willPresent notification: UNNotification, withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        print("\(#function)")
    }
}

extension AppDelegate: MessagingDelegate {
    func messaging(_ messaging: Messaging, didReceiveRegistrationToken fcmToken: String) {
        print("Firebase registration token: \(fcmToken)")
        let dataDict:[String: String] = ["token": fcmToken]
        NotificationCenter.default.post(name: Notification.Name("FCMToken"), object: nil, userInfo: dataDict)
    }
}
```
