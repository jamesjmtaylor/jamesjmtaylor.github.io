---
title: AAR pt 6 (Tools)
date: 2018-01-01T05:48:17-08:00
tags: software-engineering aar
---
![Toolbelt](/assets/toolbelt.jpeg)

If you haven't had a chance to read the first entry in the series for context, [you can do so here](/after-action-review-aar/).

Over the course of a professional career you tend to acquire a lot of tools, regardless of the industry.  I wanted to list some of my favorites in this blog entry, as well as some of my lessons learned from each.  I'll go into greater detail for each in the future, but for now I'll just cover some of my major takeaways from each.

* Always lint any mocked JSON.  I use [JSONLint](https://jsonlint.com/)
* If you are doing any kind of network API interaction, test your all your API calls with [Postman](https://www.getpostman.com/).
* You can get the code equivalent of any Postman call in Java or Swift by clicking on the **Code** button.  If exporting Postman calls as Java use **OKHTTP**.  For swift, use **NSURL**.
* In Postman its a best practice to configure settings to erase the cache each time Postman is closed.  That way you can ensure any issues you had in your previous session of Postman don't cross-contaminate your current session.
* In Postman you can have your environment variables configuration save your **clientURL**, **client id**, and **client secret** in the dialogue box for each argument in the Oauth2.0 modal view.
* ALWAYS debug network integration through Postman first, then in the simulator with [Charles](https://www.charlesproxy.com/) running (Charles is a Man-In-The-Middle network debugging program)
* The** Network Profiler** tab in Android Studio 3.0 provides all the functionality of Charles network debugging (view the raw request and response as well as delay time) without the fuss of installing custom SSL certificates on your Android emulators.
* In Windows you can use [Fiddler](https://www.telerik.com/fiddler), which is equivalent to Charles, but free.  A mac version of Fiddler exists, but it's not nearly as easy to setup and use as Charles is.
* Use the application [Network Link Conditioner](http://nshipster.com/network-link-conditioner/) (not normally included on Mac) to simulate a bad connection over localhost.
* MacOS comes with grep installed and can use the terminal to run "grep \[options] \[searchTerm] \[dir]" i.e. "grep -r myFile ."
* MacOS can display it's path variable with "echo $PATH".  You can change it with "sudo vi /etc/paths"
* MacOS can use it's Keystore application to introspect on .jks files, provided you know the password.
* When in MacOS it's best to write .NET Core in VS code and debug with the F5 hotkey.
* For iOS profiling the best tool is the Instruments.app.
* For headless setup on a Mac
  * Setup auto-login
  * Enable SSH access
  * Install OS X Server on developer app (must have a developer account/team)

* [GPG](https://www.gnupg.org/) is a free, fairly widely used Certificate Authority (CA) tool that allows you to create a private and public cert based on your email.  This allows you to send signed and encrypted emails that others can decrypt as long as they are registered GPG users as well.
* Sketch is best for Design creation, while the Zeplin Sketch plugin gives designers a way to provide developer access to an  immutable design 'source-of-truth' that they can export assets from, generate formatting code snippets with, post questions/comments, and view the design revision history.
* In Zeplin, to get the transparent window for overlaying a design over the siumulator click the box icon with the arrow point up and to the right that's located in Zeplins bottom-left.
* Omnigravel is excellent for flow/interaction design.
* Adobe After Effect JSONs can be translated into native iOS (and android) animations through the [Lottie Library](https://airbnb.design/lottie/).
* Whenever possible you should pre-load animation jsons as LottieConfigurations and use them that way.
* Android Studio has an .apk analyzer tool that gives you data on the final .apk size
* [Jigsaw](http://openjdk.java.net/projects/jigsaw/) is a write-once-run-anywhere Java containerization Library (i.e. the machine running the app does not need the JVM pre-installed) for Desktop apps.  Best of all, apps are often smaller than 20mbs (in constrast to electron apps that are as large as 100 mbs or more.
* As of 2018 there is no industry standard for measuring app battery consumption outside of actually testing on physical devices
