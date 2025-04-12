---
title: Rust Queue Performance
date: '2023-06-05T06:12:42-07:00'
tags: algorithms rust
---
![null](/assets/rustqueue.jpg)

After creating my Starcraft Rustbot and then comparing queue performance in Kotlin and Swift, I naturally began to wonder about the performance of a queue in Rust. How would a language billed as performant as C compare? Are there truly significant gains for operating "close to the metal", or have the Swift and Kotlin compilers developed to a point where they can optimize to a similar level of performance?

Below is the implementation for my Rust queue:  

```
struct Node<T> {
    data: T,
    next: Option<Box<Node<T>>>,
}


impl<T> Node<T> {

    fn new(data: T) -> Self {
        Node { data, next: None }
    }
}



struct Queue<T> {
    head: Option<Box<Node<T>>>,
    tail: Option<*mut Node<T>>,
}


impl<T> Queue<T> {

    fn new() -> Self {
        Queue {
            head: None,
            tail: None,
        }
    }




    fn is_empty(&self) -> bool {
        self.head.is_none()
    }


    fn enqueue(&mut self, element: T) {
        let new_node = Box::new(Node::new(element));
        let raw_node = Box::into_raw(new_node);

        unsafe {
            (*raw_node).next = None;

            if let Some(tail) = self.tail {
                (*tail).next = Some(Box::from_raw(raw_node));
            } else {
                self.head = Some(Box::from_raw(raw_node));
            }

            self.tail = Some(raw_node);
        }
    }



    fn dequeue(&mut self) -> Option<T> {
        self.head.take().map(|mut node| {
            self.head = node.next.take();

            if self.head.is_none() {
                self.tail = None;
            }

            node.data
        })
    }



    fn size(&self) -> usize {
        let mut count = 0;
        let mut current = &self.head;

        while let Some(node) = current {
            count += 1;
            current = &node.next;
        }

        count
    }
}
```

I avoided using the [Rust queues crate](https://docs.rs/queues/latest/queues/) because it suffers the same penalty as Swift's array based queue, specifically a dequeue performance of (O(n)) time.  Instead I opted to use my own linked list implementation with an optional boxed data type to prevent memory overflows.  The syntax for using the queue was almost identical to that of Swift's:

```
use std::time::Instant;

fn main() {
    let mut queue: Queue<i32> = Queue::new();
    let ITERATIONS = 1_000_000;
    let start_time = Instant::now();


    for i in 0..ITERATIONS {
        queue.enqueue(i);
    }


    for _ in 0..ITERATIONS {
        if let Some(_element) = queue.dequeue() {
            // Process the dequeued element
        }
    }


    let elapsed_time = start_time.elapsed();
    println!("Execution time: {:?}", elapsed_time);
}
```

The results were as follows:

![Kotlin and Rust Results](/assets/rustresults.png)

The table above shows three Kotlin runs (KR1 through 3) and three Rust runs (RR1 through 3) with the number of `ITERATIONS` for each run as the column header.  Swift was omitted from the comparison because of the similarity of its performance envelope to Kotlin.  

In contrast to Swift & Kotlin, Rust showed almost no overhead in marshalling the memory necessary for the queue. The first enqueue operation was almost as fast (within the same order of magnitude) as the millionth.  After the first thousand enqueue and dequeue operations however, that initial startup cost had been amortized sufficiently for Kotlin to be comparable to Rust.  After the ten thousandth queue and enqueue Kotlin was even faster than Rust!  I'd really like to know how Kotlin accomplished such a feat, but that's an article for another time.  

Code generated from https://chat.openai.com/

Image generated from https://creator.nightcafe.studio/
