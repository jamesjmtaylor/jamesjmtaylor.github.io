---
title: Swift & HackerRank (Part 1)
date: '2021-11-28T07:01:40-08:00'
tags: algorithms swift
---
![Swift and Hacker Rank](/assets/swifthackerrank.png)

I recently practiced coding challenges on HackerRank with Swift.  The reason being my day job consists almost exclusively of C# development, so a little bit of hacking with Swift seemed like a good idea to keep my native development skills sharp.  This would mean going back and implementing basic algorithms and data structures in Swift.

One of the most basic data structures in computer science is the queue.  Queues are known as FIFO systems, First In, First Out. You can implement Queues a few different ways, but the most common implementations are as a doubly-linked list or as an array-this is where things get interesting.  

On paper, a doubly linked list implementation should be more efficient.  It's a single operation to deque an object, and a single operation to enqueue an object.  It's also more memory efficient in terms of size, because it takes up exactly the number of objects that it is filled by.  A possible implementation of a doubly linked list queue is below:

```swift
struct LLQueue<T> {
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
    public var isEmpty: Bool { return head == nil }

    init() {
        head = nil
        tail = head
    }
    
    mutating func enqueue(_ key: T) {
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
    
    mutating func dequeue() -> T? {
        if self.head?.data == nil { return nil  }
        let ret = head?.data
        if let nextItem = self.head?.next {
            head = nextItem
        } else {
            head = nil
        }
        return ret
    }
    
    func peek() -> T? {
        if self.head?.data == nil { return nil  }
        return head?.data
    }
}
```

An array backed queue is certainly more elegant when written out:

```swift
struct Queue<T> {
 private var q = [T]()
 func isEmpty() -> Bool {
  return q.count == 0
 }
 
 func peek()-> T? {
  return isEmpty() ? nil : q[0] 
 }

 mutating func enqueue(_ v: T) {
  q.append(v);
 }

 mutating func dequeue() -> T? {
  return isEmpty() ? nil : q.removeFirst()
 } 
}
```

But how does it compare to a doubly-linked list implementation?  In theory much worse.  Swift arrays are dynamically resizing.  When you append an element it will take the next available empty space in the array.  If space isn't available however, it will create an entirely new array, with double the original space, and then copy all the elements from the old array into the new array.  Needless to say this is a lot more operations than the first implementation's append method required.  Fortunately this doubling is fairly seldom required, translating into a constant time enqueue operation when amortized.

The bigger concern is the deque operation.  Because you are removing from the front of the array, Swift needs to shift everything over to the left.  This will always take O(n) time, where 'n' is the number of elements in the array.  This is significantly worse than the constant time of O(1) that the doubly-linked list implementation takes.  

But what about in practice?  Well, here are the results after instrumenting 100 enqueue and deque operations executed in 10 iterations:

```log
Test Suite 'MyTests' started at 2021-11-10 10:12:20.331
Test Case '-[__lldb_expr_23.MyTests testLLQueue]' started.
<unknown>:0: Test Case '-[__lldb_expr_23.MyTests testLLQueue]' measured [Time, seconds] average: 5.597,
relative standard deviation: 2.909%, 
values: [5.914082, 5.454277, 5.660311, 5.526723, 5.517859, 5.488948, 5.470160, 5.895244, 5.529500, 5.509556],
performanceMetricID:com.apple.XCTPerformanceMetric_WallClockTime, 
baselineName: "", baselineAverage: , polarity: prefers smaller, 
maxPercentRegression: 10.000%, maxPercentRelativeStandardDeviation: 10.000%, maxRegression: 0.100, maxStandardDeviation: 0.100
Test Case '-[__lldb_expr_23.MyTests testLLQueue]' passed (56.221 seconds).

Test Case '-[__lldb_expr_23.MyTests testQueue]' started.
<unknown>:0: Test Case '-[__lldb_expr_23.MyTests testQueue]' measured [Time, seconds] average: 1.275,
relative standard deviation: 0.330%, 
values: [1.279142, 1.274859, 1.283349, 1.274912, 1.270122, 1.267840, 1.271426, 1.275260, 1.276281, 1.274089], performanceMetricID:com.apple.XCTPerformanceMetric_WallClockTime, 
baselineName: "", baselineAverage: , polarity: prefers smaller, 
maxPercentRegression: 10.000%, maxPercentRelativeStandardDeviation: 10.000%, maxRegression: 0.100, maxStandardDeviation: 0.100
Test Case '-[__lldb_expr_23.MyTests testQueue]' passed (12.999 seconds).
Test Suite 'MyTests' passed at 2021-11-10 10:13:29.553.

  Executed 2 tests, with 0 failures (0 unexpected) in 69.220 (69.222) seconds
```

What's this!? The array based queue was almost 5x faster than the doubly-linked list implementation?  But why is this?  Well, it comes down the compiler and the runtime.  Arrays are a standard library function in Swift while doubly-linked lists are not, and must be implemented from scratch.  This is why the doubly-linked list queue implementation is considerably longer in terms of code than the array-backed queue.  Because of this the compiler is able to make a lot of optimizations for the array-backed queue that it simply can't for the doubly-linked list queue.  But that's only part of it.  The bigger issue is with how the runtime allocates memory.  For the array the runtime is able assign a contiguous block of memory.  This makes memory traversal _very _efficient.  To access the next element you just go to the adjacent memory address.  You also don't need to worry as much about memory assignment for new elements.  It's all handled in batches, with an amortized constant time.

The doubly-linked list has none of these advantages.  When a new element is added the runtime has to search for an empty memory address.  Furthermore, the empty address isn't guaranteed to be adjacent to the last object in the list, and in fact, it probably won't be.  This means that you could have to travel quite a ways between each element.  

Now this is not to say that a doubly-linked list implementation of a queue is always wrong.  Some languages, like Java and Kotlin, have native library support for these kinds of data structures.  Because of this it is conceivable that they may actually be more efficient than array-based implementations.

 Next month we'll cover some of these language runtime optimization differences, and how they affect HackerRank challenges.

Image credit to the [HackerRank blog](https://hackerrankblogblog.wordpress.com/author/hackerrankblog2/page/13/)
