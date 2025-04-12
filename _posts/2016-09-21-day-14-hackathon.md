---
title: Day 14-Hackathon
date: 2016-09-21T12:32:27.000Z
tags: coding-bootcamp
---
![Day 14](/assets/day14.jpg)

Timeline:
* 0740-Apparently there’s an Amazon Alexa Hackathon today at the dojo in addition to the Swift Hackathon that the class will be conducting.  I went ahead and registered for the event on Meetup and rebooted my Amazon developer account out of suspended animation.  I also looked into some existing web services for ideas on the Swift JSON hackathon.
* 0915-They’ve divided us into groups and written down some existing web APIs that we can use.  Time to get hacking.
* 1100-Same algo time, same algo place.  Today we covered min and max heaps and their functions.  Which was really helpful because I didn’t have a terribly strong grasp of heaps prior to today other than that they have either the biggest value or the smallest value as their root.  What I learned was that they are actually represented as arrays, but that you could visualize them as a tree where each parent was just bigger (or smaller if a min-heap) than its children.  When you insert a value it goes to the bottom right and bubbles up until going any further would break that rule.  When you pop the root value the bottom right value takes it’s place and bubbles down going any further would break that rule.  The way that all these nodes are represented in the array is that the left child’s index of a node is that nodes index x 2, the right child’s index of a node is that nodes index x 2 + 1.  Brilliant!
* 1300-Back to our Game of Thrones app.  The best part of this project so far has been that we’re all working on our own computers (as opposed to the single computer pair programming we were doing in previous projects) which means lots of git merge practice (we’re pulling the origin master, merging with our branch, and pushing every two hours in sequence).
* 2000-Well, time to go home.  The project is not ready for prime time yet, but we don’t have to present until 1000 tommorow.
