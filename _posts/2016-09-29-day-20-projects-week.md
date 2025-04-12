---
title: Day 20-Projects Week
date: 2016-09-29T05:42:59-07:00
tags: coding-bootcamp
---
![Day 20](/assets/day20.jpg)

* 0730-I’m starting work on the Gwent Deck manager today.  It’ll be the last full day to work on projects so I’ll have to get as much done as possible.
* 0900-We had our projects mini-scrum again.  It looks like all the groups are coming along nicely.  I’m looking forward to seeing what each has to present tomorrow.
* 1100-Algorithms time.  Today we covered graphs, as well as three different ways that they can be implemented.  The first (and most intuitive) way is as an array of Nodes with each node containing another array of nodes that it points to.  The second is as an array of tuples describing the connections between the Nodes.  The third (and most powerful) is as a 2D array that uses the indices of each dimension to describe a particular node, with the intersection of each row and column storing a boolean value stating whether or not a connection exists between the node represented by that row and the node represented by that column.  I say that this is the most powerful because you’re not limited to storing a boolean value at that intersection.  You could, for example, store a connection object at that intersection that has attributes describing that connection (travel time between the nodes, distance between the nodes, cost to travel between the nodes, etc). 
* 1920-It’s been an exceptionally productive day.  Since I implemented CoreData and JSON parsing yesterday I’ve been able to fly through Deck creation, adding 11 separate view Controllers (yesterday I only implemented 2).  The Deck management portion is complete, tomorrow I’ll get as much done with the actual game as possible.
