---
title: Modern Android Development
date: '2020-05-01T07:24:00-07:00'
tags: android
---
![Modern](/assets/modern.jpg)

Over the last month I’ve had the opportunity to work on an open source Android project.  Working with other Android developers from across the world has been eye-opening with regards to the current state of Android development.  Android Jetpack has really hit its stride, with the adoption of Jetpack ViewModels, LiveData, View Binding, Coroutines and Navigation Flows becoming standard practice.  

ViewModels in particular are practically indispensable for android development.  As a lifecycleObserver, a VM can actually implement lifecycle callback methods, avoiding fragment transaction errors. Prior to VMs, fragment transaction errors were largely caused by executing fragment transactions in an AsyncTask’s “onPostExecute()" call.  AsyncTasks were Android’s original means of handling long-running processes. The exception would be thrown because there is no lifecycle safety within the scope of onPostExecute(), whereas fragment transactions must take place either in the "Activity.onCreate()", "Activity.onPostResume()" or "FragmentActivity.onResumeFragments()” lifecycle scopes. 

Kotlin coroutines have also largely superseded AsyncTasks.  To use coroutines you must include the kotlinX gradle dependency. For coroutines you can use either "async { ... }" which returns a deferred async task", or launch { ... }", which does not.  You should not pass in the backing data structure to an asynchronous call however. For maximum thread safety you should use immutable objects and replace the original object instead of modifying it.

At Nautilus we’ve made a point to keep the code as modern as possible,  but it’s very much a “brown-field” project. The sheer size of the App (248,660 lines of code when I started two years ago!) has meant that we’ve had to keep changes to the original paradigms to a minimum.  To avoid unintentional drift we use the 3rd party plugin "Save Actions" to optimize our imports automatically on save, maintain indent uniformity, and support consistent code formatting in general.

One of the transitions we made early on at Nautilus was to use Timber for logging. Timber is now preferred over the vanilla Log function call. This is because Timber auto-grabs the class name for the TAG and by default does not log in production. The only catch is that you must “plant” the Timber instance in your Application Singleton.  The use of tags allows you to filter out repetitive debugger messages with exclusionary regexes in Android Studio.

At Nautilus we’ve also taken advantage of Google Play’s new “Timed Publishing” feature for app releases. "Timed Publishing" allows you to delay promotions to Alpha, Beta, and Production until you press "Go Live".

Overall, we’ve worked very hard to capitalize on the advantages provided by the latest Android developments. In Android 7.0 JIT was added back to ART for a hybrid JIT/AOT(Just In Time/Ahead Of Time) approach, resulting in faster app downloads and app execution speeds.  At Nautilus we’ve set our minimum SDK version to 24 partly to take advantage of these efficiencies.  As of Android 9 compiler warnings are displayed if you try and extend private interfaces.  We’ve used these to ensure that no private interfaces are unintentionally implemented.  This prevents our concrete classes from breaking when private interfaces are changed in newer versions of Android.  Our app also uses SystemAlertWindow to allow us to display workout metrics on top of other apps such as Netflix. As of Android 8.0.0 SystemAlertWindow was deprecated in favor of TypeApplication.Overlay. To adapt to newer devices we’ve added conditional logic to use the API appropriate for the OS version.

Photo by [Meriç Dağlı](https://unsplash.com/@meric?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on Unsplash
