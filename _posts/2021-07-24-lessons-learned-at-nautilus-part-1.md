---
title: Lessons Learned at Nautilus (Part 1)
date: '2021-07-24T08:18:56-07:00'
tags: life android ios software-engineering web ble
---
![Velacore Bike](/assets/velacore.jpg)

In my two and a half years at Nautilus I learned a tremendous amount about IoT, Hardware, Software, Data Science, and myself in general.  Given the amount of ground to cover, I plan to split this topic up into the following:

1. The importance of supporting firmware OTA (Over the Air) updates from the start
2. The importance of data scientist input for analytics at the start.
3. The benefits of Android OS for IoT and how hardware can unintentionally impact software

So without further ado, the first lesson!

## OTA Update Support

OTA is critical for keeping IoT from permanently becoming part of the [IoS](https://www.reddit.com/r/theinternetofshit/). Our OTA system at Nautilus used [Terraform with AWS](https://aws.amazon.com/blogs/apn/terraform-beyond-the-basics-with-aws/), [Jenkins](https://www.jenkins.io/), and [AWS S3](https://aws.amazon.com/s3/).  Terraform codified our cloud APIs into declarative yaml configuration files.  Mobile developers could update a .yaml file for the latest app, hardware, or firmware release and then open a pull request on Github.  Once the changes were merged, a Jenkins job was triggered to deploy the changes to AWS.  New firmware binaries likewise were managed via Github, with additions to the binary collection initiated through pull requests.  A successful merge would again trigger a Jenkins job, this time updating an S3 bucket with the firmware binary file.  The "develop", "staging", and "master" branches would update our QA, Staging, and Production environments respectively.  

The JRNY Android and iOS mobile applications acted as the hub for firmware updates.  As soon as a user connected over Bluetooth to their exercise equipment JRNY would query the firmware version installed.  Provided that, JRNY would then send the firmware version, hardware type (treadmill, bike, dumbbells, etc.) and app version to the server.  The server would respond with whether an update was required or not, and if one was required it would send the S3 bucket URL that JRNY could download the update from.  JRNY could then download the firmware binary over wifi and then transmit the binary bytes over Bluetooth to the hardware.  The hardware's firmware would then execute a checksum to validate that none of the Bluetooth packets had been lost or corrupted, and then install the update.  All together it made for an incredible feat of engineering.

Hardware at Nautilus fell into one of two broad categories:  equipment that supported OTA updates via our JRNY mobile app, and equipment that didn't.  The equipment that didn't was a constant source of consternation.  The JRNY mobile app would do the best that it could to code around bugs, feature gaps, and the like with legacy hardware.  But at the end of the day the equipment there were just some things that we were stuck with.  

Our BXT116 and BXT216 treadmills were one such example.  It would have been infinitely preferable to be able to control these treadmills exclusively through the JRNY mobile application.  This is what we called a "takeover experience".  That's because JRNY control of the treadmill unlocked a plethora of incredible features.  JRNY could play video streams of running trails from locations ranging anywhere from the Alps to Appalachia, Zimbabwe to New Zealand, and adjust the incline of the treadmills to match the what was being streamed.  Likewise JRNY could dynamically adjust the speed of the treadmill based on the user's fitness profile, their active heart rate, their workout history, and the workout that they selected for that day.  Or for trainer-led video workouts JRNY might automatically program the treadmill with the running coach's recommendations.  

Because the BXT116 and BXT216 treadmills were "legacy" platforms that didn't support OTA, we were stuck with the functionality of the firmware that they shipped with in 2016.  Which was a digital readout of the speed and incline that the treadmill was currently at, and a summary at the end of the workout.  The user had to manually press the physical console START button before the workout would begin.  And they had to manually press STOP twice before they could get the summary (pressing it once would only pause the workout).  This severely hamstrung the functionality JRNY could provide.  It meant that we had to write and maintain special code just for these treadmills.  And it led to innumerable bugs (what happens if a user closes and re-launches JRNY mid workout?  What happens if the user presses STOP and then START?  What happens if they press START before selecting a workout in JRNY?).  Needless to say, the BXT116 and BXT216 treadmills hammered home to me the importance of supporting firmware OTA from jumpstreet.

Next month: The importance of data scientist input for analytics at the start.

Image credit to [https://www.bowflex.com/bikes/velocore.html ](https://www.bowflex.com/bikes/velocore.html)
