---
title: AAR pt 5 (Android)
date: 2017-11-01T12:20:03.000Z
tags: android aar kotlin
---
![Android](/assets/android.png)

If you haven't had a chance to read the first entry in the series for context, [you can do so here](/post/after-action-review-aar/).  Now, about Android.  Of the two native platforms (iOS & Android) I've found Android far and away to be the more challenging of the two.  It's more verbose, more error-prone, and less standardized.  Google went a long way at Google I/O 2017 in resolving the first two with official support of Kotlin, and the last one with the adoption of the MVVM architecture pattern.  But it still leaves a lot to be desired.  Keep in mind that this is also coming from a self-admitted Google fanboy.  I've got the Google Pixel, the Motorola 360 v2, and have been developing Android for almost 4 years now (compared to 2 for iOS).  Regardless of it's complexity, I have to say that I enjoy developing in Android more, in my own sick, masochistic way.  It forces you to consciously consider the consequences of the code that you write, rather than simply churning it out like you can do in iOS.  But enough of my postulations, on to the lessons learned:

* If your "onPostExecute()" args don't match the "doInBackground()" outputs of an AyncTaks, the function will never be called!
* To blur a view from a fragment use "this.getView().getRootView() to get the activity's view, then get a Bitmap from that view, blur the bitmap, convert it to a drawable and set it as the original view's background.  In order to cover the toolbar and the tab bar use "Visibility = .GONE"  on both and present the blur fragment inside a FrameLayout that covers the entire width and height of the activity view.
* Stopping the emulator in a breakpoint when debugging an app also stops the emulator system clock, leading to odd artificial timing errors if you're trying to debug timing issues between different instances of the app on different devices.  
* The android equivalent of the iOS AlertDialogue is cleverly enough called AlertDialogue.
* In order to get the same Layout Inspector in Android Studio as in Xcode during run-time, go to the **MenuBar > Android >** **Layout Inspector**
* You can cast a context into an activity, just make sure it's the right activity context first!
* As of API 27 you must explicitly declare a notification channel for each notification, otherwise you will receive a "Developer warning for package com.google.android.apps.messaging" toast.  Because this is an SDK 27 and later issue, you must encapsulate your notification channel declaration in a "`if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {`"conditional block.
* The HttpURLConnection from the java.net library has a defect where PATCH commands are not correctly handled.  OpenJDK has labeled this as [a "Won't Fix" bug](https://bugs.openjdk.java.net/browse/JDK-7016595).  Because of this, it's recommended that you use the OKHTTP library instead, which has better mocking support for unit testing anyway. 
* When you add actions to a notification, the only way to tell them apart is to assign the intent associated with each action an \`action\` string.  This is because intent extras are shared between all the actions and the notification itself.
* The `final` key word means that you can't change the variable within it's scope.  This does mean that it's fine to use within loops.
* Since 2015 Google has had a Data-Binding Library that allows your xml to have fields that are direct observers of whatever java attributes you declare in the xml.  It's not commonly used however due to the added complexity it lends to XMLs over the more traditional 'findViewById' approach.
* The closest analogue Android has to an iOS stackView is it's LinearLayout
* You cannot use .svg's directly.  Instead, save them to your Desktop and use Android Studio to create a new .svg image asset by right-clicking on the "drawables" package.
* Vector graphics in Android are only supported in OS 21 and up.
* You can use vector graphics for an imageButton, just set the background tint to be the clear color.
* You can auto-generage methods with the **CMD+N**.
* **CMD+N > Override** allows you to autogenerate any public/protected method your class extends or implements.
* You can delcare an onClickListener for a button in a seperate function and then just set that button's onClickListener to the function in onCreateView, making it cleaner.
* ActionBar/Toolbar menu items are the same as the iOS navigationBar buttons, just set toDisplay="Always" in xml
* The Android toolbar can be assigned a custom xml just like any other element.  In it you can change the buttons, background colors, etc.  Just replace the toolbar in the activity layout file with your own and change it's own children.
* If you don't return false in Activity's "onOptionsItemSelected" then the fragment's implementation of that method will never fire.
* You can call a static method from a non-static method but not vice-versa.
* You can reset the adapter everywhere except recyclerViewListClicked, use "myAdapter.notifyItemChanged(position)" there instead.
* Don't forget to call "adapter.notifyLayoutChanged()" from the parent activity/fragment after handling a recyclerCell click.
