---
title: Worldwide Equipment Guide
date: '2018-06-04T04:59:15-07:00'
tags: ios android
---
![WEG Cover](/assets/cover.png)

It's been a while since I last posted.  Truth be told I've been rather busy with the final release of my Worldwide Equipment Guide 2015 apps for iOS and Android.  I released the [Android app](https://play.google.com/store/apps/details?id=com.jamesjmtaylor.weg2015) at the end of April.  The [iOS app](https://itunes.apple.com/us/app/worldwide-equipment-guide-2015/id1392413944?ls=1&mt=8) was just recently (as of June 1st) released.  I've already received some really positive feedback from the Army as well as some feature requests they'd like.  Top on the list is filtering by region or country to aid soldiers in preparation for a particular theater of operations.

The Army also gave me the 2016 edition of the Worldwide Equipment Guide, which was just recently released, to update some of the statistics. In the previous 2012 iteration of my app that would have meant that I would need to release a new binary.  But the 2015 edition is backed by a RESTful JSON api, so that I can add and remove equipment on the fly.  The API uses the Spring Boot framework, is written in Kotlin, uses PostgreSQL for object persistence and Jackson for JSON serialization & deserialization.  All of it is hosted on a Digital Ocean droplet for the same cost as a monthly Starbucks coffee.  

Prior to releasing the API I did a scripted load test to find its breaking point.  The script simulated 500 simultaneous connections to the server. Amazingly, all 500 of the requests were resolved in 4 seconds, generating a plaintext JSON output file 253mb in size.   While there are certainly optimizations that I could be made to the API to increase performance further, the current number of WEG users don't even come close to that number of simultaneous requests.

So if you haven't already, go get a copy of the app for yourself.  They're available on the Google Play Store and Apple App Store, and both are free!
