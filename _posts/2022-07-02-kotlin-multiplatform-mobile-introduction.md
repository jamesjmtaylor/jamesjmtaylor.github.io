---
title: Kotlin Multiplatform Introduction
date: '2022-07-02T07:24:47-07:00'
tags: kmp android ios kotlin
---
![Kotlin Multiplatform Mobile](/assets/kmm.png)

This is the first in a multi-part series on Kotlin Multiplatform.  Kotlin Multiplatform (KMP) "is an SDK for iOS and Android app development \[that] offers all the combined benefits of creating cross-platform and native apps." (<https://kotlinlang.org/lp/mobile/>).  I'm currently following the [kotlinlang.org KMP tutorial](https://play.kotlinlang.org/hands-on/Networking%20and%20Data%20Storage%20with%20Kotlin%20Multiplatfrom%20Mobile/01_Introduction) to implement a mobile iOS and Android app.  

Like the tutorial, the application will retrieve data over the internet from a public API, save it in a local database, and display it in a list in the application.  I will implement business logic and data access layers only once in the KMP module, while the UI of both applications will be natively written in Jetpack Compose and SwiftUI. 

 Unlike the tutorial, I won't be using the SpaceX API, which provides public access to information about SpaceX rocket launches.  Instead, I'll be using the Army's brand new [ODIN API ](https://odin.tradoc.army.mil/WEG) for a reboot of my WEG applications.  The ODIN API provides in-depth information about a wide array of military equipment.  I've always found that tweaking a tutorial just a little bit helps cement the underlying lessons a little better. It makes following the tutorial less about copy-and-paste and more about improvisation to figure out how something really works.  

I plan to break this series into four parts:

* Implementing the Database layer
* Implementing the Networking layer
* Writing and integrating the Jetpack Compose view.
* Writing and integrating the SwiftUI views.

I've already started work on the Database layer, but it's not quite complete as of this publication, so I figured I'd postpone it until the next full blog entry.  

Until next time, I hope you have a happy 4th of July.
