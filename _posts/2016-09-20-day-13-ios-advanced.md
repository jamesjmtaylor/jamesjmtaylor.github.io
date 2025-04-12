---
title: Day 13-iOS Advanced
date: 2016-09-20T12:30:56.000Z
tags: coding-bootcamp
---
![Day 13](/assets/day13.jpeg)

Timeline:

* 0745-I asked David as soon as I got in and he had it cleared up in 5 minutes.  Apparently it’s not sufficient to have a tabNavigation controller embedded in the TableViewController, you also have to have a regular navigationController embedded as well for the show segue to function.  Otherwise it just defaults to a modal segue.
* 0915-David calls the class together and we address the development of our own back-end servers for our mobile apps.  He shows us a convenient mocking scheme known as json-server that installs and runs in under a minute (literally less than 60 seconds, it’s pretty amazing).  After a little bit of experimenting I found that I can host stand-alone .json files on my GoDaddy root folder though, and as long as I have a URL to that folder I can just have my apps call that instead.  Definitely something I’m going to leverage during project week.
* 1100-It’s algorithms time.  No song today. We’re covering HashMaps again and today we’re asked to implement .get, .set, and .grow.  The .grow was the most interesting implementation, although not too terribly challenging.
* 1300-The Star Wars app is finished and uploaded to my GitHub.  Our next project is going to involve connecting our old Bucket List app to our own backend server.  I’m a little nervous, especially concerning whether or not GoDaddy will support POST, PUT, and PATCH routes (I’ve already implemented GET).
* 1500-David calls us up to the board again to discuss JSON CRUD implementation for a RESTful app.  He goes over the keywords, POST, PUT, DELETE, and GET as well as how they are integrated into a URL request.  Afterwards we all go back to work on our Bucket List App 
* 1945-Well, that’s it.  I’ve completed all of the required learning modules.  The Bucket List app now has full JSON REST support and is uploaded to my GitHub.  I’ve completed the end of course survey. Overall it was a punishing pace for last three weeks but we’re done now.  Tomorrow is another hackathon, probably revolving around the implementation of a RESTful app.  Thursday is a universal catch-up day.  Friday we go for our Black Belts.  Then next week will be completely devoted to final projects.
