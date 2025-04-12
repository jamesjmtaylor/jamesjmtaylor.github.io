---
title: Leetcode Lessons Learned
date: '2023-11-05T08:03:34-08:00'
tags: kotlin algorithms
---
As I've been working my way through Leetcode I've picked up a few new tricks.  Some of these are fairly standard, like the sliding window technique for array analysis, but others are somewhat less intuitive.  I gained a number of insights through experimentation, and these may be specific to the Kotlin compilers for Leetcode in particular. I figured they would still be worth mentioning in any case.

One of the first things that surprised me as I started the Leetcode 75 Study Plan for Kotlin was how efficient StringBuilder was.  And not just the typical "don't append strings manually because it forces the compiler to create a new string behind the scenes each time you append."  I'm talking about faster than even trying to manage a single array manually.  Take this problem statement as an example:

> You are given two strings word1 and word2. Merge the strings by adding letters in alternating order, starting with word1. If a string is longer than the other, append the additional letters onto the end of the merged string.

My naive solution was to just use a CharArray initialized to the same size as the two words provided.  That way there's no need to dynamically expand the array, it's already the exact size you need. The code is below:

```
class Solution {
    fun mergeAlternately(word1: String, word2: String): String {
        val newWord = CharArray(word1.length + word2.length)
        for (i in word1.indices) {
            if (i > word2.lastIndex) { //fill in the rest with word1
                newWord[word2.length * 2 + i - word2.length] = word1[i]
            } else {
                newWord[i*2] = word1[i]
                newWord[i*2 + 1] = word2[i]
            }
        }
        if (word1.length < word2.length) { //fill in the rest with word2
            for (i in word1.length..word2.lastIndex) {
                newWord[word1.length * 2 + i - word1.length] = word2[i]
            }
        }
        return String(newWord)
    }
}
```

This solution passed all the tests and was middle of the road in terms of memory usage, but wasn't really that performant in terms of speed, as shown below:

![CharArray Performance](/assets/chararray.png)

So I looked at the editorial to see the "preferred" solution.  That's when I saw that they were using a StringBuilder.  I copied their solution (shown below) into my code window and ran it to see how it performed.

```
class Solution {
    fun mergeAlternately(word1: String, word2: String): String {
        val sb = StringBuilder()
        var i1 = 0
        var i2 = 0
        while (i1 < word1.length && i2 < word2.length) {
            sb.append(word1[i1++]).append(word2[i2++])
        }

        if (i1 < word1.length) {
            sb.append(word1.substring(i1))
        }
        if (i2 < word2.length) {
            sb.append(word2.substring(i2))
        }

        return sb.toString()
    }
}
```

and I got the following results:

![StringBuilder Performance](/assets/stringbuilder.png)

While not a huge difference in absolute terms (26 ms and 2 MB in speed and memory usage respectively), it made a vast improvement in the relative efficiency of the solution compared to the average submission.

Here are a few more minor insights:

* To avoid comparing individual characters between two strings for equality checks, you can compute the hashes for both strings once, and then compare the hash results.  If the hashes are equal to one another, the strings are equal.
* If you need to find values that are unique between two arrays, you can convert them to sets with the extension function \`.toSet()\` and then subtract one set from the other with the \`-\` operator.  This is far more efficient than using the lambda functions filter, contains, and distinct to accomplish the same result. 
* The \`forEach\` loop structure in Kotlin is not just syntactically different from the Kotlin \`for\` loop, but semantically as well.  Behind the scenes it uses a lambda, and must return a value in each iteration.  This means that you cannot invoke \`continue\` or \`break\` like you can with a normal \`for\` or \`while\` loop. 

I hope you found these tips as useful as I did.  Until next time, happy coding!
