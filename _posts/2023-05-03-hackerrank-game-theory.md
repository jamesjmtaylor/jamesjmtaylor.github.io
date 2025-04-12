---
title: HackerRank Game Theory
date: '2023-05-03T07:34:00-07:00'
tags: algorithms game-theory
---
<img style="float: left; margin:0 1em 0 0; width: 25%" src="/assets/nash.png"/>

I've used HackerRank throughout my career, both when I've been screened as a candidate, as well as to screen my own candidates.  I've also used it to study algorithms and data structures.  It's a great platform for practicing and evaluating the fundamentals of Computer Science.  In the process of skills evaluation the candidate must check a box stating 

> "I will not consult/copy code from any source including a website, book, or friend/colleague to complete these tests, though may reference language documentation or use an IDE that has code completion features."

Having studied John Nash's work I'm naturally curious as to what the  Game Theory analysis of this simple statement is.  So I decided to apply what I had learned while reading [Game Theory 101: The Complete Textbook](http://gametheory101.com/) (an excellent introduction to the field if you're interested) to the problem of competing candidates.  

Before we get into it, a quick primer.  Game theory is "the study of strategic interdependence."  In other words, the objective analysis of the decisions made by two or more parties and how those decisions impact the parties involved.  One of the ultimate goals of Game Theory is to determine the "Nash Equilibrium" (NE) of any given problem. The NE is the set of strategies that maximizes each player's outcome, regardless of the choices of the other players.  When most people think of Game Theory they think of the Prisoner's Dilemma.  Prisoner's Dilemma is the oldest and simplest game theory problem.  Two prisoners must simultaneously decide whether to confess or keep quiet.  The "Nash Equilibrium" is that both players confess, even though this means an objectively worse outcome than if both players kept quiet. 

The technical interview process is more akin to a Game Theory "Game Tree".  This is because there are multiple sequential stages.  Each path in the tree terminates at a discrete outcome.  For the sake of keeping the tree from getting too large or complex we'll define a number of premises:

1. There are two competing candidates.
2. There is only one job opening, so at most one candidate can get the job.  It is also possible neither candidate gets the job.
3. Both candidates are of equal starting skill.
4. There are two interview stages, a HackerRank digital challenge and an in-person whiteboard challenge
5. The time invested studying for the whiteboard interview only imperfectly translates to skill in the digital challenge.
6. The time invested studying for the digital challenge only imperfectly translates to skill in the whiteboard challenge.
7. You cannot pass the digital challenge without either studying or cheating.
8. You have even odds of being hired as a candidate if both candidates study for the digital challenge since the candidates are otherwise indistinguishable to the interviewers.
9. If you study for the whiteboard and the other candidate does not you will be hired over them.
10. Both candidates are sufficiently skilled that their cheating would not be detected and sufficiently amoral that they would experience no guilt for having cheated.

There are two ways that we can frame the scenario.  The first is as a sequential game. The second player sees the first player choose to study whiteboard problems, and makes their decision on what to study based on that.  The first player knows _a priori_ that the second player will be honest (or dishonest) and acts accordingly.  Thus framed the problem can be represented by the tree below:

<img style="width: 100%" src="/assets/sequential.png"/>

A score of "-10" means that the candidate will not get the job.  A score of "0" means the candidate could get the job.  A score of "10" means that the candidate will definitely get the job, and a score of "5" means that either one of the candidates will definitely get the job.  In a sequential game we can use backwards induction to solve for "Subgame Perfect Equilibrium" (SPE).  A SPE is a complete and contingent plan of action for all players.  All SPEs are NEs, but not all NEs are SPEs.  The solution is depicted below:

<img style="width: 100%" src="/assets/sequentialsolved.png"/>

Both candidates study whiteboard problems and both candidates cheat on the digital portion.

In the second scenario is as a simultaneous game.  Neither of the candidates see the other's preparations or knows their level of honesty.  The tree for this scenario is:

<img style="width: 100%" src="/assets/simeltaneous.png"/>

Because we cannot use backward induction, we have to compare the grids themselves.  In this case it is always more beneficial for a player cheat rather than to not to cheat, and to study whiteboard problems than digital problems.

This conclusion comes with a LOT of caveats.  Most candidates are not amoral, and securing a job offer contingent on cheating would have a negative impact on the candidate's final "objective" outcome value.    Most candidates are not exactly equal in all other domains.  Often times strong soft skills can make up for missing technical skills.  Interviewing also is not necessarily a zero-sum game.  I personally interviewed for a position that had already been filled, but the employers liked me so much that they created a second position just so that they could hire me.  

Despite those caveats, I do not think it wise to use a system that favors dishonest candidates over honest ones, and will not be using it in my own future screening of candidates.  

https://workplace.stackexchange.com/questions/150857/not-allowed-to-use-google-during-programming-test

_Image Credit: MIT Museum_

_Graphics credit _[_http://www.gametheoryexplorer.org/_](http://www.gametheoryexplorer.org/)
