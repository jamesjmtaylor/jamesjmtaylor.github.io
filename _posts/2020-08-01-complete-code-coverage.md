---
title: Complete Code Coverage
date: '2020-08-01T11:53:00-07:00'
tags: software-engineering
---
![Tiger tank](/assets/tiger.jpg)

I have a bit of a confession to make: I'm something of a [Quora](https://www.quora.com/) addict.  I love scrolling through the incredible variety of questions and answers.  A little while ago I came across an excellent question about German engineering as it applied to software.  Specifically  [If Germans are good at engineering, why don't they dominate computer operating systems or mobile operating systems?](https://www.quora.com/If-Germans-are-good-at-engineering-how-come-they-dont-dominate-computer-operating-systems-or-mobile-operating-systems)   The most upvoted answer, written by an American software engineer who works at Daimler, was fascinating: 

> "I work in the IT department of a German-owned company. I use a lot of German software, both commercial and developed in-house. **There is one thing that you can say about German software: it is absolutely rock-solid, bullet-proof-reliable. It is amazing.**
>
> So, why aren’t they on top? I’ll let you in on the secret.
>
> An IT colleague spent several years in Germany. Americans were sometimes stereotyped as “cowboys.” He wasn’t sure the precise meaning of the metaphor (after all, cowboys are heroes in American culture). They explained, “Oh, you Americans are like cowboys that walk into a room and just start shooting everywhere.”
>
> He replied, “Well, if we’re like **cowboys**, you guys are like **snipers**. You meticulously plan your mission, and then stalk for days to get to the target, only to find the target has moved on.”
>
> And that reflects the reality. German software development cycles are careful, deliberate, well-planned and well-tested; this approach does not work well with a “moving target.” That approach takes time, so you can’t have rapid product evolution. It’s also expensive, because you aren’t using your user community as your beta-testers, just throwing out whatever sloppy code you banged out the night before. This extensive in-house testing means the products are also expensive.
>
> For the Germans, functionality is valued over usability. You won’t find a whole lot of time/thought put into the user interface; users are just expected to comply with the perfectly-functional design (even though the menus suck). The people who are buying the software are not the people who will be using it.
>
> This German approach does not lend itself to the market demands of consumer-oriented software (and certainly not to mobile devices). Consequently, consumer-oriented software does not share Germany’s reputation for stability and reliability.
>
> German software development lends itself better to software products used in, say, industry, energy, healthcare, and infrastructure. Such as Siemens, a German company with worldwide usage in large stable corporate environments that do not undergo rapid change. You might even have seen a Siemens logo on various pieces of equipment the last time you visited a hospital. But you won’t find Siemens software running on your typical consumer laptop. (FWIW, my German colleagues use Siemens-branded laptops, which run Windows.) I’m not allowed to tell you if we run SUSE Linux (a German Linux operating system) on any servers, but it’s what I use at home.
>
> The nature of German software development will relegate most of it to niche markets which value stability and reliability more highly than most consumers do. That’s not a bad thing; German software fulfills a legitimate and important need, and those companies make a nice profit."

Which got me thinking about industry best practices in general.  The picture above is of the infamous German Tiger tank.  They were the pinnacle of tank technology in WW2, and approximately 1,300 were produced before the end of the war.  In contrast the American main battle tank, the M4 Sherman, had over 49,000 produced.  The American tank was widely considered to be a death trap; an iron coffin.  American tankers with gallows humor called it the "Zippo" because it "lights up every time."  Despite its shortcomings compared to the Tiger tank, The M4 Sherman ultimately ended up winning the war (alongside the T-34, Russia's equivalent, of which 80,000 were produced).  The reason that so few Tiger tanks were produced was because of their mechanical complexity and precision engineering.  Many parts had to be individually machined to incredibly narrow tolerances.  The Russian and American tanks in contrast often used a combination of cast iron hulls and welded plates, dramatically reducing production times.  They weren't completely absent of machined parts, but their designs made due with a great deal less of them.

All this is exposition is just a long-winded pre-amble to the main topic of today's blog, code coverage.  For the uninitiated, code coverage is a percentage figure of how much of your code base is executed when you run your unit tests.  A lot of development organizations see 100% as the gold standard for unit testing.  The problem with this approach is three-fold.

The first issue is that 100% code coverage generally makes you do some pretty silly things to get every last scrap of code under coverage, i.e. "Does this onCreate() method get executed?"  The reason this is a silly test is because the framework automatically executes onCreate() when you instantiate the activity, regardless of what the rest of your code does.  The test will always pass.  If it were to fail, it would mean that something was broken with the framework, and that not only is your program broken, but that tens of thousands of other programs that rely on the framework are broken as well.

The second issue is that it takes time to write unit tests.  Anyone who tells you otherwise is a bold-faced liar.  They may save you time in aggregate when you factor in the regression bugs that you prevent, or the ability to iterate quickly with red-green Test-Driven Development (TDD).  But in the short run they do take time to implement and execute.  If you don't invest the limited amount of time that you have in order to maximize your cost-to-benefit ratio, you're wasting time.  Especially given the implications of the first issue.

The third issue is that unit tests in a perverse way can lock-in bugs and make them slower to fix.  An example would be a user interaction flow.  You implement the flow per the guidance from your User Experience team, writing unit tests as you go.  Then, after releasing the code to production you find that it's fundamentally flawed in some way.  Maybe user's don't find it as intuitive as User Research indicated they would.  Maybe you find that you need additional inputs from the user to enable some other feature.  But standing in the way of fixing these issues are unit tests.  Before you can complete your changes you need to either replace or remove the previous iteration's unit tests.  

Now, this isn't to say that you should never use unit tests.  In the past I've found unit tests immeasurably helpful, especially for the TDD of more math-heavy functionality.  But, everything in moderation.
