---
title: AAR pt 8 (Architecture/Software Engineering)
date: '2018-07-01T07:08:20-07:00'
tags: software-engineering aar android
---
![Android architecture](/assets/architecture.jpg)

If you haven’t had a chance to read the first entry in the series for context, [you can do so here](/after-action-review-aar/)

A lot of software development is more general than the idiosyncrasies of this framework over that.  Over the last year I've come across a few pretty handy rules of thumb to use while planning or implementing features.  A few of these are more technical (i.e. web tokens) but were included because their application spans a breadth of platforms.  Others are more general but opinionated. As an example, the first item in the list below is a more functional paradigm that others may or may not agree with, but that I have in my experience found to help reduce the overall number of bugs in production code.

* You should use immutable (they can't be changed, or _mutated_) objects where possible for speed and thread safety.  If you need to make a change you should create a new immutable copy of the object with the changes applied to it.
* Always time-box an attempt to integrate a new open-source library.  If you're not done at the end of the exploration, capture your learnings, disseminate them to the team, and either petition for more time or to time-box a different framework option.
* ARM stands for Advanced Reduced Instruction Set Computing (RISC) Machine, and is used to power IoT, smartphones, RaspberryPi's, and BeagleBoards.  It is not compatible with x86 Complex Instruction Set Computing (CISC) that most desktop computers use. Applications developed for ARM must be recompiled for x86 and vice versa.
* JSON Web Tokens (JWTs) can be deconstructed and constructed at [JWT.io](jwt.io), allowing you to see what info is encoded in the token itself (i.e. expiration time, object id, etc). You can encode attributes into a token (i.e.  expiration time, userId, etc.) yourself as well.
* BFS and DFS are identical in time complexity (unless searching for the shortest path between two points, which is best served by a binomial BFS).  Space complexity depends on whether the graph is wider or deeper.
* For all code reviews:

1. Go through the functionality
2. Show and describe the code
3. Go through the unit tests
4. Address any remaining questions

* The "Rule of 30" is that there should be no more than 30 lines per method, 30 methods per class, 30 classes per module, etc.  This reduces complexity and overall bug density.
* Most modern programming languages are now pass reference by value; that is to say that they pass values to methods through values, the value being the reference to the object.  That means that during a method call there are two values that point to the same object.  The easiest way I've found to remember it is that it's like tying a second string to a balloon.  If you cut one of the strings (i.e. set the reference to null in the method) you still have the other reference to retain the object in memory.
* Their are two ways to organize projects, package by layer (i.e. ViewControllers, Views, etc.) and package by feature (i.e. Login section, Tab 1 section, etc).  The former is easier starting out since there's no question where different filetypes go.  The latter is easier for long term projects though because new developers (or old, returning developers) won't have as many questions as to where to find the files to implement a new feature.
* A Bloom Filter is a Hashmap of Bools intended to track if a value exists in a Database or Network call.  Each value in the database or network is hashed with 3 different hashes and then the value is marked as true at each of the hash indexes.  Then for lookup if you get 3 trues the value MAY be in the database or network.  But any less than that and the value definitely isn't in the database or network.  This solution is more space efficient than a POJO hashmap and more time efficient than querying the database or network each and every time.  The larger the bloom array the more certainty that the object queried is actually present.  
* The "copy-left" moniker refers to licences that force you to open-source your code if you use that licensed library.
* Kerning refers to the space between a pair of specific letters and hails back to the printing press when letters would have angles cut out of their blocks so they could nest better with other letters, the characters 'AV' being one example.  Letter spacing on the other hand refers to the spacing between all letters.
* There are 3 main Timezone encodings: Windows (i.e "Pacific Standard Time") IANA (i.e. "America/San-Francisco"), and offset (i.e. "+0800")
* Hungarian notation is just prefixing variables with a lower-case letter.  Examples include s for singleton (i.e. "sInstance") m for main (i.e. "mVar").  
* The key to reactive programming is "Push, don't pull". For example, VMs shouldn't have getters, just an update() method that pushes a static state to it's observers.
* Override Java's 'clone()' method of Objects judiciously.  That's because it does NOT create deep copies of the mutable objects that may be referenced as attributes of the original object.  To make deep copies of those you need to override their 'clone()' methods and call them as well in the original object's 'clone()' call.

Photo by Daniel McCullough on Unsplash
