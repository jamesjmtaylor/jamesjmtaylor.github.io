---
title: Rooting an Android emulator
date: '2025-05-06T12:00:00-08:00'
tags: android
---
![Rooted](/assets/rooted.jpg)

I recently had to verify that a piece of security software correctly identified an android device as rooted.  This of course raises the question, "how do you configure a device to be "rooted"?" The instructions and software vary by device, but if you're just trying to confirm the detection of a device's rooted status, the easiest method is to root an Android Studio emulator.  This is because there are no manufacturer-specific safeguards to prevent you from rooting the emulator and you don't have to worry about irrevocably corrupting actual hardware during the rooting process.  So without further preamble, the instructions!

In order to root your emulator, you'll need to download the following softare:

* [Magisk APK](https://magiskmanager.com/downloading-magisk-manager/)
* [rootAVD](https://github.com/newbit1/rootAVD/archive/refs/heads/master.zip)

You will also need to install ADB, which can be done in Android Studio directly by following the steps below:

1. Open Android Studio
2. Click on "Tools" on the top bar
3. Select SDK Manager
4. In the Center Box, select the "SDK Tools" tab
5. Scroll down to "Android SDK Platform-Tools"
6. If already installed, skip to next step
7. If no checkmark / not already installed, checkmark the box and hit "apply".
8. Complete the process to install SDK Platform-Tools

![Step 0](/assets/root0.png)

Next, you'll need to add "emulator" and "adb to your PATH variable:

1. Open a Finder window
2. Press Command + Shift + G then press Enter to be navigated to the root folder of your mac
3. Open "Users" directory
4. Open your User directory (example: ab1234)
5. Press Command + Shift + Period (".") to show hidden files
6. Open .zshrc in a text editor (double click)
7. Copy/paste the following lines to this file:
8. export PATH=$PATH:/Users/<USERNAME>/Library/Android/sdk/platform-tools/   /*adds adb to path*/
9. export PATH=$PATH:/Users/<USERNAME>/Library/Android/sdk/emulator/         /*adds emulator CLI to path*/

![Step 1](/assets/root1.png)

For the remainder of this tutorial, you will want to open 3 Terminal instances which will be used for the following:

A: Emulator
B: Assorted Commands to verify the next step(s) are ready
C. rootAVD

If you haven't already, download Magisk and rootAVD as seen above. Unzip the rootAVD .zip file. Relocate the folder to a more permanent location if desired. From Terminal A or B, run the command "emulator -list-avds". You should see the names of all the emulators you've created in Android Studio:

![Step 2](/assets/root2.png)

From Terminal A, run the command "emulator -avd <Name of Emulator you wish to root>" to start the emulator.

![Step 3](/assets/root3.png)

After the Emulator has started, drag-and-drop the Magisk apk from your download folder to the emulator. Open Magisk from your apps list and tap "install" on the App tab (NOT the Magisk tab)

![Step 4](/assets/root4.png)

You may be prompted to authorize unknown sources in order to install. Click yes. Swipe back. You may be prompted to install an update. Confirm that you are not root by looking at the bottom of the Magisk screen. There should be a "Superuser" button that is greyed out, indicating you do not have root access.

![Step 5](/assets/root5.png)

In Terminal B, confirm that adb is in Path and can connect to your running emulator with the command "adb devices". A "List of devices attached" should be displayed, with only one result of "emulator-XXXX", where XXXX is a 4-digit port code. Ex: "emulator-5554"

![Step 6](/assets/root6.png)

In Terminal C, navigate to the folder where you saved the contents of rootAVD.zip. Still in Terminal C, confirm that rootAVD runs and detects your emulators by executing "./rootAVD.sh ListAllAVDs". You should see a list of lines describing your emulators and the commands you would run to root them (which you can copy/paste).

![Step 7](/assets/root7.png)

Example: `./rootAVD.sh ~/Library/Android/sdk/system-images/android-33/google_apis/arm64-v8a/ramdisk.img`
Refers to an emulator with the following configuration:

* android-33: The API / SDK version installed on this emulator
* google_apis_playstore / google_apis: Source used to install your API / SDK. Useful for helping you differentiate one emulator from another.
* arm64-v8a: The type of processor this emulator uses, which can help you match rootADV devices to Android Studio devices. Pixels use arm64.

To summarize the example above, a Pixel 5 emulator is running API 33 downloaded from Google APIs.

Find the corresponding candidate command for the (now currently running in Terminal A) emulator you wish to root (In yellow, previous screenshot). Using the emulator from the example above, you would execute:

```bash
./rootAVD.sh ~/Library/Android/sdk/system-images/android-33/google_apis/arm64-v8a/ramdisk.img
```

Copy/Paste the command above, edit it for the emulator of your choice, and then hit enter.

![Step 8](/assets/root8.png)

The emulator should shut down. It may first pop-up a window asking if you wish to save state for next boot. Select No.

![Step 9](/assets/root9.png)

Open the Terminal where the emulator was running. Restart the emulator by running the previous command (simply press "up" then "enter" on the keyboard)

* If the device successfully restarts, fantastic!
* If the device black-screens, wait a minute. It sometimes take a bit before it boots.
Another possibility is that the emulator is active but the device is "off". Press the power button at the top of the emulator's right-side menu.

Once the device has restarted, open Magisk. If the "Superuser" tab is no longer greyed out, you have successfully rooted your device!

![Step 10](/assets/root10.png)

Photo by <a href="https://unsplash.com/@deedeedss?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">DeeDee Wang</a> on <a href="https://unsplash.com/photos/grayscale-photo-of-tree-roots-3Ck1ppnf-6c?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>