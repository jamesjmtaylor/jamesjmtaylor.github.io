---
title: Real-World Applications of Leetcode
date: '2023-10-03T06:42:34-07:00'
tags: algorithms 
---
![Call For Fire](/assets/cff.jpg)

As I've been grinding through LeetCode problems I've been reminded of some of the real-world applications of the algorithms used during my past life as an Army officer.  The "Call For Fire" (CFF) is one such example.  

A CFF is a radio request for artillery fire and is performed by a Forward Observer (FO) using a map, compass, protractor, binoculars with reticle pattern, and a target.  Before contacting the artillery battery, the FO makes their best guess with regards to the location of the target on their map.  They can do this either by determining their location using a GPS and the direction to the target using a compass and then estimating the distance, or by using terrain association.  In the latter method,  you associate the targets proximity to prominent terrain, i.e. they are 500 meters west of hill 505.

Once an estimate of the target location is made, the FO sends the initial CFF request.  This can be done in one of two ways: grid or polar.  For the purposes of this description we'll assume we're using grid.  The request sounds something like this:

FO: HATEFUL, THIS IS APOCALYPSE, ADJUST FIRE, OVER

Artillery:  APOCALYPSE, THIS IS HATEFUL, ADJUST FIRE, OUT

FO: GRID NOVEMBER GOLF190513, OVER

Artillery: GRID NOVEMBER GOLF190513, OUT

FO: INFANTRY PLATOON IN THE OPEN, DPICM IN EFFECT, OVER

Artillery: INFANTRY PLATOON IN THE OPEN, DPICM IN EFFECT, OUT

FO: DIRECTION, 1650, OVER

Artillery: DIRECTION, 1650, OUT

The artillery battery will then fire a single round to determine if the FOs estimated location was accurate or not. Once the first round is fired, the artillery battery will notify the FO with "Shot, over", to which the FO acknowledges with "Shot, out." Then, based on their ballistic computer calculations, the artillery battery will notify the FO of the expected impact with "Splash, over", to which the FO acknowledges with "Splash, out".  If the round impacts within 50 meters of the target then the FO requests that the remaining guns in the battery fire with "Fire for effect, over".  If the round lands more than 50 meters away then the next phase of fire begins, bracketing.  This is where our binary search comes into play.

The direction called in  the penultimate transmission is given in mils (not degrees). 20 mils are equivalent to 1.125°.  The direction is used by the artillery battery to determine how to translate the FOs adjustments into grid coordinates using their ballistic computer.  If the round impacts to the left of the target, the FO will naturally request that the artillery be shifted to the right.  If the round impacts short of the target, the FO will recommend that the artillery be shifted further away from them and towards the target.

These adjustments aren't performed randomly however.  They utilize a type of binary search pattern, known as bracketing, as illustrated below:

![Bracketing](/assets/bracketing.png)

In the search, the FO adjusts the round more than twice the distance of what he thinks it should require.  If the impact is on the other side of the target the bracket is successful and he can adjust in the other direction by half the distance of the previous adjustment.  This is done until a round lands within 50 meters of the target. Once the target has been successfully bracketed, the FO requests that the remaining guns in the battery fire with "Fire for effect, over".

I hope you've found this example of a real-world application of binary search to be informative.  There is significantly more detail to the CFF than this brief overview includes.  You can read more in [ATP 3-09.30 "Observed Fires"](chrome-extension://efaidnbmnnnibpcajpcglclefindmkaj/https://armypubs.army.mil/epubs/DR_pubs/DR_a/pdf/web/ARN5011_ATP%203-09x30%20FINAL%20WEB.pdf) by the Army Publishing Directorate.  Until next time!
