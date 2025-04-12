---
title: Day 5-Catchup
date: 2016-09-10T05:04:32-07:00
tags: coding-bootcamp
---
![Day 5](/assets/day5.jpg)

Timeline:

* 0745-It’s a Saturday, so traffic was really light and the Dojo parking lot was next to empty, so I parked there instead of my usual park-and-ride spot almost a mile away.  I continue work on my Calculator app.
* 0915-I’ve completed my work on the Calculator App and commit it to my GitHub.  One of the cool resources that I used was http://unicode-table.com/en/ which allows you to copy special characters which you can then paste directly into your Xcode.  Guess I should have made a rogue-like for yesterday’s competition!  

I’m starting work on the Great Number Game.  It appears to be a lot simpler than the last app.  All it does is tell you to guess a number between 1 and 100 and then displays a popover telling you if your guess is right or wrong.  Initial plan is 30 minutes on the storyboard, 30 minutes wiring it up and implementing the logic with console output, and 60 minutes figuring out how to switch that console output to popover output.

* 1430-My estimates for the storyboard and the logic were spot on.  The popover output estimate was overly optimistic.  Popovers only work on iPads, so I needed to find a different component to use.  I managed to create an alert dialog with the required content, but alert dialogue styling is strictly governed, preventing me from mimicking the storyboard in the video. I tried creating my own Cocoa class and ViewController, but kept getting casting errors.  This was despite the fact that I set my new class to inherit directly from the prototypical UIViewController class.  So I’m putting it on the back burner for now.  On to next week’s assignments!

* 1530-One of the other students here, Tom, showed me where I erred in my Great Number Game app.  Apparently you have to set all the text fields (title, message, buttons, etc) during initialization.  Changing them afterwards leads to unpredictable behavior.  Making this small tweak I was able to get the interface identical to that in the video using alert dialogues.  I feel a lot better now that it’s implemented exactly as it’s supposed to be.

* 1930-Well, I got almost halfway through Monday’s reading, and ran into a couple troubling inconsistencies in the lesson code.  I’ll just have to wait until Monday to ask about it.
