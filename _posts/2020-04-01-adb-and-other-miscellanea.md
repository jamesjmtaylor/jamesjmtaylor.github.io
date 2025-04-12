---
title: ADB and other Miscellanea
date: '2020-04-01T08:11:25-07:00'
tags: android
---
![Swinging footbridge](/assets/bridge.jpg)

At Nautilus I’ve been hard at work on a new line of Google-Certified OEM (Original Equipment Manufacturer) touchscreens for our fitness equipment.  This work has meant that I’ve had to become much more familiar with ADB (Android Debug Bridge) and the Android Operating System. 

The first step when developing using ADB is to establish a connection.  This is done either over USB or a wireless network.  Once that is done, you can confirm the connection by executing `adb devices` in the terminal and confirming the presence of the device name.  The next step is to install the Android application package (apk).  This is accomplished by executing `adb install /path/to/apk`.  To ensure that no typos are made when transcribing the path, you can drag and drop the apk itself into the terminal window.  This transcribes the absolute path into the terminal window.  

You can extract an apk from the device by executing `adb pull /path/to/apk`. Any time you pull an apk from a device, you strip the Google Play signature from it. The `adb pull` command also works if the application is an AAB (Android Application Binary).  The difference is that this will generate multiple apk files.  If you need to subsequently install the apks back onto the device you can do so all at once by running `adb install-multiple apk1 apk2 apk3 etc`.  It is important to note that you must include all the apks that you pulled, otherwise you risk creating a corrupted .aab file.

On our SBCs it was initially necessary to circumvent the system-level permissions in order to access the device’s serial bus.  This was because USB (Universal Serial Bus) access is a user-level permission, but general serial bus access is a system-level permission and not normally accessible to the user.  Because Android is at its heart a Linux OS, I could accomplish this by escalating my ADB permissions to root and using `shell` to execute privileged requests. This was done with the following commands:

```shell
adb root 
adb shell setenforce 0 
adb shell chmod 777 /dev/ttyS4
```

While on the topic of permissions, a distinction should be made between Internal and External storage.  In android these terms can describe two different things.  They can refer to a device's internal non-removable memory (hard drive) versus a device's removable media (an SD card, as an example).  They can also refer to the internal app memory (sandboxed to the app's process) versus everything external to that (i.e. the user's photo's directory). Under either definition you need explicit user-level permissions for external memory access.

Communications between android processes and activities  like permission requests normally occur through intents.  Occasionally it is necessary to trigger our applications from the terminal with specific intents and intent extras.  We accomplished this with commands like the one below:

```shell
adb shell am start -a android.intent.action.MAIN --es machineType "BIKE" -n com.nautilus.app/com.nautilus.ui.view.activity.MainActivity
```

This is an example of launching our application and communicating that it should default to “bike” mode.  Intents can serve a wide number of purposes in Android.  Deep links are created by launching intents for a specified package and activity.  Files can be shared as binary streams contained in an intent's EXTRA_STREAM. You can start services through intents as background services. You can pair pending intents with notifications to create foreground services. Finally you can create an intent service that receives an intent, executes, and then finishes automatically. The two service intent options allow you to bind and unbind the service from other objects. Doing this allows the objects to call the services public methods directly instead of relying on a broadcast manager. 

It was sometimes necessary to extract crash logs from the devices while developing our app.  If your application crashes, you can export the crash logs by using `adb logcat > ~/Desktop/logcat.txt`.  This will drop your most recent stack trace into a text file on your development machine’s desktop.  Logcat messages are formatted as "date time PID-TID/package priority/tag: message". Priority is either V/D/I/W/E and stands for Verbose/Debug/Info/Warning/Error. TID stands for "Thread Identifier" and PID stands for "Process Identifier".  They can be the same if there is only one thread in the process.

I hope you’ve found this article useful. Let me know in the comments!

Photo credit to <a style="background-color:black;color:white;text-decoration:none;padding:4px 6px;font-family:-apple-system, BlinkMacSystemFont, &quot;San Francisco&quot;, &quot;Helvetica Neue&quot;, Helvetica, Ubuntu, Roboto, Noto, &quot;Segoe UI&quot;, Arial, sans-serif;font-size:12px;font-weight:bold;line-height:1.2;display:inline-block;border-radius:3px" href="https://unsplash.com/@andreamaraldg?utm_medium=referral&amp;utm_campaign=photographer-credit&amp;utm_content=creditBadge" target="_blank" rel="noopener noreferrer" title="Download free do whatever you want high-resolution photos from Andre A. Xavier"><span style="display:inline-block;padding:2px 3px"><svg xmlns="http://www.w3.org/2000/svg" style="height:12px;width:auto;position:relative;vertical-align:middle;top:-2px;fill:white" viewBox="0 0 32 32"><title>unsplash-logo</title><path d="M10 9V0h12v9H10zm12 5h10v18H0V14h10v9h12v-9z"></path></svg></span><span style="display:inline-block;padding:2px 3px">Andre A. Xavier</span></a>
