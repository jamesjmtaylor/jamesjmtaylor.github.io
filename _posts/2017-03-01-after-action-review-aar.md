---
title: After Action Review (AAR)
date: 2017-03-01T04:53:40-08:00
tags: aar android
---
![After Action Review](/assets/aar.jpg)

In the Army we conducted a formal retrospective after every operation that we completed, called an After Action Review (AAR).   Books like <u>Outliers</u> have found such rituals to be extraordinarily important to skills mastery.  In the work, Malcolm Gladwell posits that it takes at least 10,000 hours of deliberate practice to cultivate a world-class skill.  Part of deliberate practice is performing a critical review of performance after every session, essentially an AAR.

 Since leaving the Army I've carried on the habit of After Action Reviews. After every two week Sprint at Steelcase I summarize the most important lessons learned over the last two weeks in my notebook.  My plan going forward is transcribe the best lessons learned from my notebook into my blog every two months. The lessons learned will be in the form of bullet points that I wish I had known prior to undertaking the work involved in the Sprint.  If you're not interested in my more technical musings, feel free to skip over these.  Otherwise, I hope your able to glean a few helpful tidbits here and there.  

This review iteration's topic is Android.  Without further adieu:

* The android emulator can't directly connect to localhost, instead use "http://10.0.2.2"
* The android emulator is actually configured as an x86 architecture to increase compilation speed since it cuts out the translation from a CISC architecture to a RISC architecture.  This means it's actually a simulator, since Android devices generally employ ARM architecture.
* Android garbage collection (GC)  was initially done with the Java Virtual Machine (JVM) GC.  Then in the Marshmallow update it starting using the Dalvik Virtual Machine (DVM) GC.  The Nougat update upgraded GC to Automatic Reference-Counting (ARC), and Oreo improved it to Concurrent Copy-Compact (CCC).  Since traditional garbage collection has to occur on the main thread, these updates reduced UI 'jank' from 10ms, to 5ms, to 3ms, to 0ms respectively.
* Static variables are tied to the process and can cause crashes when the activity process is killed and restarted
* Do NOT use loader fragments (Complex and buggy)
* Use one activity for each region (i.e. on-boarding, sign in, payment, etc), with fragments serving primarily for phone versus tablet layouts.
* Cache is destroyed with the process, better to save images to the file system when possible.
* In the menu bar you can select **Run** > **Espresso Test** to record UITests in Android Studio.
* Espresso UI Tests in particular and instrumented tests in general do not contribute to code coverage
* Use `SharedPreferences` with `Context.MODE_PRIVATE` for secure token storage (requires use of application singleton to avoid passing context around)
* You can implement custom transition animations as a xmls.  These are called by executing `FragmentManager.setCustomAnimation`.
* When you see "100%p" for an android animation xml it means that the animation refers to a percentage of it's parent.  For example 100%p in an x-coordinate translation would shift the animated object the full width of it's parent.
* The only elements absolutely necessary for a RecyclerViewFragment are a **recyclerView**, a **recyclerAdapter**, a **layoutManager**, a **clickListener** and an **onCreateView** method to instantiate them all.
* In a recyclerView you MUST set each attribute of the cell (Can't rely on default values because of cell reuse).
* RecyclerView has headers baked in already.  Don't try to implement your own header logic.
* The best way to re-select recyclerView ListItems on orientation changes is by referencing a preserved position from a ViewModel singleton in the Adapter's "onBindViewHolder".  You should handle animations and onClickListeners in "onBindViewHolder" as well.
* Always ensure network permissions are declared in the manifest file.
* Always ensure that you use SupportFragmentManager (NOT FragmentManager)
* For `supportFragmentManager.replace` combines the `.remove` and the `.add` transactions.
* `setDrawerIndicator(false)` will remove the hamburger and back if there is only one fragment in the backstack, but will only show hamburger if true. `setDiplayHomeAsUpEnabled(true)` just provides the back button.  Make sure to call `mDrawerToggle.syncState()` after any changes.
* If updating the UI (i.e. the actionBar/toolBoor) you should post the updates inside an `OnBackStackChangeListener` method declared within your class' `onCreate` method
* Mipmamp versus Drawable-Drawables may be stripped out as part of dp-specific resource optimization. Mipmaps will never be stripped. A best practice is to right-click your **res** directory and select **New**>**Vector Asset** (if .svg) OR **Image Asset**>**Launcher Asset** and move the generated icon from mipmap to drawable.
* The `OnPostExecute` of an AsyncTask takes place on the main thread.  This makes it a good place to call methods from the supported Fragment/Activity
 (callback methods).
* Any CRUD operation for SQLite must take place in a background thread. If you need to update UI you need access to an activity and must create a new runOnUiThread Runnable.  The alternative is to use an AsyncTask with a UI update method call in the `onPostExecute` method.
* For simple polling use a Timer and TimerTask (Make sure to call `TimerTask.cancel` in the Fragment's `OnDetach`).  Note that you will need to create a new Timer, since you cannot rerun a timer after you have called it's `.cancel` method.
* When using Timers you need a TimerHandler in order to post to the main thread.
* Whenever you're dealing with data from the network, always ask yourself: "What happens if this is null?"
