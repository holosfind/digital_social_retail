![DSR logo](http://cloud.digitalsocialretail.com/img/logo-long-v2.png)

# Digital Social Retail SDK Android Installation Guide
Technical support: support@digitalsocialretail.com

Last production version : 2.0.1 - 14 october 2016


## 1. Introduction

This document is destined to the Android developer of your app and will guide him to install Digital Social Retail SDK in to your android  app. The operation should be really simple, because you just need to add entry points into your build.gradle files , Application Class  , Manifest and Main Activities . No other files will be modified.

Requirements: **Android 4.3 or later**


## 2. Step by Step instructions 

[x] In the **project build.gradle** file, add the maven Social Retail SDK url :

```javascript
allprojects {  
  repositories {  
       ...
       maven {
		...
        url "https://digitalsocialretail.s3.amazonaws.com/sdk/android/prod"
       }
   }
}
```

Synchronize your build.gradle to apply the modifications.

[x] Add to build.gradle file from Module app :

```javascript
dependencies {
   ...
   compile 'com.socialretail.sdk:android-socialretail:2.0.0'
   ...
}
```

Synchronize your build.gradle to apply the modifications.

[x] Create new class inherited from Application class of Android SDK and add the next lines to the OnCreate method. Lets call it **DsrSdk**:

```java
import android.app.Application;
import com.socialretail.sdk.SRBeaconManager;
import org.altbeacon.beacon.startup.RegionBootstrap;

public class DsrSdk extends Application {
   private RegionBootstrap regionBootstrap;
   @Override
   public void onCreate(){
       super.onCreate();
       SRBeaconManager srBeaconManager = SRBeaconManager.getInstance();
       srBeaconManager.SetContext(this.getApplicationContext());
       srBeaconManager.setSettings(this);
   }
}
```

[x] Add the MainApplication class to your android manifest xml file:

```xml
<application
android:name=".DsrSdk"
android:label="@string/app_name"
...
>
```

[x] In the Main Activity class add these imports:

```java
import android.Manifest;
import android.content.DialogInterface;
import android.content.pm.PackageManager;
import android.support.annotation.NonNull;
import android.support.v4.app.ActivityCompat;
import android.support.v4.content.ContextCompat;

import com.socialretail.sdk.SRBeaconManager;

import java.util.ArrayList;
import java.util.List;
```

[x] In the Main Activity class add these fields and method:

```java
private static final int MY_PERMISSIONS_REQUEST = 26;
private static boolean arePermissionGranted = false;
SRBeaconManager srBeaconManager;

public void LoadBeaconScan()
{
   srBeaconManager=SRBeaconManager.getInstance();
   srBeaconManager.mContext=MainActivity.this;
   srBeaconManager.fragmentActivity=this;
   srBeaconManager.loadBeaconScan();
}

```
[x] Override method onPause of your Main Activity class:

```java
@Override
public void onPause() {
   super.onPause();
...
   if(arePermissionGranted)
srBeaconManager.SetForeGroundModePause();
...
}
```

[x] Override method onResume of your MainActivity Class:

```java
@Override
public void onResume() {
   super.onResume();
...
   if(arePermissionGranted){
srBeaconManager.SetForeGroundModeOnResume();
   	srBeaconManager=SRBeaconManager.getInstance();
   	srBeaconManager.mContext=MainActivity.this;
   	srBeaconManager.fragmentActivity=this;
   }

...
}
```

[x] Override method onDestroy of your MainActivity Class:

```java
@Override
protected void onDestroy() {
   super.onDestroy();
...
   if(arePermissionGranted)
       srBeaconManager.cleanup();
...
}
```

[x] Add in the onCreate method of your MainActivity Class:

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
   super.onCreate(savedInstanceState);
   setContentView(R.layout.activity_main);
...
//  Managing of permissions
if(android.os.Build.VERSION.SDK_INT < 23){
   arePermissionGranted = true;
   //call load beacon scan
   this.LoadBeaconScan();

}else {
   List<String> permissionsList = new ArrayList<>();

   if (ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED )
       permissionsList.add(Manifest.permission.ACCESS_FINE_LOCATION);
   if (ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED )
       permissionsList.add(Manifest.permission.ACCESS_COARSE_LOCATION);
   if (ContextCompat.checkSelfPermission(this, Manifest.permission.READ_PHONE_STATE) != PackageManager.PERMISSION_GRANTED )
       permissionsList.add(Manifest.permission.READ_PHONE_STATE);
   if (ContextCompat.checkSelfPermission(this, Manifest.permission.BLUETOOTH) != PackageManager.PERMISSION_GRANTED )
       permissionsList.add(Manifest.permission.BLUETOOTH);
   if (ContextCompat.checkSelfPermission(this, Manifest.permission.BLUETOOTH_ADMIN) != PackageManager.PERMISSION_GRANTED )
       permissionsList.add(Manifest.permission.BLUETOOTH_ADMIN);

   if (permissionsList.size() > 0)
       ActivityCompat.requestPermissions(this, permissionsList.toArray(new String[permissionsList.size()]), MY_PERMISSIONS_REQUEST);
   else {
       arePermissionGranted = true;
       //call load beacon scan
       this.LoadBeaconScan();
   }
}
...
}
```

[x] Implement onRequestPermissionsResult method in your MainActivity Class:

```java
@Override
public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
   boolean tmpPermissionGranted = true;
   switch (requestCode){
       case MY_PERMISSIONS_REQUEST:
           if (grantResults.length > 0){
               for (int permission: grantResults ) {
                   if (permission != PackageManager.PERMISSION_GRANTED){
                       tmpPermissionGranted = false;
                       break;
                   }
               }
           }else {
               //  Denied
               tmpPermissionGranted = false;
           }

           if (!tmpPermissionGranted){
               final android.support.v7.app.AlertDialog.Builder builder = new android.support.v7.app.AlertDialog.Builder(this);
               builder.setTitle("Permission");
               builder.setMessage("This permission is required to use the Social Retail SDK.");
               builder.setPositiveButton(android.R.string.ok, new DialogInterface.OnClickListener() {
                   @Override
                   public void onClick(DialogInterface dialog, int which) {
                       finish();
                   }
               });
               builder.setCancelable(false);
               builder.show();
           }else{
               //call load beacon scan
               this.LoadBeaconScan();
               arePermissionGranted = true;
           }
           break;
       default:
   }
}
```

[x] Add permissions to Android manifest under tag <manifest > :

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
   package="...">
...
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.VIBRATE" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
<uses-permission android:name="android.permission.SET_DEBUG_APP" />
<uses-permission android:name="com.app.aircaraibes.permission.MAPS_RECEIVE" />
<uses-permission android:name="com.google.android.providers.gsf.permission.READ_GSERVICES" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
<uses-permission android:name="android.permission.BLUETOOTH" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
...
</manifest>
```

[x] Please attach also contents in the build.gradle (Module app) like the Reference application DemoApp.

```javascript
android {
 ...
 packagingOptions {
   exclude 'META-INF/DEPENDENCIES'
   exclude 'META-INF/NOTICE'
   exclude 'META-INF/LICENSE'
   exclude 'META-INF/LICENSE.txt'
   exclude 'META-INF/NOTICE.txt'
 }

 defaultConfig{
	...
	minSdkVersion 18
	targetSdkVersion 23
	multiDexEnabled true
	...
 }
}
```

Synchronize your build.gradle to apply the modifications.

[x] In your styles.xml file, in the style section used by your application theme (defined in your AndroidManifest.xml, in the <application> section, in the android:theme value), you have to add these 2 item tags and to make inherit your theme from AppCompat theme:

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
   <!-- Customize your theme here. -->
   ...
   <item name="windowActionBar">false</item>
   <item name="windowNoTitle">true</item>
   ...
</style>
```
Congratulations, you already integrated Digital Social Retail SDK in to your application.   -

## Test your app

To do so, you need to have one Social Retail beacon with you, configured with a notification message, near your device. Make sure to have internet connexion in your device and Bluetooth activated. Launch your app, accept to receive notifications and after 1-5 seconds, you should receive the notification. Then tap on ok and you should see the Ad in full screen. Tap on the button Cancel in the header and the Ad should disappear.

If you need any support, feel free to contact our IT teams by simply sending them a email to support@digitalsocialretail.com

All the best
Digital Social Retail R&D team
