---
title: Jetpack Compose Lessons Learned
date: '2024-02-04T16:11:37-08:00'
tags: android 
---
![Winter Landscape](/assets/winterlandscapejpeg.jpeg)

Over the past couple months I've had the opportunity to refactor large swaths of our app user interface from XML to jetpack compose.  Throughout the process I've come to gain an appreciation for some of the sharp edges that jetpack compose still has as February 2024.  My hope is that by reading this blog post you might avoid cutting yourself on the currently jagged APIs of compose, at least in the same places that I did.

One of the more painful experiences was the conversion of our app's form fields.   Each field required independent validation logic, as well as collective form validation that determined whether the user was allowed to progress to the next screen.  In the first implementation of the refactor I relied on state hoisting to allow us to simply re-use the validation logic in our viewmodels.  This worked great on our debug builds.  Unfortunately we found that once obfuscation was applied the responsiveness of the fields to user input was crippled.  For every ten keystrokes a single character might appear.  To their credit, [Google admits the TextField widget shortcomings and has recommended maintaining state and validation logic as close to the composable as possible](https://medium.com/androiddevelopers/effective-state-management-for-textfield-in-compose-d6e5b070fbe5).  Once these changes had been applied the fields responsiveness improved dramatically.  

To persist each field's content we used `remember` variables backed by mutable state. In jetpack compose once a variable is initialized through a `remember` block however it can never be re-initialized, regardless of how many times you receive new state observations. We also found that `remember` variables  are best used with primitive data types.  Once we understood these limitations though transferring state from the viewmodel to the UI was straightforward enough

Unfortunately moving our validation logic to the compose layer meant that we had to move our regression tests too.  What had previously been unit tests now had to be converted to AndroidTests, since jetpack compose relies on android instrumentation.  This migration brought its own challenges. While jetpack compose views and XML views are interoperable, Espresso and ComposeTestRule interoperability is unreliable at best.  Examples include the following:

* XML AlertDialogs only allowed interactions with one of their buttons.  Espresso could not select anything other than the primary button if the test also relied on ComposeTestRules.
* XML TimePicker dialogs could not be interacted with at all.

To get around these conflicts the AlertDialogs, TimePickerDialog, and DatePickerDialog were all converted to compose.  This brought new problems however.  The Date and Time picker compose functions do not yet allow you to assign test tags to their subcomponents.  In order to get around the test tag limitation it was necessary to look up the pre-assigned content descriptions.  In jetpack compose tests you can view the full semantics tree of any arbitrary element, including content descriptions, text values, and actions, by selecting the desired node and calling `printToLog` with your own TAG, i.e.

```
composeTestRule.onNodeWithTag("id_date_picker").printToLog("MyTag”)
```

This causes all of the properties of the given node, as well as all of its subnodes, to be output to logcat.  A small sample of the DatePicker is shown below:

```
| Text = '[Tuesday, January 16, 2024]'
| Role = 'Button'
| Focused = 'false'
| Selected = 'false'
| Actions = [OnClick, RequestFocus]
| MergeDescendants = 'true'
|-Node #142 at (l=411.0, t=841.0, r=537.0, b=967.0)px
| Text = '[Today, Wednesday, January 17, 2024]'
| Role = 'Button'
| Focused = 'false'
| Selected = 'false'
| Actions = [OnClick, RequestFocus]
| MergeDescendants = 'true'
|-Node #145 at (l=537.0, t=841.0, r=663.0, b=967.0)px
| Text = '[Thursday, January 18, 2024]'
| Role = 'Button'
| Focused = 'false'
| Selected = 'false'
| Actions = [OnClick, RequestFocus]
| MergeDescendants = 'true'
|-Node #148 at (l=663.0, t=841.0, r=789.0, b=967.0)px
```

Once a suitable element is identified, you can access it using its preassigned content description, i.e.

```
composeTestRule.onNodeWithText("Tuesday, January 16, 2024").performClick()
```

This approach allows you to interact with the compose elements in instrumented tests just like a user might.  It should also give you more of an appreciation for the importance of content descriptions, particularly from an accessibility standpoint.

While all of the lessons learned above were painful to earn, I still feel that the conversion from XML to jetpack compose was worth it.  The advantages of being able to preview screens as you edit them, to declaratively express UI, and to composite multiple compose functions into a single, complex UX,  collectively outweigh the drawbacks that compose currently suffers from.  Furthermore, as compose becomes the standard for the implementation of Android user interfaces I expect that many of the problems listed above will be resolved by Google.
