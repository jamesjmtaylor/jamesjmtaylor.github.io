---
title: Zen and the Art of Software Maintenance
date: '2024-06-01T06:03:21-07:00'
tags: software-engineering philosophy
---
![Zen Mountain](/assets/zenmountain.jpg)

I recently finished the book <u>Zen and The Art of Motorcycle Maintenance</u> by Robert Pirsig.    The book is a semi-autobiographical work that combines philosophy and self-help with a compelling personal narrative of mental illness. It's an excellent book that I highly recommend if you haven't read it already.  This post isn't specifically about the book per se, but the concepts of maintenance that Pirsig outlined as they apply to Software.

In the book Pirsig outlines a motivating force he broadly defines as "gumption" or enthusiasm.  Enthusiasm, he notes, is derived from the Greek word "theo", or god, and roughly translates to "filled with the spirit of god".  This motivating force is what serves as our fuel for maintenance, combatting the forces of entropy in the service of a higher power.

This fuel is replenished through meditation and depleted by internal and external forces.  Pirsig calls the external forces "setbacks" and provides a number of categorical examples.  "Out-of-sequence" setbacks occur when you disassemble a motorcycle and then try to reassemble it but do so out of order.  An example in software maintenance would be the refactoring of tech debt.  How many times have you reworked a class and its functions to be more clear or less bug-prone only to find that it doesn't quite work like it did before the refactor?  The fix, of course, are unit tests.  By writing unit tests before refactoring, you can guarantee that the behavior that you expect remains afterwards.

The next category of setback is the "intermittent problem." Pirsig gives the example of an engine that sputters only when you turn right or a headlight that flickers when you hit a bump, but the intermittent software bug (or "[Heisenbug](https://en.wikipedia.org/wiki/Heisenbug)") is probably much more familiar to you.  This category of bug is best served by the scientific method.  Gather observations, make a hypothesis, test, repeat. 

The final category of setback are what Pirsig calls "parts problems."  This occurs when you need to replace a part on your motorcycle and find that it's out of stock, expensive, or has become obsolete in the time since you bought the motorcycle.  In software this is the problem of the deprecated library or API.  Pirsig recommends that you make reliable contacts with the folks in the parts department, improvise existing parts, or even go so far as to machine your own from scratch.  The analagous behaviors in software are to contribute to existing open-source projects or even create your own when you identify an unserved need.

That wraps it up for external depleters of gumption.  I hope you found the problems and their proposed solutions as useful as I did. In the next post I plan cover internal enthusiasm depletors, or what Pirsig terms "hangups".  Until then, happy coding!

Photo by <a href="https://unsplash.com/@mischievous_penguins?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Casey Horner</a> on <a href="https://unsplash.com/photos/gray-and-brown-pagoda-temple-beside-calm-body-of-water-at-daytime-KR03PvYv3Fs?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
