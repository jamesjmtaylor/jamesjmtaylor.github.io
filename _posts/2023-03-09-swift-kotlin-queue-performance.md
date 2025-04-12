---
title: Swift & Kotlin Queue Performance
date: '2023-03-09T06:40:19-08:00'
tags: algorithms swift
---
![Queue](/assets/queue.png)

This is an expansion on two articles that I did about this time last year: 

* <a href="/swift-hackerrank-part-1/">Swift & HackerRank (Part 1)</a>. 
* <a href="/swift-hackerrank-part-2/">Swift & HackerRank (Part 2)</a>. 

In those articles I marveled at how much faster Kotlin was compared to Swift in terms of queue performance.  While I quantified the performance in terms of order of magnitude at a specific volume (3 orders of magnitude better performance for 1,000 enqueues and dequeues) what I failed to do was generalize the differences in terms of [Big-O notation](https://en.wikipedia.org/wiki/Time_complexity).  That's something I plan to remedy in this blog post.

In order to empirically derive the time complexity I will need to run samples of various sizes, plot the points and fit a formula to each algorithm performance profile.  Kotlin uses a doubly-linked list as its underlying data structure.  My hypothesis is that the Kotlin queue will see constant-time enqueuing and dequeuing, that is the thousandth enqueue (or dequeue) operation should be just as performant as the first. Below is the Kotlin implementation that will be used to test this:

```
import java.util.*
import kotlin.system.measureNanoTime

const val ITERATIONS = 1_000_000
fun main() {
    val ll : Queue<Int> = LinkedList()
    val time = measureNanoTime {
        for (i in 0..ITERATIONS) {
            ll.add(i)
        }

        for (i in 0..ITERATIONS) {
            ll.remove()
        }
    }

    print("Time: ${time.toDouble() / 1_000_000} milliseconds")
}
```

My Swift hypothesis is a little more complicated.  If you remember from part 2 of the series, the doubly-linked list implementation was not at all performant, most likely because Swift doesn't perform any memory optimization around linked lists.  The array implementation was more performant, but still underperformed compared to Kotlin.  After digging into the Apple Developer documentation the reason became obvious.  [Enqueuing](https://developer.apple.com/documentation/swift/array/append(_:)-1ytnt) uses an array append operation, which occurs in constant time with the benefit of memory optimization like Kotlin. [Dequeuing](https://developer.apple.com/documentation/swift/array/removefirst()) however shifts all the elements to the left to occupy the space vacated by the removed element.  This requires linear (O(n)) time.  So even though it is less performant than the array at 1,000 values, we'll need to use the original Swift doubly linked list implementation in order to be able to compare apples to apples (pun not intended).  I'm guessing that while the absolute amount of time to perform each enqueue and dequeue will remain greater than Kotlin's, there will be no change in the relative amount of time between the first enqueue and the thousandth.  Below is the Swift implementation that will be used to test this:

```
import Foundation

let ITERATIONS = 1_000_000
@main
public struct swift_cli {
    public static func main() {
        let q = Queue<Int>()
        let time = measureTimeMillis {
             for i in 0..<ITERATIONS {
               q.enqueue(i)
             }

             for _ in 0..<ITERATIONS {
               _ = q.dequeue()
             }
           }
        print("Time: \(time) milliseconds")
    }
    
    static func measureTimeMillis(block : (() -> Void)) -> Double {
        let start = DispatchTime.now()
        block()
        let end = DispatchTime.now()
        let nanoTime = end.uptimeNanoseconds - start.uptimeNanoseconds
        return Double(nanoTime) / 1_000_000
    }
}


class Queue<T> {
    //note class b/c struct type cannot reference itself
    class LinkedList<T> {
        var data: T
        var next: LinkedList?
        var prev: LinkedList?
        init(_ data: T){
            self.data = data
        }
    }
    typealias LLNode = LinkedList<T>
    var head: LLNode!
    var tail: LLNode!

    init() {
        head = nil
        tail = head
    }
    
    func enqueue(_ key: T) {
        let newNode = LLNode(key)
        if let t = tail {
            t.next = newNode
            newNode.prev = t
            tail = newNode
        } else if let h = head {
            h.next = newNode
            newNode.prev = h
            tail = newNode
        } else {
            head = newNode
        }
    }
    
    func dequeue() -> T? {
        if self.head?.data == nil { return nil  }
        let ret = head?.data
        if let nextItem = self.head?.next {
            head = nextItem
        } else {
            head = nil
        }
        return ret
    }
}
```

And now on to the results!

![Results](/assets/data.png)

Both Swift and Kotlin performed poorly on the first 1,000 enqueues and dequeues.  This was probably due to initialization of the the instrumentation taking a disproportionately large amount of time.  Once we got into 10,000 operations and above however, the time per enqueue and dequeue leveled off significantly.

![Graphs](/assets/graphs.png)

Kotlin still outperformed Swift by an order of magnitude, but on average both Swift and Kotlin exhibited constant time (O(1)) enqueue and dequeue performance, confirming the hypothesis.  

Photo by <a href="https://unsplash.com/@emilegt?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Emile Guillemot</a> on <a href="https://unsplash.com/photos/x5-IRhnJkxI?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
