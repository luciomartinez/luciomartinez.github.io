---
layout: post
title:  JavaScript Test Environment”
date:   2020-05-23 16:48:00 +0200
categories: js
---
## Testing

Testing can be done ether manually or automatically. there are many type of tests inside the automation side from which [Unit Testing](https://en.wikipedia.org/wiki/Unit_testing) will be covered on this post. There's a nice post from Atlassian on [Software Testing](https://www.atlassian.com/continuous-delivery/software-testing/types-of-software-testing) if you need a broader introduction.

Any JavaScript project that requires Unit Tests will involve a couple of tools. Understanding each tool can be greatly beneficial for a better use of them.

### Is it gonna help me?

<iframe src="https://giphy.com/embed/iD6iDZFXQQagsmgmU1" width="480" height="270" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/nbcthevoice-nbc-voice-thevoice-iD6iDZFXQQagsmgmU1">via GIPHY</a></p>

If you are in one of the following sceenarios this post may be helpful for you:

 * JS developer recently introduced into a new project with a test suite on place
 * JS developer trying to build a new app from scratch
 * Not a JS developer starting to code on the JS world and trying to bring tests into an existing project
 * Frontend or backend developer

## Phases

In order to understand the tools first we need to understand the phases of the Unit testing.

### Framework

<iframe src="https://giphy.com/embed/bAH0F5oK1QUow" width="480" height="202" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/polyvinylrecords-bAH0F5oK1QUow">via GIPHY</a></p>

The 1st phase is the framework in which tests will be executed. No, I'm not talking about Angular or another framework but the test framework. Wikipedia contains a full [list of frameworks for JavaScript](https://en.wikipedia.org/wiki/List_of_unit_testing_frameworks#JavaScript).

The 5 more populare ones are:

Name | Website | Client | Server
--- | --- | --- | ---
Jest | https://jestjs.io | ✅ | ✅
Mocha | https://mochajs.org | ✅ | ✅
Jasmine | https://jasmine.github.io | ✅ | ✅
tape | https://github.com/substack/tape | ✅ | ✅
AVA | https://avajs.dev |  | ✅

Ordered by popularity (looking at npm downloads during last week at the time of writing).
