---
title: AAR pt 10 (Android)
date: '2018-12-01T08:56:22-08:00'

tags: aar android kotlin
---
![Android developers banner](/assets/developers.png)

If you have not had a chance to read the first entry in the series for context, [you can do so here](/after-action-review-aar/)

It's been a little over a year since I did [my last Android AAR entry](/aar-pt-5-android/), so I figured it was about time that I had a new entry on my lessons learned with Android and Kotlin.  

* To add adb to the path (adb controls android debugging):

1. "cd ~"
2. "vim .bash_profile"
3. paste "export PATH=$PATH:/Users/jtaylor/Library/Android/sdk/platforms"

* To restart adb from the terminal (if/when logcat crashes)

1. "adb kill-server"
2. "adb start-server"

* You can run android unit tests from the terminal with "./gradlew clean test"
* To see a list of connected devices in the terminal enter "adb devices"
* "adb shell" in the Android Studio terminal tab on the bottom allows you to examine app directories (i.e. if you're saving a file to disk in the app). SQLite tables for your android app are available by navigating to data/data/yourPackageName".
* If you need to debug Android Wear you can also use TCP forwarding through adb
* In Android Studio, holding ALT while dragging your selection enables a column style selection.
* You can only lock orientation for specific activities, not the entire app.  Each activity's orientation can be specified in the manifest
* ALWAYS use Glide for image download/editing/caching/etc.  

ViewModels must never call View methods directly.  This is because Views are typically fragments and activities and are created and destroyed at will, leading to possible null pointer exceptions if called directly.

* You can press ALT+F8 in the variables window during debugging to bring up the expression evaluater.
* To calculate view size at runtime:

```kotlin
myView.measure(0,0); myView.getMeasuredWidth() / myView.getDisplayMetrics.density
```

* If you clone a project with multiple projects in it, open the base folder in Android Studio and select the project you want to run in the dropdown menu by the play button.  If a module doesn't appear in the drop-down, check it's corresponding 'build.gradle' file and see if its actually "apply plugin: 'com.android.application'" or just "apply plugin: 'com.android.library'"
* CTRL+R for local replace, CMD+R for global replace.
* Best way to check if an object's collection is null is in the object's accessor method (return an empty collection, not null)
* Primitive data types (int, double, etc.) cannot be null.  Objects (Integer, Double) can.
* Robelectric (a unit testing framework) is limited to one thread and you must manipulate views manually.
* Separate overloaded singleton constructors can lead to multiple singletons if you don't know exactly what you're doing.
* TimerTasks cannot be re-run once cancelled.  They must be de-allocated entirely and re instantiated first.
* A pointer points to a memory location and can be null.  A reference refers to an object  and shouldn't ever be null.  Because references are implemented with pointers in Java, accessing a bad reference throws a NullPointerException.
* Google has a LOT of their own libraries for functionality: Glide for image loading and caching, Room for ORM, Volley for networking, Firebase for Analytics, ViewModel and Livedata for MVVM, GSON for JSON (de)serialization
* Push notifications are a component of Firebase, which is a component of Google Play Console Services and GCP (Google Cloud Platform)
* In order for an organization to appear it must have a GCP (Google Cloud Platform) asset
* Once an app is assigned to an organization it can never be transferred
* Google API is just another name for GCP
* If you ever get "cannot resolve" errors for everything, just go to the menu and select Files>Invalidate caches>Invalidate & Restart"
* The difference between "/@+id" and "/@id" are that the former adds an id entry in R.id, the latter references the entry.
* Avoid using "include" xml tag to reference a fragment xml, it usually doesn't work.  Instead use a generic FrameLayout that you can .add(...).addToBackstack.commit()" to to, or use the "Fragment" xml tag directly in the activity xml.
* If you have a class that has difficult to isolate dependencies (i.e. DB/Network) create a subclass for your tests with an overridden constructor that doesn't actually access the DB or Network.
* Unlike iOS storyboards, aOS XML files CANNOT have views that start outside of the screen, unless you are using a ConstraintLayout.
* Best practice for lists is to use a recyclerView fragment that instantiates it's view in an Activity's FrameLayout.
* To invisibly increase a touch target increase the objects padding.  You can use negative margins on overlapped components to compensate for the increase padding.
* Passing GSON a string that's not actually an object will crash the app.
* The difference between a bundle and a hashmap is that a bundle can only contain parcelable primitives (i.e. int, bool, string) whereas Hashmaps can contain objects (Integer, Boolean, String). 
* Hashmaps also autobox (create objects for) primitives that are passed to it.
* If an item has a different UI when activated you can use a "selection.xml" in your drawables folder and assign that file to the object's XML.
* In order to pass objects between activities you must have the object implement the parcelable interface, which was built for android specifically for this purpose.  If you are using the Android Studio IDE then hovering over the class declarations name should give you a tooltip to auto-implement the interface.
* View state like user input should be preserved when the Activity is restored after the process has been killed. ViewModels are NOT a replacement for onSaveInstanceState, because as soon as the activity's process is destroyed it's corresponding ViewModel is destroyed as well.
* Configuration changes do not destroy the activity, but back-grounding the activity or opening a new activity will. You should use a combination of ViewModels, onSaveInstanceState() and local persistence to cover all situations.
* Local persistence is used for storing all data you don’t want to lose if you open and close the activity. For example: The collection of all song objects, which could include audio files and metadata. 
* ViewModels are used for storing all the data needed to display the associated UI Controller. Example: The results of the most recent search, the most recent search query.
* onSaveInstanceState is used for storing a small amount of data needed to easily reload activity state if the UI Controller is stopped and recreated by the system. Instead of storing complex objects here, persist the complex objects in local storage and store a unique ID for these objects in onSaveInstanceState(). Example: The most recent search query. [Read more on Medium](https://medium.com/google-developers/viewmodels-persistence-onsaveinstancestate-restoring-ui-state-and-loaders-fc7cc4a6c090)
* As of API 21 you have a choice to store a PersistableBundle as well as just a Bundle.  The former will be saved across device reboots.
* An unfortunate side effect of the way hierarchical navigation is implemented in Android is that the activity that you navigate up to will be completely re-created from scratch.  This means that any saved instance state will be lost.  The parent activity is seen as a completely new activity. To get around this, add the attribute \`android:launchMode="singleTop"\` to the activity in the AndroidManifest.  If that's not an option you can also override the child activity's \`onOptionsItemSelected\` method and in the item id switch case android.R.id.home call NavUtils.navigateUpFromSameTask(this).
* A bug currently exists with the Android RecyclerView. RecyclerView.dispatchLayout() can try to pull items from the scrap before calling mRecycler.clearOldPositions(). The consequence is that it pulls items from the common pool that had positions higher than the adapter size, leading to a run-time crash.  To get around this you need to turn off predictive animations by subclassing the recycler view's LayoutManager. [Read more on StackOverflow](https://stackoverflow.com/questions/30220771/recyclerview-inconsistency-detected-invalid-item-position).
* In android studio you can create code folding blocks, similar to the \`//MARK: -\` annotation in Xcode, by pressing CMD + ALT + T on a single line and selecting either "editor fold" or "region...end region".  While the former is a little uglier in the IDE, it folds all the encapsulated code in the Structure pane of the Android Studio IDE as well as in the main code pane.
