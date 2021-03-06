# Microsoft Endpoint Manager - Android troubelshooting 

**Quick blog about how to troubelshoot Microsoft Endpoint Manager managed Android devices**

This blog is about troubelshooting Android devices managed by Microsoft Endpoint Manager(MEM). The blog post provide information on how you can use the Android Debug Bridge(ADB) to get a Unix shell on a Android device. 

This can be usefull for:
- Troubelshooting issues.
- Collecting logs.
- Getting app package names.
- Getting a more in deep understanding of the inner working of:
    - Android(Build on top of the Linux Kernel).
    - Android management.

Personal goal of this blog: Feature reference and documentation.


## Enable usb debugging ##
For devices that are enrolled into MEM as a Corporate-owned, fully managed user device or Corporate-owned dedicated device debugging need to be enabled. Debugging is disabled by default for these enrollment senarios.


**Configuration MEM:**
Enabeling debugging can be done by: Creating a Device Configuration profile were we can allow Debugging features. 

![MEM DebugAllow](/Images/DebugAllow.JPG)

_Device Configuration Profile -> Android Enterprise -> Device Restriction -> General
-> Debugging features : Allow_

**Android Device: Enable developer mode and USB debugging:**
We need to enable developer mode on the Android phone to to enable debugging.
Depending on the Android/API version the location is slightly different. 
Good source on how to enable developer mode and enable USB debugging : https://developer.android.com/studio/debug/dev-options

In general it is:
- Finding the build number of the phone in the About Phone menu and tap 7 times to enable developer mode
- In the developer option menu find USB debugging and enable. 

We can now connect the phone via USB to your device.

## Use ADB ##
**Connect ADB:**
Android Debug Bridge(ADB) is a command line tool which can be used to communicate with a Android Device.
Android Debug Bridge(ADB) is part of the Android Studio SDK platform tools and can be downloaded from: 
https://developer.android.com/studio/releases/platform-tools

When downloaded we can extract the tools and use the adb.exe. First we want to check if the adb tool detect our Android phone. 
This can be done by: `adb devices`

![adb devices](/Images/adbdevices.JPG)

We can start a unix shell by: `adb shell`

![adb shell](/Images/adbshell.JPG)

Note: The context of the adb shell is not rooted. Permissions are the same context as using your phone. 
Apps on the Phone will have a unique UID: unique linux user context. Therefore we do have limited permissions and cannot access all folders, app data or services as we simply do not have permissions to do so.


## Usefull senarios ##
Hereby a list of usefull things you can do know we are having a shell access. 

**Collect logs**: Reproduce the senario were you want to collect logs for(for example run a app): And run: `Adb logcat -v > CollectedLOG.txt`
please note: do **not run** above command in a already established shell as above command remotely gather the log info.

Example log snipped: In below log snipped of CollectedLOG.txt we can see that the Intune App is on-screen as the ActivityManager is starting the app and is loading the app screens/pages. 

![Open Microsoft Intune App on Android](/Images/applog2.JPG)

**List of all apps including package name**
Usefull for finding package name for reference in MEM for example uninstall particullary out of the box user of system apps from MEM.

- First start a shell by: `adb shell` In the shell run: `pm list packages`
- Oneliner without the need to setup the shell first: `adb shell pm list packages`

Example output of pm list packages:

![PM List](/Images/pmlist.JPG)


**Additional usefull examples from the Shell**

- Press Home button: `adb shell input keyevent 3`
- List of TCP connectivity: `adb shell netstat`
- Copy file from Computer to Phone: `adb push [source] [destination]`
- Copy file from Phone to Computer:  `adb pull [device file location] [local file location]`
- Uninstall app: `pm uninstall -k --user 0 Package Name`