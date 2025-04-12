---
title: Swift & HackerRank (Part 2)
date: '2022-02-01T06:46:37-08:00'
tags: algorithms swift kotlin
---
![Swift and Hacker Rank](/assets/kt.png)

This is the second part in the series on Swift performance.  If you haven't read the first part, <a href="/post/swift-hackerrank-part-1/">you can find it here</a>.

In the first part we analyzed two different implementations of Queues in Swift: one with a linked list (the list structure, non-existent in Swift, had to be created from scratch), and  another from an array.  What we found was that the array implementation was 500% faster than the linked list implementation.  But what about Kotlin?  Kotlin builds on Java, which has a whole stable of studiously hand-crafted data structures and algorithms.  Unlike Swift, Java has [Queue's](https://docs.oracle.com/javase/8/docs/api/java/util/Queue.html), although they're exposed as interfaces rather than concrete classes.  But there are a number of pre-built data structures that conform to the queue interface, to include: 

* ArrayBlockingQueue
* ArrayDeque
* ConcurrentLinkedDeque
* ConcurrentLinkedQueue
* DelayQueue
* LinkedBlockingDeque
* LinkedBlockingQueue
* LinkedList
* LinkedTransferQueue
* PriorityBlockingQueue
* PriorityQueue
* SynchronousQueue

To keep things simple I just used a LinkedList.  I ran the tests in Android Studio using a [Kotlin Scratch](https://kotlinlang.org/docs/run-code-snippets.html#running-as-repl) (Kotlin's current equivalent to a Playground) on the same machine that I ran the Swift Queue Playground to keep performance relative. I used the following logic

```kotlin
import java.util.*
import kotlin.system.measureTimeMillis

var ll : Queue<Int> = LinkedList()
val time = measureTimeMillis {
  for (i in 0..1000) {
    ll.add(i)
  }

  for (i in 0..1000) {
   ll.remove()
  }
}

print("time $time")
```

When I ran the test I simply couldn't believe the results.  It took just over 1 millisecond to complete all 1,000 enque and deque operations! That's compared to the 1.275 seconds that the Swift array implementation took.  That means the Kotlin implementation is 10,000% more efficient than Swift's!

In order to double check that the operations were actually taking place I even added print statements outputting the indexes being added and removed.  That increased the final runtime to 253 milliseconds, conceivably because of the overhead of the print statements.  For reference, this was the logic that I used for the Playground performance test (since I didn't include it in the original post).

```swift
import UIKit
import XCTest

class MyTests: XCTestCase {

  func testQueue() {
    measure {
      var q = Queue<Int>()
      for i in 0..<1000 {
        q.enqueue(i)
      }

      for _ in 0..<1000 {  
        q.dequeue()
      }
    }
  }
}

MyTests.defaultTestSuite.run()
```

Having completed this analysis, I'm not sure which is more startling, the fact that Kotlin is so much more performant than Swift, or the fact that despite it's limitations, it is significantly easier to program performant apps in iOS than it is in Android.  Food for thought until next time!
