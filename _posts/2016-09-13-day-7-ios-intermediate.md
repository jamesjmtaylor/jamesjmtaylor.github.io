---
title: Day 7-iOS Intermediate
date: 2016-09-13T12:08:38.000Z
tags: coding-bootcamp
---
![Day 7](/assets/day7.jpg)

Timeline:

* 0740-Another day at the dojo.  I continue work on CoreData.
* 0900-David cover’s the basics of the delegate pattern, which is critical for an object to reliably call another object’s method (which it can count on because that object implements the specified delegate protocol).  I also found out why my display wasn’t refreshing upon a successful add; tableView.reloadData() was commented out, preventing the tableView from updating to reflect the new data in the array it’s pointing to.

* 1100-I’ve completed the Bucket List App with a CoreData implementation.  It now has persistent CREATE, READ, UPDATE, DELETE (or CRUD) functions.  For algorithms we addressed the creation of min, max, size, and isValid functions for our binary search tree.  I first implemented them on the whiteboard and then created a Swift playground to validate their syntax.
* 1300-Everyone’s called to the whiteboard once more.  David goes over delegate syntax and we have a rather lengthy question and answer session. 
* 1400-I start work on the optional My Dogs app assignment.  The Coding Dojo lesson platform instructions are quite clear in that this will be an advanced application.  It specifically states that self-study of the UIImagePicker and CollectionsViewController classes will be necessary to complete the challenge. I’m up to it though and dig in.
* 1900-After five hours of work I have the first half of the app, the add functionality using UIImagePicker, completed.  I still need to implement the CollectionsViewController logic though and won’t be able to add edit functionality until this is done.  Ancillarily I did learn a lot about modal vs show seques and how they are handled both in presentation and application logic.  Looking forward to tomorrow!
