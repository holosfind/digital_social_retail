![DSR logo](http://cloud.digitalsocialretail.com/img/logo-long-v2.png)

# Digital Social Retail SDK Ios Installation Guide
Technical support: support@digitalsocialretail.com

Last production version : 2.1.1 - 10 april 2016


## 1. Introduction

This document is destined to the iOS developer of your app and will guide him to install Digital Social Retail SDK in to your iOS app. The operation should be really simple, because you just need to add entry points into your AppDelegate.h, AppDelegate.m and info.Plist files. No other files will be modified.

Requirements: 
  - Xcode 6 or later
  - The SDK is compatible with iOS 7.1 or later

## Getting started

[x] Download and Unzip this file : <a>Download</a>
It contains 2 files:
- **SocialRetailSRSDK.framework**: this file contains the public headers that will be used to integrate the sdk in to your application.
- **SocialRetailSRSDK.bundle**: This file contains the settings and the Ad view file.

[x] Open your Xcode project

[x] Drag and drop those 2 files inside your project root. In the popup, choose “Copy items if needed”

[x] Build settings: Go to your application Targets ->Build Settings ->Other Linker Flags and set it to: *-all_load -ObjC* like indicated in this screenshot:
![DSR build settings](res/build-settings.png)

## info.Plist settings

[x] ]Add these rows :

- **NSLocationAlwaysUsageDescription** : <put your text, You can customise the text of the field NSLocationAlwaysUsageDescription>
- **Required background modes > Item 0 : App downloads content from the internet**
- **App Transport Security Settings > Allow Arbitrary Loads : YES**

Your info.plist should looks like:
![DSR build settings](res/info-plist.png)

## Modifications to AppDelgate.h

[x] Import the SDK headers: Add these 3 imports:

```Objective-C
#import <SocialRetailSRSDK/SRBeaconDelegate.h>
#import <SocialRetailSRSDK/SRBeaconManager.h>
#import <SocialRetailSRSDK/SRWebViewController.h>
```

[x] Set the protocol SRBeaconDelegate like this:

```Objective-C
@interface AppDelegate : NSObject <…,SRBeaconDelegate>
```

## Modifications to AppDelegate.m

[x] Main entry point:

```Objective-C
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    //Add these lines in the beginning of this method
    [[SRBeaconManager sharedManager]startBeconDetection];
    [[SRBeaconManager sharedManager]setBeaconDelegate:self];
    if ([UIApplication instancesRespondToSelector:@selector(registerUserNotificationSettings:)]) {
        [[UIApplication sharedApplication] registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeAlert|UIUserNotificationTypeSound categories:nil]];
    }
…
}
```


[x] Entry point for localNotifications in the application:

```Objective-C
-(void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
    //Add these lines in the beginning of this method    
    UIApplicationState state = [application applicationState];
    NSDictionary * notifDict= notification.userInfo;
    [[SRBeaconManager sharedManager]showNotificationWithUserInfo:notifDict state:state];
    …
}
```

[x] Link the AD Web View in your application root. To do so, implement your showWebViewController method like this:

```Objective-C
-(void)showWebViewController:(SRWebViewController *)webViewController
{
    if([[[[UIApplication sharedApplication]delegate]window].rootViewController isKindOfClass:[UINavigationController class]])
    {
        UINavigationController *navController=(UINavigationController*)    [[[UIApplication sharedApplication]delegate]window].rootViewController;
        
        [navController pushViewController:webViewController animated:YES];
    }
    else
    {
        UIViewController *navController=(UIViewController*)    [[[UIApplication sharedApplication]delegate]window].rootViewController;
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
      …
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

```c#
- (void)applicationWillTerminate:(UIApplication *)application {
    //Add this line in the beginning of this method    
    [[SRBeaconManager sharedManager]willTerminate];
}
```

[x] Stop the location when the app goes to background:

```Objective-C
- (void)applicationDidEnterBackground:(UIApplication *)application {
    //Add this line in the beginning of this method    
    [[SRBeaconManager sharedManager]stopLocation];
}
```c#

## Miscellaneous
If you get this error : *...SocialRetailSRSDK.framework/SocialRetailSRSDK(WebServiceManager.o)' does not contain bitcode*, You must rebuild it with bitcode enabled (Xcode setting ENABLE_BITCODE), obtain an updated library from the vendor, or disable bitcode for this target. for architecture arm64 clang: error: linker command failed with exit code 1 (use -v to see invocation)
Just change Enable bitcode from Yes to No


-   Congratulations, you already integrated Digital Social Retail SDK in to your application.   -

## Test your app

To do so, you need to have one Social Retail beacon with you, configured with a notification message, near your device. Make sure to have internet connexion in your device and Bluetooth activated. Launch your app, accept to receive notifications and after 1-5 seconds, you should receive the notification. Then tap on ok and you should see the Ad in full screen. Tap on the button “<” in the header and the Ad should disappear.

Lock your device, wait few seconds and unlock it. Then you should see your app icon in the bottom left of the screen. You can now close the app.

If you need any support, feel free to contact our IT teams by simply sending them a email to support@digitalsocialretail.com or calling this number: +33(0)1 73 54 75 00


All the best
Digital Social Retail R&D team
