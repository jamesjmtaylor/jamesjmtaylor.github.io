---
title: Lessons Learned at Nautilus (Part 3)
date: '2021-10-02T09:22:43-07:00'
tags: life android ble
---
![Droid](/assets/droid.png)

In my two and a half years at Nautilus I learned a tremendous amount about IoT, Hardware, Software, Data Science, and myself in general.  This will be the last entry in my Nautilus lessons learned series and will cover how hardware can unintentionally impact software and the benefits of the Android OS for IoT.

One of the best parts of my time at Nautilus was all the firmware and electrical engineers that I got to interact with and learn from.  My undergraduate degree was in computer science.  The syllabus had exactly two electrical engineering courses, "Digital Computer Logic" and "Introduction to Computer Architecture". I got a little bit into electromagnetic theory and Maxwell's equations in my Advanced Physics course, but ultimately only scratched the surface of all the content available in electrical engineering.   My computer science education covered a lot of the theory of computer operation and programming.  Data structures, algorithms, operating systems, computer graphics, etc.  While at Nautilus I had the privilege of revisiting many of the electrical concepts that I had only briefly touched on while in school.

Nautilus, as a fitness equipment company, is enmeshed in firmware and hardware development by necessity.  These systems would impact the operation of our software systems more than you would expect.  The most noteworthy instance was an issue with heartrate logging on our BXT116 treadmills when connected to the JRNY app.  When I was assigned the bug both our iOS and Android apps were reporting long stretches of a flat heart rate.  That is, it would report 160 beats per minute for 5 minutes, then 170 for 4 minutes, then 156 for 8 minutes, and so on.  Digging into the iOS code I found that we had set the threshold for our Douglas-Peucker algorithm too high.  The algorithm uses interpolation to smooth out rapid jumps in a graphed line.  Because the threshold was too high, it flattened the graphed beats per minute into a line that would only change after a fairly significant change in heart rate.  A fairly straightforward software issue to fix.

Android was quite different.  I found our interpolation was just fine on that platform.  Digging into it deeper I found that our BLE byte decoding logic was giving us those values, not our business logic.  Since our electrical engineers built and maintained the firmware that generated those byte values on the console, I asked them why we were getting these bytes and why the iOS logic handled them differently.  It turned out that the way that the heart rate sensor hardware was built was to blame.  Specifically, the heart rate band that shipped with the BXT116 operated in the 5khz range.  This range is not regulated by the FCC, and is thus subject to electrical interference.  In our case the treadmill motor, when under the load of propelling an individual while they are running (which I personally did for the purposes of reproducing the bug as exactly as possible), generates an electromagnetic field in the 5khz range.  On iOS the byte decoding logic would recognize this as noise and remove it from the heart rate date.  But on Android the byte decoding logic would coerce the noise into the nearest reasonable heart rate reading.  Once this bug was found we updated the Android Bluetooth logic to match that on iOS and the issue was fixed.

 Now for the benefits of using the Android OS for IoT development.  When I was at Steelcase there was a big debate about whether to use Android or Linux for the next generation of Room Wizards, which were low-power room scheduling touchscreens located outside of each conference room.  When I joined Nautilus the debate had already been won in favor of Android.  What was remarkable was that at Steelcase the largest proponents of using Linux for IoT were the firmware engineers. It was what they knew and used on a daily basis.  The mobile developers (myself included) had to fight tooth and nail for Android.  But at Nautilus the firmware engineers were the champions for the Android OS.  When I asked one of them why he had some pretty insightful answers:

1. Android is much more locked down than Linux out of the box.
2. Android userland management is much more straightforward.
3. Android UI application development is cleaner in terms of licensing.
4. There is a much wider selection of mobile device management (MDM) companies for Android.

By his own admission Android was extremely "bloated" for what we were using it for in comparison to Linux.  But in his opinion the benefits outweighed the drawbacks.

I hope this 3-part overview of my time at Nautilus has been enlightening; it certainly was for me!
