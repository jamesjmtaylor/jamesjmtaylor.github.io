---
title: Subscription Management
date: '2019-07-01T07:53:56-07:00'
tags: ios android
---
While working at Nautilus I've had to do a fair amount of subscription management on both iOS and Android platforms.  Each platform has its nuances and gotchas, so I figured I would write a blog post detailing the processes for implementing and testing both.

## iOS

iOS Subscriptions are controlled through Apple App Store Connect.

**_Subscription Creation & Management_**

Subscriptions can be viewed, created, modified, and deleted by selecting the relevant app in App Store Connect. From there, navigate to **Features **in the top navigation bar and select** In-App Purchases** in the left drawer.  Any new subscription must be added to a subscription group.  Users can only have one subscription active from a subscription group at a time. 
To modify subscription pricing select **View all Subscription Pricing** under the **Subscription Prices** section

![iOS Subscriptions](/assets/iosSub0.png)

In the **Subscription Prices** tab of the new screen select **Starting Price** below the Current Prices title:

![iOS Subscriptions](/assets/iosSub1.png)

Tap **Edit Price Change** in the new screen that appears:

![iOS Subscriptions](/assets/iosSub2.png)

Finally select **Recalculate prices for all territories** and follow the instructions.

![iOS Subscriptions](/assets/iosSub3.png)

The product Id that you assign to new subscriptions must be declared & referenced in the SubscriptionService.swift class that you create in the app.  The product ids are build bundle id specific, since available subscriptions are managed by bundle id.   If you need to make the app available in more countries then navigate to the **Pricing and Availability** tab in the left drawer of App Store Connect and modify the countries you want to offer the app in in the **Availability** section.

**_Subscription Testing_**

![iOS Subscriptions](/assets/iosSub4.jpg)

Testing of subscriptions is done from within the app.  The app must be downloaded from Testflight and the test account must be registered in the App Store Connect Testers Group (Labeled **Testers** in the left drawer of the **Users and Access** section of App Store Connect). Users from this list will be prompted to pay for their subscription just like a normal user would, but their accounts only receive simulated Apple Pay subscription charges.

To test international pricing you must change the Country/Region of the Sandbox iCloud account. This can be done by following the steps below:

![iOS Subscriptions](/assets/iosSub5.jpeg)

1. Launch **Settings** from your Home screen.
2. Tap on **iTunes & App Store**.
3. Tap on **Apple ID**.
4. Authenticate with Password or Touch ID, if required.
5. Tap on **Country/Region**.
6. Tap on **Change Country or Region**.
7. Choose a new country or region.
8. Tap on **Next**.
9. Tap on **Agree** to consent to the terms and conditions.
10. Enter your payment details.
11. Enter your billing information.
12. Tap **Next**. You will now be set up in your new country and ready to go.

NOTE: iOS supports the ability to be simultaneously logged into an iTunes account and a sandbox account.  Sandbox accounts can only use the US App Store, so make sure to logout if you need to test international subscriptions.

## Android

Android Subscriptions are controlled through the Google Play Console. 

**_Subscription Creation & Management_**

Subscriptions can be viewed, created, and modified by selecting the relevant app, navigating to **Store Presence** in the left drawer and selecting **In-app products**. From there you can select **Subscriptions** in the top navigation bar.  
You may need to setup a merchant account before the screen is available.  You will also need to add billing permissions to your APK, otherwise the you will receive the screen below:

![Android Subscriptions](/assets/aosSub0.png)

Once you have created a subscription you can control the price (to include locale pricing), billing period, grace period, and free trial period.  You can also add translations that allow you to create locale specific entries with their own descriptions & prices. Be aware that not all languages have an associated country, which means that either the subscription description must omit the price or that the description translation must apply to all the countries for that language.

The product Id that you assign to new subscriptions must be declared & referenced in the SubscriptionManager.kt class that you create in your Android App.  The product ids are build flavor specific, since available subscriptions are managed by appId.  That means a development variant build will NOT have any available subscriptions when you try to access them.

**_Subscription Testing_**

Testing of subscriptions should be done from within an internal build of a staging app in the Google Play Console. This should be done to avoid affecting subscription values for the production app, since subscription settings cannot be decoupled from their associated app id.  The staging app must be downloaded from the internal testing link and the test account must be registered in the Testers group, editable through **Release Management** → **App Releases** → **Internal Test** → **Manage Testers** (See below).  

![Android Subscriptions](/assets/aosSub1.png)

Attempting to download the app from an alternative source (i.e. HockeyApp) will prevent the app from resolving the Google Play in-app purchase SKUs.

Once the tester has opted into the testing program by opening the internal testing Google Play link and has downloaded the app they can test purchasing subscriptions in the same manner that they would in the production version of the app. [Full instructions for testing Google Play Billing can be found here](https://developer.android.com/google/play/billing/billing_testing#test-purchases-sandbox). Note that Google Play will resolve aliased addresses as belonging to the same Google Play account, preventing aliased subscription testing.  Google Play also does not currently support mocking international test accounts.  The best means around this is to create a Google account while using a proxy server that is configured for the country that you want to mock.  You can then set your country as desired by following [Google's guidance on changing your Google Play country](https://support.google.com/googleplay/answer/7431675?hl=en).  Your country can only be changed once per year, so you will need to create a new account for each country.  Each account will also need to be retroactively added to the Einstein Testers group.
