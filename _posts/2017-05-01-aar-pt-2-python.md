---
title: AAR pt 2 (Python)
date: 2017-05-01T11:37:09.000Z
tags: aar python
---
![Python programming language](/assets/python.jpg)

 If you haven't had a chance to read the first entry in the series  for context, [you can do so here](/after-action-review-aar/).  In this post I'll be covering my most important lessons learned from everyone's favorite language, Python.  I'll also have some React.js and React-Redux at the end, since the majority of these lessons learned were made while I was on-boarding with a full-stack web developer.

* You can run python2 and python3 side-by-side, just need to differentiate calls by appending a '3' to calls to the python3 tools (i.e. "python3" vs. "python" or "pip3" vs. "pip"
* You can install required dependancies in a project after a git clone by running 'pip install -r YOURREQDOC.txt"
* In Python2 use `print "myString"`, in Python3 use `print("myString")`
* If you ever receive an "unindent does not match" error, it's probably because there are tabs mixed in with spaces.
* "console.log" output only visible in the Google Chrome inspector console (NOT the terminal running the application)
* In React-Redux the immutable 'store' singleton maintains the application state.
* The React-Redux store can have new versions created with methods known as 'reducers'
* In the React-Redux architecture files are organized by layer (rather than by feature) into the following directories: **Containers**-Single JSX functions that are paired with "MapStateToProps" and "mapDispatchToProps" and "Connect" to provide access to the redux store for components; **Components**-JSX functions that represent pieces of the UI; **Actions**-JS Functions that enable duplication of the store without explicitly calling a state constructor.
* All React-Redux components are automatically subscribers to changes in the store if you use 'containers'
* Rich links involve searching a webpage for meta-tags and then rendering their parsed results on your page.
* React.js has it's own function to convert a string to an HTML tag, so don't try and use the standard javascript tag creator or try to roll your own.
