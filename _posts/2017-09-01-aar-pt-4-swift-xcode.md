---
title: AAR pt 4 (Swift/Xcode)
date: 2017-09-01T00:00:00-07:00
tags: swift aar
---
![XCode](/assets/xcode.jpg)

If you haven't had a chance to read the first entry in the series for context, [you can do so here](/after-action-review-aar/). Given that my work is primarily based around development of the client applications, I'm going to have an outsized quantity of iOS and Android lessons learned compared to the other stacks.  I'll do as well as I can to stagger them with other languages and tools (for example, the preceding entries on Python, React, and C#), but will probably eventually have a few back-to-back entries of iOS and Android.  So, without further adieu, Swift & Xcode tips!

* In order to measure startup time, click the app name by the play button, **Edit scheme** > **Run** > **Arguments** > **Environment Variables** > **DYLD_PRINT_STATISTICS**.  Make sure it's a fresh simulator instance each time.
* `.description` in swift and ObjectiveC is the same as .toString in Java
.
* Keep configuration info in the AppDelegate to a minimum ( Use scheme environment variables instead)
* To view device logs (for wired debugging) select the menu bar **Window** > **Devices** > **Select Device** > **View Device Logs**"
* If building with a distribution license you will NOT be able to debug through your device (You'll receive a "Failed to attach to process" error)
* To acces the AppDelegate use "app = UIApplication.shared.delegate as! AppDelegate"
* If you ever have a "no such module" error make sure you're using xcworkspace, NOT xcproject
* Environment variables are ONLY available in Xcode, use Xconfig files (one for each scheme) for run-time configuration variables.
* You can mock the framework and it will still count for code coverage.
* The syntax "`defer { abc }`" waits until the end of a function to execute (good for cleaning up resources)
* If a scheme run config (managed in Edit Schemes) is not set to debug, garbage collection will cause variables to deallocate during step-through debugging!
* Stopping the simulator in a breakpoint when debugging an app also stops the simulator system clock, leading to odd artificial timing errors if you're trying to debug timing issues between different instances of the app on different devices.  
* The is no analogue to Java's ArrayList because Swift doesn't have lists.  This means O(1) prepend/append is impossible.
* If you're going to animate a UILabel expansion from one line to multiple lines, it should be over the number of lines displayed, not a change in the height of the UILabel itsel
* You can never count on a storyboard loading before AppDelegate does or vice-versa.
* You can see exactly where your layout view boundaries are by setting LayerBorderWidth, Number, 1 in"UserDefinedRuntimeAttribues" of the storyboard.
* UIScrollView ContentSize is the scrollable area; UIScrollView.frameSize is the actual size of the scrollView window.
* You can CTRL+Drag a constraint from the storyboard to the viewController and change constraints programmatically.
* For a calendar inmplemented through a UICollectionView always use images for selection because cells will have different sizes on different phones (and different sizes on the same phone if the number of cells is odd) . This can be fixed with images and the "aspect-fit" setting.
* In order to replicate an android carousel view, use a horizontal scrolling CollectionView and a PageControl with the number of pages equal to the CollectionViews.cells.count and the CurrentPage = CollectionView.visible.cells.first (set in the `scrollViewDidScrollFunction).
* For any kind of image filtering just use CoreImage.
* If you have multiple collectionViews/tableViews you don't need to re-declare the delegate methods, just have a check within each method to see which collectionView the method is called with.
* You can get/set a specific cell at any time, you just need to create an indexPath with the item and section, then call collectionView.itemAt(indexPath).
* For a storyboard to recognize a font for selection in Xcode, it must be in the Mac Fonts application of your development Macbook
* When you manually instantiate ViewControllers (VCs) in XCTests you must explicitly provide relationships between VC's (doesn't happen automatically like it does with a storyboard).
* If in XCTestSetup you need to wait for an async task to complete before running the test itself, use "RunLoopMode(defaultRunLoopMode, before: .distantFuture)"
* UILabels can never be changed by the user.  Use UITextFields instead.
* The Enterprise Dev Account EULA requires you to have direct control of the devices registered under the account and the ability to install and uninstall apps at will.  The primary use case is for company owned smartphones. 
* There's nothing to stop a company from having multiple regular Apple Developer Accounts, you just need multiple DUN #'s.  Each DUN # must be tied to a different physical address.
  Apple's primary retribution for EULA violations is to stonewall all future app approval requests, so be careful!
* Swift Date() function always generates a date with a UTC timezone.
* For the Observer pattern, just use Notification Center (The selector is the method to execute on update).  As of iOS 8 subscribers to the Notification center are even cleaned up when their views are destroyed, preventing memory leaks.
* The swift string class has an implementation very similar to the Java StringBuilder class, allowing constant time string appends.
* To dismiss the keyboard just do "self.view.endEditing(true)"
* To have a view 'float' from within a scrollView, just assign it constraints external to the UIScrollView.
* UITableView header and footer views cannot have constraints external to themselves.
* By writing extensions to protocols you can define default behavior for conformers.
* Protocols can have attributes but must show "{get set}".  This is one way you can get around extensions not allowing attributes, that is, by writing an extension to a protocol.
* If you use a break to escape an 'if' statement, you must prefix the 'if' with a name, i.e. "myCustomIf: if let unwrap = wrappedVar { ... break customIf ... }"
