---
title: AAR pt 7 (Android Architecture Components)
date: '2018-03-18T06:15:05-07:00'
tags: android kotlin aar
---
![Model-View-ViewModel](/assets/mvvm.jpg)

If you haven’t had a chance to read the first entry in the series for context, <a href="/post/after-action-review-aar/">you can do so here</a>

Way back in 2017 at Google I/O several architectural components were announced to help streamline Android development.  Kotlin support was also announced.  I've only just recently been able to get around to doing an app that implements both simultaneously.  I've included some of my lessons learned below:

## LiveData

The Android MVVM pattern relies on a custom implementation of the MVVM and Observer patterns.  LiveData is necessary for implementation of the Observer pattern.  This is because use of the Realm subscriber functions in any View class prevents testing of the Android lifecycle methods within that class by the Robolectric Test Runner.  LiveData has no publicly available methods to update the stored data. The MutableLiveData class exposes the setValue(T) and postValue(T) methods publicly and you must use these if you need to edit the value stored in a LiveData object. You can declare separate observers within a fragment or activity, one for each ViewModel property. This allows you to explicitly declare what actions to take if a change in a recyclerView's list items occurs as a result of a database change, versus a change in observed from a LiveData 'isLoading' boolean.  LiveData should NOT communicate directly to a recyclerView adapter or holder. Instead, the RecyclerViewFragment observes changes to the ViewModel's liveData properties and informs the adapter as normal. Always keep the ArrayList object that the recyclerView is displaying as a property of the adapter
.

The Google ViewModel is a superclass that all declared ViewModels must implement. It pairs with Room & LiveData to provide a persistent data layer for your views to receive updates from.  The gradle dependencies are below:

**build.gradle (app)**

```groovy
dependencies {
    implementation "android.arch.lifecycle:extensions:1.1.0" // ViewModel and LiveData
    annotationProcessor "android.arch.lifecycle:compiler:1.1.0"

    implementation "android.arch.persistence.room:runtime:1.0.0"  // Room

    annotationProcessor "android.arch.persistence.room:compiler:1.0.0"

    testImplementation "android.arch.core:core-testing:1.1.0" // Test helpers for LiveData
    testImplementation "android.arch.persistence.room:testing:1.0.0"// Test helpers for Room
}
```

Once both the project and app build.gradle files are modified you can import the ViewModel superclass in your java files.  The benefit of using ViewModel is that ViewModel objects are automatically retained during configuration changes so that the data they hold are immediately available to the next activity or fragment instance.  For this to work however all Activities & Fragments must implement the LifecycleOwner interface in order for ViewModels to be aware of their lifecycles. An example of a ViewModel class implementation is below:

**EquipmentViewModel**

```kotlin
class EquipmentViewModel(application: Application) : AndroidViewModel(application), LifecycleObserver {
    val repo = EquipmentRepository()
    val equipment = MediatorLiveData<List<Equipment>>() //Mediator allows this class to pass the RoomLiveData from the repo class to the View

    val isLoading = MediatorLiveData<Boolean>()

    var filterResults : List<Equipment>? = ArrayList<Equipment>()
    private var selectedType : EquipmentType = EquipmentType.LAND

    init {
        isLoading.addSource(repo.isLoading){isLoading.value=it}
        val source = getCurrentSource()

        if (source != null){
            equipment.addSource(source) {
                equipment.value = it
            }
        }
    }
}
```

When implementing an MVVM architecture you can have your view model class implement either ViewModel or ViewModelAndroid. ViewModelAndroid requires that you pass an Application instance to its constructor, but in exchange it now has a reference to the application singleton. That means that you can query the application in order to get a reference to the application database (as long as you have a reference to the database in the application, which you should). The ViewModel itself can either be instantiated by the activity or the fragment relying on the ViewModel.  Below is an example of the latter:

**EquipmentFragment**

```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        columnCount = calculateNoOfColumns(App.instance.applicationContext)
        initVM()
    }

    //MARK: ViewModel Methods
    private fun initVM() {
        eVM = activity?.let {ViewModelProviders.of(it).get(EquipmentViewModel::class.java)}
        eVM?.let { lifecycle.addObserver(it) } //Add ViewModel as an observer of this fragment's lifecycle
        eVM?.equipment?.observe(this, equipmentObserver)
    }
}
```

To have a fragment receive the same instance of ViewModel as your activity you need to pass that activity in the fragment's ViewModelProviders method. To do that, just call 'getActivity' (as long as that fragment is hosted by the activity).  In the case of Kotlin, you can simply call \`activity\` since accessors and mutators generally aren't used.

NOTE: Since the ViewModel outlives specific activity and fragment instantiations, it should never reference a View, or any class that may hold a reference to the activity context.  This is where the observer pattern is necessary through LiveData.  By having the View subscribe to the ViewModel and call ViewModel functions for the application of business logic, you avoid the ViewModel knowing anything about the View.

Now for some Kotlin lessons learned:

Kotlin offers a Java interoperable language that offers a lot of Swift features including: type inference with var (same as Swift 'var') and vals ('let'), optionals ('?'), forced unwrapping ('!!'), extensions, DSLs, and DataObjects (which are similar to swift structs).

* Kotlin support is built into Android Studio 3.0, including template Kotlin code.
* Default Java protection is package-private, in Kotlin it's public.
* Kotlin actually doesn't have the keyword 'new'. Classes are instantiated by calling them like methods (Just like swift). The constructor is also combined with the class name. So instead of explicitly declaring a \`class foo\` and then a constructor function, you would just declare the class as \`class foo(val bar: Int) {\` . 
* By default, all classes in Kotlin are final. To declare otherwise you must use the 'open' keyword.
* The Kotlin paradigm for object property access and mutation is identical to that of Swift.  Just get and set the property explicitly.
* The Kotlin 'let' keyword is tersely described as a function that "Calls the specified function block with this value as its argument and returns its result." It's real power is that it allows the object that calls it to have an '?.' operator, and only runs if the calling object or expression is not null. Think of it as a swift \`if let\` block where the unwrapped constant generated as a result of the \`if let\` is assigned the name \`it\`. An example is below:

**let keyword**

```kotlin
findUser(id)?.let {
 return it.name
 }
 ```

The Kotlin equivalent of the Swift \`guard let\` statement takes the following format ( the unwrappedString variable is available for use immediately following unwrapping).  Note that this function makes use of the 'Elvis operator' ('?:') which is similar to Swift's default value operator ('??') except that it can execute functions as well as return values.

**guard let statement**

```kotlin
val nullSafeString : String? = "Cool string"
val unwrappedString = nullSafeString ?: return
```

The Kotlin documentation recommends solving most of the needs for static functions with package-level functions. They are simply declared outside a class in a source code file. Then you can call them anywhere and auto-import the function in Android Studio by pressing ALT+ENTER when the function is underlined. "Kotlin has “class” for classes that have multiple instances, and “object” for singletons.
 A “companion object” is an extension of the concept of “object”: an object that is a companion to a particular class, and thus has access to it’s private level methods and properties.
 Sometimes in Java you need to create anonymous inner class instances.  OnClickListener is an excellent, oft used example of this. Kotlin slightly generalizes this concept with object expressions and object declarations.  

## Kotlin Gotchas

The elvis operator is last in the order of operations, which means that any operations after it will be considered part of the elvis operation unless you use parentheses.  For example, if the wrappedInt below stores the integer 3:

```kotlin
unwrappedIntPlusFive = wrappedInt ?: 5 + 5
```

unwrappedIntPlusFive will actually evaluate to 3, not 8.  To executed the order of operations as intended you would need to use the expression below:

```kotlin
unwrappedIntPlusFive = (wrappedInt ?: 5) + 5
```
