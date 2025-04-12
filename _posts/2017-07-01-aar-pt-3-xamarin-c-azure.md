---
title: 'AAR pt 3 (Xamarin/C#/Azure)'
date: 2017-07-01T11:58:08.000Z
tags: xamarin c# web aar
---
If you haven't had a chance to read the first entry in the series for context, [you can do so here](/after-action-review-aar/). We do a fair amount of .NET development over at Steelcase.  When I first joined the mobile development team we seriously considered doing our mobile applications in Xamarin.  We eventually decided against it since Steelcase is a very heavily design driven company, and Xamarin just wouldn't provide the flexibility that a purely native approach does.  But in the spirit of giving Xamarin a fair shake we spent a sprint working in it to get an idea of it's capabilities.  Below are the lessons I learned, as well as some I've come to learn after working with Steelcase's C#/Azure backend.

* To issue adb commands in Xamarin Studio go to the menu bar and select **Tools** > **SDK Command Prompts.**
* To find a package name right click the project and select **options**> **Android Applications** > **Package Name.**
* To add unit tests, add a project, select **platforms**, **Unit Tests**, then in the unit tests project add a reference to the main project.
* To run unit tests, select the project file and emulator and press the play button
* To add UI tests, do the same as above except with a UI Test project, then **View** > **Pods** > **Unit Tests**, right click the tests and select **add project**
* When integrating AAD (Azure Active Directory) services Microsoft restricts services with 401 (unauthorized) responses.
* C# naming convention is Pascal Case for public methods, classes, and properties.  Private fields, classes, and methods are all camel case.
* An Azure cloud service can have one or more worker roles.  Info for that worker's run-time settings are in **ServiceDefinition.csfg**.  It includes endpoints, roles required, and vm size.
* In VS17 you can configure whether the MVC is run locally or on the cloud by right-clicking the **project** > **properties** > **development** > **service configuration** > **local/cloud**
* In VS17 you can debug by clicking the gray area to the left of the line numbers to create a breakpoint.
* In VS17, to keep the command terminal open when running a command line project, run with CTRL+F5.
* An Azure IoTHub is really just an EventHub with a device registry bolted on.
* To view, modify, and debug blobs use **Azure Storage Explorer**, which can be downloaded for free from the internet.
* Every Azure IoTHub event has 3 buckets: a **properties bucket** (can't change), an **application bucket** (read as a header), and a **payload bucket** (read as JSON, can change).
* Each Azure event processor has its own marker, so one event processor consuming from a partition does NOT prevent another from consuming it.
* .NET Core allows you to deploy to Linux and MacOS, which also means that you can create .NET Core Docker containers.  In exchange, you lose a lot of .NET Framework libraries.
* In C# the `wait` keyword will block further progress synchronously.  The `await` keyword just blocks execution in the containing method and the calling class gets an incomplete Task.
* You can pass multiple parameters after a url's ``?`\` by concatenating them with an \``& symbol.
* For Azure Oauth you must have the following variables: Auth URL, Access Token URL, client Id, and client secret.
* In a .NET MVC application, if you have two identical routes in the same controller all the endpoints will result in a 500 error.
* If Visual Studio ever can't find the 'system' import, press CMD+SHIFT+P (opens the VS command terminal) and type **Restart Omnisharp**
* Exchange Web Server (EWS) and on-premise Exchange will throttle excessive API requests automatically.  O365 will not.

![Xamarin](/assets/xamarin.jpg)
