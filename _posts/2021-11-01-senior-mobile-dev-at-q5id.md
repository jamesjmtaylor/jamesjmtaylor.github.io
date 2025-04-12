---
title: Senior Mobile Dev at Q5id
date: '2021-11-01T05:52:06-07:00'
tags: life xamarin c#
---
![Fingerprint](/assets/fingerprint.jpg)

I've been at my new job at [Q5id ](https://q5id.com/)for almost half a year now.  I was hired as a Senior Mobile Developer for the Proven Identity mobile application team.  The company is a moderately sized startup in Hillsboro, Oregon.  Since I've been here, we've received a $10 million dollar funding round and signed our first customer.  I'm optimistic that with continued hard-work we'll be able to keep up the momentum throughout the year, and that this customer will be the first of many.

Regarding the technical background of my work; we use Azure for our hosting.  Our backend uses the .NET framework, while our frontend leverages the Xamarin framework.  Both frontend and backend are written using the C# programming language, with a few exceptions.  Q5id derives it's name from the 5 factor enrollment process that the mobile app I work on, Proven Identity, uses.  The steps are: 

1. Face liveness and recognition
2. Right and left palm liveness and recognition
3. Voice match
4. ID scan and biometric data input
5. Review by an Enrollment Services employee.

Liveness is performed on device using exported ML  (Machine Learning) models.  Only after liveness is confirmed is a biometric hash generated and submitted to our servers for matching.  A biometric hash is used so that we don't have to store actual images of user faces or palms in our databases.   The one-time enrollment process takes less than 3 minutes from start to finish.  Once it's done, the default verification process uses the user's face and right palm to authenticate.  This is adjustable by the user or their organization.

While working at Q5id I've learned a lot about authentication (validating that a user is who they say they are) and authorization (validating that a user can do and access what they say they can).  We're working to implement a superset of the Open ID Connect (OIDC) standard, as defined in [RFC 8414](https://datatracker.ietf.org/doc/html/rfc8414).  Our application currently conforms to the OAuth2.0 Authorization Code Grant, which is formally specified in [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749).  Given that OIDC is itself just a superset of OAuth2.0, we've got a pretty clear cut roadmap of what we need to do to realize that goal.  In any case, it's been fascinating learning the ins and outs of biometric verification.

That should just about bring you up to speed on my role at Q5id.  I've gathered some great insights with regards to security during my time here, but I'll save that for a future entry.  Until next time!

Photo by <a href="https://unsplash.com/@georgeprentzas?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">George Prentzas</a> on <a href="https://unsplash.com/s/photos/fingerprint?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
