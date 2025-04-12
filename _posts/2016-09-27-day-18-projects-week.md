---
title: Day 18-Projects Week
date: 2016-09-27T05:39:54-07:00
tags: coding-bootcamp
---
![Day 18](/assets/day18.jpg)

* 0745-David’s back.  He provides a some help with a CoreData problem that I was having.  Apparently the developers of Swift 3 and xCode implemented the Swift 2 to Swift 3 migration in such a way that Swift 2 CoreData models were kept in their original Swift 2 format while the rest of the project’s syntax was changed to Swift 3. This was probably done to maintain backwards compatibility with existing app databases that clients were currently using.  What this meant for me was that what I thought was Swift 3 CoreData syntax from a previous project was really just grandfathered Swift 2 syntax that would not, could not, be compiled in a fresh Swift 3 project.  Very confusing until David pointed out the error.  
* 1100-Continuing on the trie theme we implement the trie with a sorted array to store the children of each node and write a size function that returns the total number of words stored in the trie.
* 1800-I’ve found that the manner in which the Gwent API is implemented prevents the development of clean and maintainable (or even functional) code to parse it.  After looking for a while I found quite a few recommendations for Alamofire and SwiftyJSON.  The installation of all the dependencies required to install them took about 15 minutes, plus another 15 minutes to troubleshoot issues (apparently you have to have the project and the ‘pods’ within a single workspace, which is something we haven’t worked with yet).  While I haven’t made any demonstrable progress on the app today, I do feel that I’ve learned a lot that will help me tomorrow and the remainder of my Swift development days.
* 1845-I know I’m calling it a little bit early, but I want to have a chance to rest up before hitting the project fresh tomorrow. Until then…
