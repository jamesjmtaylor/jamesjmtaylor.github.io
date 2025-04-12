---
title: Lessons Learned at Nautilus (Part 2)
date: '2021-09-05T07:25:40-07:00'
tags: software-engineering
---
![Circuit Board](/assets/circuitBoard.jpg)

In my two and a half years at Nautilus I learned a tremendous amount about IoT, Hardware, Software, Data Science, and myself in general. Last month I covered the importance of supporting Over-The-Air (OTA) updates for hardware from the start.  Today I'll be covering how critical data scientist input can be when starting a project.

When I was hired at Nautilus, all of our application analytics were handled by the Adobe Analytics SDK.  The SDK uploaded analytic actions (things a user did) and states (things a user experienced) to S3, where a nightly job transferred the accumulated data into Azure Databricks.   Our product owners could then run queries on the dataset using Microsoft Power BI to answer questions the business might have about our customers in general.  

The problem with this setup was that we didn't have a dedicated data scientist to validate the coherence or completeness of the data.  The mobile development team did it's best to capture every action or state that they thought might be relevant.  Unfortunately we just couldn't anticipate all the questions the business might have, or the edge cases around the data.

It wasn't until we acquired a team of dedicated data scientists that the unknown unknowns were revealed.  We realized that we didn't have the data to answer questions like "who's the favorite virtual trainer for bicyclists?"  Such a question had clear business importance because it would allow us to provide our users with more content from that coach and less from other trainers. We simply had not anticipated it as a question when creating our analytics collection points.  

Answers to other questions had to be painstakingly reconstructed by the data scientist team. In one example, the average length of a treadmill workout had to extrapolated using the timestamps of the workout start analytics action and the workout saved analytics state as associated by the session id.  This required a lot of work in Microsoft Power BI that might have been avoided if the mobile team had simply sent the workout duration of each workout by exercise equipment model as its own analytics action.

You might recall that I mentioned user edge cases, which brings me to my final point, data validation.  Our product owners were brilliant at their job: to represent the voice of the customer in the development of the JRNY app.  However, because they weren't data scientists they missed some analytics edge cases that seem glaring in hindsight.  For example, the mobile team made sure to always send the user's machine model whenever a new analytics state or action was captured but we missed the edge case where users weren't connected to a machine. This caused the machine model to be empty.  Because there wasn't a data scientist validating the quality of the data, these edge case failures went unnoticed for over two years.  That meant two years of potential insights into our users' behavior was lost.  

All of this is to say that having a data scientist on your team from the start is critical to having any kind of robustness to your analytical insights.  Unfortunately, I think this is a mistake that a lot of companies make.  [For this reason is should come as no surprise that on average 80% of a data scientists' time is spent cleaning incomplete or incoherent big data.](https://www.forbes.com/sites/gilpress/2016/03/23/data-preparation-most-time-consuming-least-enjoyable-data-science-task-survey-says/?sh=635521b86f63)

Photo by <a href="https://unsplash.com/@lazycreekimages?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Michael Dziedzic</a> on <a href="https://unsplash.com/s/photos/data-science?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
