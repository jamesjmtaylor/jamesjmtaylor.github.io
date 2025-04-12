---
title: AAR pt 9 (Swift)
date: '2018-09-01T06:00:16-07:00'
tags: aar swift
---
![Swift](/assets/swift.jpg)

If you haven’t had a chance to read the first entry in the series for context, <a href="/post/after-action-review-aar/">you can do so here</a>

It's been exactly a year since I did <a href="/post/aar-pt-4-swift-xcode/">my last Swift/iOS AAR entry</a>, and two years since I <a href="/post/day-1-ios-fundamentals/">started the Coding Dojo Swift bootcamp</a> so I figured it was about time that I had a new entry on my lessons learned with Swift.  I've got a few more entries in my notebook that I haven't been able to transcribe into a digital format yet, but you'll probably find this to be a lot to digest as it is; so without further adieu:

* Apple's recomendation is that you use .pngs for image assets, not vector drawables (pdfs).  You can read more about their recommendation at <a href="https://developer.apple.com/library/content/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/LoadingImages/LoadingImages.htm">https://developer.apple.com/library/content/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/LoadingImages/LoadingImages.html</a>.
* You can declare a delegate protocol in the same file as the class that uses.  Java will not allow you to do this, but Kotlin will.
* If you need to erase a UserDefault key, use `UserDefaults.standard.removeObject(forKey: String)`. Do NOT use` UserDefaults.standard.setNilValueForKey(key: String)` which is a run-time crash.
* By far the most versatile UserDefault type to use is dictionary.
* UserDefaults, just like Android sharedPreferences, use a the repository pattern behind the scenes.  This means that when you set the key it's first saved to an in-memory cache, and then a SQLite database.
* Swipe left to delete supported as of iOS8, but swipe right/left for custom actions is supported only as of iOS11
* Do NOT try to add your own swipe action.  Use the` editActionForRowAt` tableview function.
* Do NOT try to keep track of tableView editing state yourself, use `tableView.isEditing`
* If you are using `editActionsForRowAt `make sure to call refreshUI in your own custom implementation of `didEndEditingRowAt`
* Only use `tableView.dequeCell(...)` in the `tableView.cellForRowAt `function. This is because it resets all the fields back to their defaults in order to promote cell reuse.
* If you want to prevent cell attributes from changing back to defaults when accessing use `tableView.CellForRow(at: indexPath)`
* By using the view debugger at runtime you can see which views to introspect against for runtime modifications.
* Use the font-book application that comes with MacOS to find and install fonts missing from your system.  Fonts must be in ".otf" format to be imported.
* To see all possible font values use `for font in UIFont.familyNames{ print(UIFont.fontNames(forFamilyName:font)}`
* It is possible to get TabBar and Navbar and superview height at run-time with self (i.e. self.view.frame.height)
* Constraints are the only way to affect frames in `ViewDidLoad`.  After `didLayoutSubviews `you can change frames at will.
* In LLDB during debugging you can trigger a button by typing `expr self.buttonPressed()`
* LLDB can dump all variables with "fr v" or "fr v -a"
* AFNetworking is implicitly asynchronous, UNLESS you try to change the database on the callback thread due to the database ACID principles (Atomic, Complete, Isolated, Durable), at which point it implicitly becomes synchronous.
* An XCode workspace must have the same name as the project for cocoapods to work.  You can also just use the workspace that cocoapods auto-generates.
* You can use cgMaskingColorComponents to remove a color that should be transparent.
* In Swift retrieving an object from a dictionary or array creates a deep copy.  In fact, every time you change an object a copy is made and a reference count check is run to see which copy to retain (or both); for this reason immutable objects are sometimes used to save computation time.
* Playing .gif files in Swift requires an external library.  It's generally not recommended though, since .gifs are generally much more size intensive and lower fidelity than an equivalent Lottie animation.
* If you want to animate a layout constraint change, set the InterfaceBuilder constraint to the new value and then call animate on the method `View.NotifyLayoutChangeNeeded()`
* You can dynamically and programatically add subviews within a stackview by calling `.addSubview(...)`
* If presenting a .xib (Pronounced 'nib') over another storyboard view using `Bundle.main.loadNibNamed(...) `make sure to set the frame to match `self.view.bounds.width/height`
* In storyboards the fit to size hotkey is "CMD+="
* You can restart Timers by setting them to nil and re-initializing them with another constructor call.
* You can control the natively provided UIBlurEffect radius by animating it's application to a view and pausing that animation at the fractional value of the blur radius that you want.
* NSURLSession has problems with reading special characters like "+" and "&" as part of a x-www-form-encoded body.  To get around that set it to use JSON encoding.
* All UIViews support using CoreGraphics methods to draw basic shapes (i.e. triangles/ovals/etc.).  Just be aware that it must be finished before the view has appeared and that the drawing is not instant, and that it should NEVER be used to try and draw to every single UITableViewCell (it may not finish in time).
* GCD (Grand Central Dispatch) allows you to asynchronously sleep with `DispatchQueue.main.asyncAfter(deadline: now+myTimeLimit){ ... }`
* You cannot animate "isHidden" like you can in Android. Use "alpha=0" or "alpha=1.0" instead.
* The animation completion block bool just refers to whether or not the animation completed successfully.
* To mimic the effect of android 9-patch files, use the XCode slicing feature on an image in the xcassets folder by going to the menu bar and selecting Editor>Show Slicing
* For dynamically resizing your tableView cells, constrain your sub-views to the auto-generated Content View, NOT the prototype cell itself.
* In Swift 4 you can paste raw JSON directly into a string multiline (initiated and terminated with three double-quotes, i.e. """myString""")
* In Swift 4 the enum "CodingKeys" keyword for codable conformance MUST be PascalCase (deserialization/serialization will fail otherwise)!
* When publishing to Testflight, the username and password that you are asked to provide is only provided to the Apple test team so that they can manually login and test the app (as opposed to the Google paradigm of automated testing).
* The keyboard does NOT automatically bump up the view like it does in Android  to prevent it from obscuring the focused editText.  You must design your own custom animations to accomplish this.
* You can adjust where a button's text and image go exactly in the storyboard constraints tab with the image and title insets.
* To use a vector graphic, create a new image asset in the xcassets file and then in the image's attributes inspector select scales>single scale, then drag and drop your .pdf into the "universal" box.
* The starting storyboard is declared in the Project Files>Target>Info>Main Storyboard File Base Name
* You do not need an embedded navigation controller for anything other than backstack management.  That being said each tab should have it's own backstack manager/navigation controller.
* You can segue from one storyboard file to another just like you would one VC to another, you just need to use a storyboard reference widget in the origin storyboard to connect the segue to.
* UITextField, UILabels, and UIEditTexts do not support clickable hyperlinks.  Instead, use a textStorage, a textContainer, and a layoutManager to introspect on character ranges. 
* You can drag and drop a tapRecognizer widget onto any other element to make it interactive (i.e. a UILabel)
* Do NOT count on attributed strings in the storyboard to render correctly at run-time.
* Schemes are just meta-data that bundle targets for compilation.  Scheme and Target should be the same name for ease of CI/CD scripting and cocoapods dependency specification.  They should also only be PascalCase, for the same reasons.
* Local and Remote (push) notifications are handled in the AppDelegate in the `didReceiveRemoteNotification `function.
* Local notifications can be triggered by timers even when the app is closed.
* A Good optimization for tableView updates is to update using `indexPathsForVisibleCells`.
* ScrollViews automatically add padding to their content in order to make room for the scrollbar.  You can hide the scrollbar inset by adjusting constraints to a setting larger than the superview. In iOS 9 scrollInsets are handled differently than iOS 10 and 11.  The solution is to set `self.autoAdjustScrollViewInsets = false` in viewDidLoad().
* If you ever want to implement custom segue animations see WWDC13-"Custom View Controller Animations"
* You can write swift extensions for InterfaceBuilder attributes of widgets in the storyboard.  They'll even show in the attributes pane of the Storyboard editor.  Changes won't reflect in the storyboard, but they will display at runtime.  An example of such an extension would be the addition of a kerning attribute for UILabels.
