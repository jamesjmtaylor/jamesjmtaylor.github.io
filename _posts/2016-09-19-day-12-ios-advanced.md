---
title: Day 12-iOS Advanced
date: 2016-09-19T12:29:18.000Z
tags: coding-bootcamp
---
![Day 12](/assets/day12.jpeg)

Timeline:
* 0800-This is the final week of guided lessons.  Next week is project week, during which we will be set loose to use everything that we’ve learned to create our own app.  I’ve got a few ideas that I’m kicking around, but I want to get through this week before I decide on anything.
* 0900-David introduces JSON query structure and how we have to convert that data (which is just a specially formatted string) into either an Array or a Dictionary. Our next few assignments are going to revolve around GET requests to a Star Wars API (also known as SWAPI) in order to populate a Star Wars Encyclopedia.  Sounds like fun.
* 1100-Algorithms time.  We covered HashMap fundamentals and were asked to implement the HashMap class as well as the insert and getValueFromKey functions.  
* 1430-I worked for 90 minutes trying to implement HashMap in the Xcode playground only for it not to work.  Not the class or any of its functions, but the playground itself.  I tried putting a print statement as the first line after the import statement and it still didn’t work.  Guess I’ll get back to work on the Star Wars App.
* 1500-David called us all up to the whiteboard to describe the MVC framework.  We’ve apparently already implemented the View and the Controller (which are the storyboard and the ViewController files respectively).  Now all that’s left is to build the model.  This turns out to be a class that represents an instance of the core database object, in this case a Star Wars character.  The special thing about the model class is that it encapsulates the JSON call and parsing entirely, which allows you to just call Person.getAllPeople() to populate the entirety of the TableViewController’s array.  
* 2030-I’ve got everything figured except for the segue.  It keeps using a modal transition even though I explicitly defined it as a show segue.  It’s getting late to I’ll have to cover it tomorrow.
