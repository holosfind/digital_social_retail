![DSR logo](http://cloud.digitalsocialretail.com/img/logo-long-v2.png)

# Digital Social Retail iOS SDK Installation Guide. (Objective-C)
Technical support: support@digitalsocialretail.com

Last production version : 2.2.3 - 19 July 2017

## 1. Introduction

This document is destined to the iOS developer of your app and will guide him to install Digital Social Retail SDK in to your iOS app. The operation should be really simple, because you just need to add entry points into your AppDelegate.h, AppDelegate.m and info.Plist files. No other files will be modified.

Requirements: 
- Xcode 6 or later
- The SDK is compatible with iOS 7.1 or later

## Getting started

[x] Download and Unzip this file : [Download](res/Digital_Social_Retail_SDK_iOS_v2.2.3.zip)

It contains 1 file:
- **SocialRetailSRSDK.framework**: this file contains the public headers that will be used to integrate the sdk in to your application.

[x] Open your Xcode project

[x] Drag and drop SocialRetailSRSDK.framework file in embedded binaries under target of your project. In the popup, choose “Copy items if needed”
like indicated in this screenshot:
![DSR import framework](res/importFramework.png)

[x] Build settings: Go to your application Targets ->Build Settings ->Other Linker Flags and set it to: *-all_load -ObjC* like indicated in this screenshot:
![DSR build settings](res/build-settings.png)

## info.Plist settings

[x] ]Add these rows :

- **NSLocationAlwaysUsageDescription** : < put your text, You can customise the text of this key >
- **NSLocationWhenInUseUsageDescription** : < put your text, You can customise the text of this key >
- **NSLocationAlwaysAndWhenInUseUsageDescription** : < put your text, You can customise the text of this key >
- **NSBluetoothPeripheralUsageDescription** : < put your text, You can customise the text of this key >
- **App Transport Security Settings > Allow Arbitrary Loads : YES**
- **Required background modes > Item 1 : App downloads content from the network**

Your info.plist should looks like:
![DSR build settings](res/infoPlist.png)

## Modifications to AppDelgate.h

[x] Import the SDK header: Add these import:

```Objective-C
#import <SocialRetailSRSDK/SocialRetailSRSDK.h>
```

[x] Set the protocol SRBeaconDelegate like this:

```Objective-C
@interface AppDelegate : NSObject <…,SRBeaconDelegate>
```

## Modifications to AppDelegate.m

[x] Main entry point: Here you need to put your Social Retail API Token. To get api token, just <a href="https://cloud.digitalsocialretail.com" target="_blank">Login</a> to your account and click on “My account” and you will see API Token

```Objective-C
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    //Add these lines in the beginning of this method

        [[SRBeaconManager sharedManager]setBeaconDelegate:self];
        [[SRBeaconManager sharedManager] setWSToken:@"Enter-Social-Retail-API-Token"];
        [[SRBeaconManager sharedManager]startBeaconDetection];

        if ([UIApplication instancesRespondToSelector:@selector(registerUserNotificationSettings:)]) {
            [[UIApplication sharedApplication] registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeAlert|UIUserNotificationTypeSound categories:nil]];
        }

        return YES;

}
```


[x] Entry point for localNotifications in the application:

```Objective-C
-(void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
    //Add these lines in the beginning of this method    
    UIApplicationState state = [application applicationState];
    NSDictionary * notifDict= notification.userInfo;
    [[SRBeaconManager sharedManager]showNotificationWithUserInfo:notifDict state:state];
}
```

[x] Link the AD Web View in your application root. To do so, implement your showWebViewController method like this:

```Objective-C
-(void)showWebViewController:(SRWebViewController *)webViewController{
    if([[[[UIApplication sharedApplication]delegate]window].rootViewController isKindOfClass:[UINavigationController class]]){
        UINavigationController *navController=(UINavigationController*)[[[UIApplication sharedApplication]delegate]window].rootViewController;
        [navController presentViewController:webViewController animated:YES completion:nil];
    }
    else{
        UIViewController *navController=(UIViewController*)[[[UIApplication     sharedApplication]delegate]window].rootViewController;
        [navController presentViewController:webViewController animated:YES completion:^{
        }];
    }
}
```

[x] Set application state for applicationWillResignActive:

```Objective-C
- (void)applicationWillResignActive:(UIApplication *)application {
    //Add this line in the beginning of this method
    [[SRBeaconManager sharedManager]willResignActive];
}
```

[x] Set application state for applicationDidBecomeActive:

```Objective-C
- (void)applicationDidBecomeActive:(UIApplication *)application {
    //Add this line in the beginning of this method
    [[SRBeaconManager sharedManager]didBecomeActive];
}
```

[x] Set application state for applicationWillTerminate:

```Objective-C
- (void)applicationWillTerminate:(UIApplication *)application {
    //Add this line in the beginning of this method    
    [[SRBeaconManager sharedManager]willTerminate];
}
```

## Miscellaneous
If you get this error : *...SocialRetailSRSDK.framework/SocialRetailSRSDK(WebServiceManager.o)' does not contain bitcode*, You must rebuild it with bitcode enabled (Xcode setting ENABLE_BITCODE), obtain an updated library from the vendor, or disable bitcode for this target. for architecture arm64 clang: error: linker command failed with exit code 1 (use -v to see invocation)
Just change Enable bitcode from Yes to No


-   Congratulations, you already integrated Digital Social Retail SDK in to your application.   -

## Test your app

To do so, you need to have one Social Retail beacon with you, configured with a notification message, near your device. Make sure to have internet connection in your device and Bluetooth activated. Launch your app, accept to receive notifications and after 1-5 seconds, you should receive the notification. Then tap on ok and you should see the Ad in full screen. Tap on the button “<” in the header and the Ad should disappear.

Lock your device, wait few seconds and unlock it. Then you should see your app icon in the bottom left of the screen. You can now close the app.

If you need any support, feel free to contact our IT teams by simply sending them a email to support@digitalsocialretail.com


All the best
Digital Social Retail R&D team

## This are some optional methods : 

```Objective-C

1. [[SRBeaconManager sharedManager] setUuidScan:@"your-uuid-string"]; 
    // Description : Set UUID string of beacon.
2. NSDictionary *nearestBeaconsDict = [[SRBeaconManager sharedManager] getNearestBeaconInfo];
    // Description : Get nearest beacon.
3. [[SRBeaconManager sharedManager] setDebugMode:YES];
    // Description : Set debug YES or NO.
```
