---
title: Holiday Season
date: '2023-12-13T06:59:09-08:00'
tags: life
---
![Turkey](/assets/turkey.jpg)

It's been a busy past few weeks.  Between Thanksgiving, Christmas shopping, a new part-time contract, family birthdays, and annual updates to my personally published mobile apps, I've had my hands full.  My regular day job responsibilities have only increased as well.  One of my new responsibilities has been the conversion of legacy xml Android layouts to Jetpack Compose user interfaces.  Over the course of this process I've really come to appreciate the declarative style of Jetpack Compose.  The ability to write tests for individual compose components has been particularly welcome.  These tests allow for test-driven user interface development and regressions in the future from the inevitable refactors that are required by the User Experience team.  I've included an example of such a test below:

```
class ButtonTest {

    @get:Rule
    val composeTestRule = createComposeRule()
    private val mockClickListener = mockk<() -> Unit>(relaxed = true)
    private val title = "Title" 

    @Before // This function is run before every function annotated with `@Test`
    fun setUp() {
        composeTestRule.setContent {            //The Modifier.testTag is critical for later identifying the node in the tests
            Button(onClick = mockClickListener,  Modifier.testTag("button")) {
                Text(text = title, modifier = Modifier.testTag("title"))
            }
        }
    }

    @Test
    fun buttonIsConfiguredProperly() {
        composeTestRule.onNodeWithTag("title").assertExists()
            .assert(hasText(title))
        composeTestRule.onNodeWithTag("button").assertExists()
            .performClick()        // Confirms the mock click listener was actually called
        verify { mockClickListener.invoke() } 
    }
}
```

This test validates that a given composable button has it's title configured properly and when you click on it the assigned click listener is triggered.  While this is a trivial test case, it does show you some of the patterns that we can use for larger, more complex composable functions.  Take a composable screen with multiple buttons and titles.  Using this test as a building block, you can validate that click listeners aren't assigned to the wrong buttons, or that text isn't assigned to the wrong labels.

The primary downside to these tests is that they have to take place within the android Test module.  This means that you can't use tools like Jacoco to objectively measure code test coverage. Aside from this small disadvantage, Jetpack compose tests are well worth the time invested writing them.  Our team has found compose tests in general are significantly less flaky than traditional UI tests.  So much so that we've made it a policy that every new Jetpack Compose function requires its own compose test.  

That wraps it up for this post.  I hope you've found this small example useful for your own Jetpack Compose testing, and encourages you to make the switch away from xml if you haven't already.  I wish I could give a few more examples, but as mentioned in the introduction time has been particularly scarce as of late.  Until next year!

Photo by <a href="https://unsplash.com/@moonlitlens?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Meelika Marzzarella</a> on <a href="https://unsplash.com/photos/brown-and-white-turkey-1dkPi4dmiw0?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
