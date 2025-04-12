---
title: AAR pt 15 (Architecture/Software Engineering)
date: '2019-10-05T09:41:08-07:00'
tags: software-engineering
---
![Blueprint](/assets/blueprint.jpg)

If you haven’t had a chance to read the first entry in the series for context, <a href="/post/after-action-review-aar/">you can do so here</a>

Given that my last post was about my architecture certification, I figured this would be a good time to do another post on some architecture concepts. This is the second architecture lessons learned post that I've done; <a href="/post/aar-pt-8-architecture-software-engineering/">you can find the first one here</a>.

* The idea of debouncing is that you artificially limit multiple, similar requests by a client, only sending a new request every X seconds.  Libraries like RxSwift and RxJava have debouncer functions built in.
* Most MVC server frameworks handle exceptions by return a 500 error code and a stack trace, recovering gracefully.  
* Any IP address starting with 10, 172, or 192.168 is private (with a few exceptions).  You will not be able to access them across the internet if you are not already on the same local network.  Everything else is public  
* The 12 factor web API guidelines recommends that you store a microservice's configuration in the environment, NOT the app's config file.  This architectural decision has the added benefit that if the source code is compromised, none of the API secrets are lost.
* When you override "Equals()" you must override "Hash()" as well, otherwise you can have duplicate objects in HashSets.
* Reachability is the ability of a device to 'reach' the internet.  Connectivity is the ability of a device to 'connect' to a particular service.  You can potentially 'reach' the internet but not connect to anything if your router is functioning but is not resolving domains.  This might happen if a user connects their device to their home wifi but their internet service provider is experiencing a localized outage.
* In Android you use ConnectivityService to determine if you are connected to the internet.  In iOS you use ReachabilityService.
* If possible use a library to serialize and deserialize rather doing it by hand.  The latter course of action is error prone and often leads to inconsistencies.
* In functional programming ".map" applies a function to each element of a collection.  ".flatmap" executes a reduce step and then applies the function.
* Never assume the image encoding standards of your server. As an example some servers use base64 encoding, while others might use UTF8, leading to conflicts.
* Be VERY conscious of decisions to use static variables vs dependency injection.  Usually static variables have long-term repercussions.
* Kotlin and Swift are BAD choices for programming competitions because they lack ready-made implementations of stack & queue.  These data structures are VITAL for most programming challenge algorithms, and re-implementing them consumes precious time.  The only language that is consistently supported on HackerRank is Java.  Learn to use and love it.
* Sorting a list is modifying it and is NOT thread safe.
* There are 2 different ways of controlling versions between clients and servers.  In mobile versioning you check if the app version is compatible with the existing API and present an alert to the user to upgrade if it isn't.  In API versioning you have multiple versions of an API available until client's that relied on the old endpoints are no longer used.
* Always use ints for money.  Floats and doubles introduce subtle rounding errors.
* The biggest question for reactive programming is how you deal with the null state.  Blank object?  Loading Screen?  This must be dealt with on a case-by-case basis.
* When you index a new column what you're actually doing is creating a whole new copy of the table that is updated and in sync with the original table.
* ODM (Original Device Manufacturer) differs from OTM (Off The Shelf) in that it is custom built by a hardware company and licensed to another for re-sale.
* As an interpreted language javascript is able to support the "spread" operator.  This allows you to combine multiple concrete objects into a single object at run-time.  Neither C#, Kotlin, Swift, or Java supports this. Precedence favors the last object passed into the spread function if multiple objects have the same property with different values.
* In javascript "generators" are function that can hold arbitrary state like an index or an array that can be iterated over with the '.next()' call.
* The law of Demeter in software engineering is the idea that a given object should assume as little about associated objects as possible. The best way to enforce this is to make attributes that are nested objects private.  It is a "code smell" metric rather than a loose-coupling methodology.  Dependency inversion would be the best concrete means to obey the Law of Demeter.
* RIB architecture is an abbreviated VIPER framework.  It stands for Router (which determines which VCs connect to which), Interactor (where the business logic and DB/network calls go), and Builder (Basically the Dagger module where dependencies are injected). Presenters can be added later if there are actually views to present to.
* Both Android Studio and XCode will throw a compile-time error if you have a class  that conforms to multiple interfaces/protocols with identical function requirements that have different default implementations.  This saves you from ever implementing the dreaded diamond of death that multiple inheritance allowed.

Photo by <a style="background-color:black;color:white;text-decoration:none;padding:4px 6px;font-family:-apple-system, BlinkMacSystemFont, &quot;San Francisco&quot;, &quot;Helvetica Neue&quot;, Helvetica, Ubuntu, Roboto, Noto, &quot;Segoe UI&quot;, Arial, sans-serif;font-size:12px;font-weight:bold;line-height:1.2;display:inline-block;border-radius:3px" href="https://unsplash.com/@sxoxm?utm_medium=referral&amp;utm_campaign=photographer-credit&amp;utm_content=creditBadge" target="_blank" rel="noopener noreferrer" title="Download free do whatever you want high-resolution photos from Sven Mieke"><span style="display:inline-block;padding:2px 3px"><svg xmlns="http://www.w3.org/2000/svg" style="height:12px;width:auto;position:relative;vertical-align:middle;top:-2px;fill:white" viewBox="0 0 32 32"><title>unsplash-logo</title><path d="M10 9V0h12v9H10zm12 5h10v18H0V14h10v9h12v-9z"></path></svg></span><span style="display:inline-block;padding:2px 3px">Sven Mieke</span></a>
