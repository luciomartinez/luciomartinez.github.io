---
layout: post
title:  3 Ways To Integrate a Node.JS App With Firebase
date:   2020-08-25 20:48:00 +0200
categories: cloud
---
# Deliver a Node.JS App With Firebase
Since 2017 is possible to integrate [Node.JS](https://nodejs.org) with [Firebase](https://firebase.google.com).<sup>[1](#reference1)</sup>
There's an official [demonstration](https://youtu.be/LOeioOKUKI8) that develops two Node.JS endpoints.
There's a [tutorial](https://indepth.dev/building-an-api-with-firebase/) building an API from scratch.
Now, what happens when an already developed Node.JS app needs to be integrated with Firebase?
There will be an issue, and the previous articles won't help.
Getting from two endpoints into a full Node.JS app will become more complex
requiring a few hours of understanding, debugging and more trying to get it working.

Hopefully, this article will save other people time on that process since you may find the description of the main issue,
the analysis and the development of a Node.JS app with Firebase on three different ways for a successful deployment. 

## Things to Consider
Before talking about the main issue let's define two things:
 - Node.JS app structure
 - Cloud Functions integration
 
First, the Node.JS app structure.

### Node.JS App Structure
The structure of a regular Node.JS app looks something like this:

```
 > bin/
   > www.js
 > src/
   > controllers
   > routes
   > more modules
 > test/
   > src
 > app.js
 > package.json
``` 

The script `bin/www.js` will serve the application configuring the HTTP server.
The application main entry is `app.js` which configures the app (eg. routes, middlewares, etc.) and requires the app's source from the `src` directory.
The `test/` directory contains `*.spec.js` files testing each script at the `src/` directory.

Now it's time to talk about the Cloud Functions integration.

### Cloud Functions Integration
The installation of Cloud Functions via the [Firebase CLI](https://firebase.google.com/docs/cli)
creates a `functions/` directory by default with `functions/index.js` as the main entry.

The most natural connection will be to require the app in the main entry of the function module.

```
// functions/index.js
const functions = require('firebase-functions');
const app = require('../app');
exports.app = functions.https.onRequest(app);
```

The smoke testing comes at deployment time by executing the `firebase deploy` command.
If that'd work then this post won't exist and so now is time to talk about the issue.

## Issue
The Cloud Functions deployment fails with the following message:

    Cannot find module '../app'

Cloud Functions uploads the content of the `functions/` directory on deployment and so there was no Node.JS app inside it.
That's where our journey begins since there are three ways to solve it.

## Solutions
There are three ways to solve it.
Each of them is defined with its "pros-and-cons" making the election easier for different scenarios.

NOTE: all solutions has a common requirement and is that the Firebase project must be configured with [Pay as you go](https://firebase.google.com/pricing).
It does not mean that there's payment requirements since the free quota is large enough for testing.
However, it requires a billing account to be in place.
Read more at the [Quotas and limits](https://firebase.google.com/docs/functions/quotas) page.

### Solution A: App Inside Functions
With this solution the project's structure will change to the next shape:
```
 > bin/
   > www.js
 > functions/
   > src/
   > test/
   > app.js
   > index.js
   > package.json
 > package.json
``` 

#### Steps

 1. Move `app.js`, `src/` and `test/` into `functions/`
 2. Update the app's path in `functions/index.js`

         -- const app = require('../app');
         ++ const app = require('./app');

 3. Update the app's path in `bin/www.js`

         -- var app = require('../app');
         ++ var app = require('../functions/app');

 4. Tests' path must be updated in `package.json`
 
        "test": "nyc --reporter=html mocha --recursive 'functions/test/**/*.js'"
 
 5. All `dependencies` (do not confuse with `devDependencies`) from `package.json` must be installed at `functions/package.json`.
 For example: if `@google-cloud/firestore` is declared as an app's dependency then it must be installed as follows.
 
        cd functions
        npm i @google-cloud/firestore

#### Pros
 - It works!

#### Cons
 - Code coupled with hosting solution.
 In case we want to switch from Firebase into another solution will be a nightmare.
 - Loss of classic Node.JS app structure.
 Looking at the project's root level there'll be no indicator of a Node.JS app.
 Instead, an app that only supports Cloud Functions will be highlighted.
 - Double installation of dependencies.
 In order to make app work locally they must be installed at `package.json`.
 To make it also work for Cloud Functions they must be installed at `functions/package.json`. 

### Solution B: Functions at Root
With this solution the project's structure will change to the next shape:
```
 > bin/
   > www.js
 > src/
 > test/
 > app.js
 > package.json
``` 

#### Steps

 1. Move `functions/index.js` to the root level
 2. Update the app's path in `index.js`

         -- const app = require('../app');
         ++ const app = require('./app');

 3. Carefully integrate `functions/package.json` with `package.json`
 It consists of, mainly, the next sub-steps:
    1. Merging `scripts`
    2. Merging `engines`
    3. Merging `dependencies`
    4. Merging `devDependencies`  
 Real example: ![git diff](https://i.imgur.com/ftT84Gh.png)
 4. Remove `functions/`
 5. Update Cloud Functions path's configuration from the `firebase.json` file.

        "functions": {
          "source": "."
        }

#### Pros
 - It also works!
 - No duplication of NPM packages.

#### Cons
 - Is tedious as heck.
 - Ends up with two entry points at the root level (`app.js` and `index.js`).
 - A single node version for both, local and Cloud Functions (`package.json@engines`).

### Solution B: Mirror App Inside Functions
With this solution the project's structure will change to the next shape:
```
 > bin/
   > www.js
 > functions
   > index.js
   > package.json
 > src/
 > test/
 > app.js
 > package.json
``` 

#### Steps

 1. Update the app's path in `functions/index.js`

         -- const app = require('../app');
         ++ const app = require('./app');
 2. All `dependencies` (do not confuse with `devDependencies`) listed at `package.json` must be installed at `functions/`.
  For example: if `@google-cloud/firestore` is declared as a dependency at the root app then it must be installed as follows.
  
         cd functions
         npm i @google-cloud/firestore

 3. Copy the app before deployment starts into the Functions module.
 This can be done via the `firebase.json` file by updating it with the following block.
 
         "functions": {
           "predeploy": [
             "rm -r functions/app; mkdir functions/app && cp -r {app.js,src} functions/app"
           ]
         }
  
 4. Add `functions/app` to `.gitignore` 

#### Pros
 - It works too!
 - Original app structure is almost untouched. The Node.JS app continue to look exactly as expected.
 - Same code tested locally will be deployed automatically on the Cloud Functions.
 - Migrating from Firebase into another solution will be as simply as removing Cloud Functions related files and enable the new solution

#### Cons
 - Needs to keep track of dependencies and make sure to install them twice.
 
These were a description of the three ways that I've found to integrate a Node.JS app into Firebase.
Which one is the right one depends on the project and its requirements.
In my experience, solution C has been the best option.
Anyways, it might be that any of the other solutions could fit better for a different project.
The choice is up to you.

## Conclusion
Is worth to give it a shot and deploy a Node.JS app via Firebase.
The platform counts with a list of exciting solutions to develop mobile and web apps.
It includes [extensions](https://firebase.google.com/products/extensions) that accelerate the development of common solutions
and [integrations](https://firebase.google.com/integrations) to quickly connect with existing platforms. 
Integrating your application into this platform can be very handy for professional as well personal projects.

## Feedback
Let me know your thoughts through my [Twitter account](https://twitter.com/delucioux). I'm looking forward to hearing from you!

## References
 <b id="reference1">[1]</b> [How to host nodeJS project to firebase?](https://stackoverflow.com/a/45538122/1505348)
