---
layout: post
title:  Enable Cookie Consent to Online Visitors
date:   2020-08-26 19:51:00 +0200
categories: cookie-consent
---
# Introduction
Explain the whole GDPR.

# Approaches
At the moment of showing a cookie consent there are a couple of different ways to communicate with the users.
The most common ones are the following two.

## Warn User
It notifies the visitor that the website is using cookies.
There's a link to either the cookie policy or to learn more about what cookies are and how they are used.

Interaction: "OK"

## Allow to opt-in
Disable cookies by default and allow visitors to enable them.

Interaction: "OK" / "Decline"

All the approaches are clear now, and we can continue with their development.

# Development
Start with the basic warning and move into an advanced opt-in approach.
The simplest one is warn user so let's start with it.

## Existing Tools
There are a couple of open source tools to show the cookie banner:
 - [Cookie Consent](https://www.npmjs.com/package/cookieconsent)
 - [GDPR Cookie Notice](https://www.npmjs.com/package/gdpr-cookie-notice)  
 - [Cookies EU banner](https://www.npmjs.com/package/cookies-eu-banner)  

The one with more weekly downloads over NPM is Cookie Consent. Hence, it was my first choice to use. 

## Cookie Consent
[Cookie Consent](https://www.osano.com/cookieconsent) is a free and open source tool developed by [Osano](https://www.osano.com).
The company offers more solutions targeting data privacy law for big tech companies so you are definitely on good hands.
The JavaScript plugin is fairly easy to integrate so let's get into it.

NOTE: latest version of plugin is v4 but seems like it has not been [released](https://twitter.com/delucioux/status/1298666264794222592/retweets/with_comments) yet.
Hence, following implementation is using v3.

CSS dependency:
Put it on the `<head>`.
```
<link rel="stylesheet" type="text/css" href="https://cdn.jsdelivr.net/npm/cookieconsent@3/build/cookieconsent.min.css" />
```

JS dependency:
Place the script at the end of the `<head>` or at the end of the `<body>` and make sure is before the configuration block. 
```
<script src="https://cdn.jsdelivr.net/npm/cookieconsent@3/build/cookieconsent.min.js" data-cfasync="false"></script>
```

Configuration:

The look and feeling can be customised to match your website style.
Go to the [download](https://www.osano.com/cookieconsent/download/) to play with all the options and get the one that fits the best for you.
Here's an example of a chosen configuration:

```
window.cookieconsent.initialise({
  "palette": {
    "popup": {
      "background": "#000"
    },
    "button": {
      "background": "#f1d600"
    }
  }
});
```

Checkout the [documentation](https://www.osano.com/cookieconsent/documentation/about-cookie-consent/) for reading all the options available.

At the end of the implementation the cookie consent should look similar to the next implementation.

![Cookie Consent implemented](https://pbs.twimg.com/media/EgWRQ_7X0AA3GWw?format=jpg&name=medium)

Now you can see the cookie consent is present letting visitors know that cookies are being used.
However, if visitors want to disable them then there's no option present to do so.
A better approach would be to let visitors decide whether enable or not cookies on the website.

There's a saying that goes like this:

    Good is not good enough when better is expected

A better approach is disabling cookies by default and letting visitors enable them, so let's develop now.

Since it involves several changes, you may want to prefer looking at the [implementation done](https://github.com/luciomartinez/luciomartinez.github.io/pull/3)
at my blog and use it as a reference.

The previous implementation will help as a starting point.
There are two more places which requires updates. First at the point of using cookies.

## Disable Cookies
Cookies are going to be disable by default. Is very much likely the only place with cookies usage is on GA so let's use it as a reference.
Following is the injection of GA update to be disable by default.

```
   <script async src="https://www.googletagmanager.com/gtag/js?id=UA-XXXXXX-Y"></script>
-- <script>
-- window.dataLayer = window.dataLayer || [];
--  function gtag(){dataLayer.push(arguments);}
--  gtag('js', new Date());
--  gtag('config', 'UA-XXXXXX-Y');
-- </script>
``` 

As you can see above, the library remains in place, and removes the initialisation code (actually moved, we'll see where to put it next).

## Integrate GA with Cookie Consent
Now the cookie consent will handle the case where visitor allows cookies.
Here's where the above GA code needs to be moved.

In order to make code still readable, I'm not copying and pasting all over the place
but rather creating a class which takes care of managing the initialisation of GA and further enabling/disabling cookies.
```
  function GAManager() {
    let isInitialized = false;

    this.enable = function() {
      initiGAIfNeeded();
      window['ga-disable-GA_MEASUREMENT_ID'] = false;
    };

    this.disable = function() {
      window['ga-disable-GA_MEASUREMENT_ID'] = true;
    };

    function initiGAIfNeeded() {
      if (!isInitialized) {
        initGA();
      }
    }

    function initGA() {
      window.dataLayer = window.dataLayer || [];
      function gtag(){dataLayer.push(arguments);}
      gtag('js', new Date());

      gtag('config', 'UA-126456490-1');

      isInitialized = true;
    }
  }
```

`GAManager` is a class with the following two methods:
 - `enable`
 - `disable`

When `enable` is called for the first time it will initialise GA.
In further calls it will just enable the cookie over the already initialised GA.

When `disable` is called, it will make sure that if GA is initialised then the cookies are, even then, disabled.

Now let's integrate this GA manager with the cookie consent plugin.

```
  const gaManager = new GAManager();

  window.cookieconsent.initialise({
    type: 'opt-in',
    palette: {
      popup: {
        background: '#edeff5',
        text: '#838391'
      },
      button: {
        background: '#4b81e8'
      }
    },
    onInitialise: function () {
      const type = this.options.type;
      const didConsent = this.hasConsented();
      if (type === 'opt-in' && didConsent) {
        gaManager.enable();
      } else if (type === 'opt-out' && !didConsent) {
        gaManager.disable();
      }
    },
    onStatusChange: function() {
      const type = this.options.type;
      const didConsent = this.hasConsented();
      if (type === 'opt-in' && didConsent) {
        gaManager.enable();
      } else if (type === 'opt-out' && !didConsent) {
        gaManager.disable();
      }
    },
    onRevokeChoice: function() {
      const type = this.options.type;
      if (type === 'opt-in') {
        gaManager.disable();
      } else if (type === 'opt-out') {
        gaManager.enable();
      }
    }
  });
```

At the top, there's a new object created from the `GAManager` class.
Then, it defines three new properties in the plugin's options:
 - `onInitialise` 
 - `onStatusChange` 
 - `onRevokeChoice`

All of this are callbacks which react to user interactions. eg. user clicks on "Allow cookies"
Each callback is then relying on the GA manager to make sure according to the action's intention it enables/disables cookies. 

After a longer implementation, the cookie consent should now look similar to the next one.

![cookie consent implemented](https://pbs.twimg.com/media/Ega4GsdXsAEKWrz?format=jpg&name=medium)

# Conclusions
Talk about EU and maybe statistics.
 - Open source is awesome! Is quicker to make use of existing tools and avoid [reinventing the wheel](https://en.wikipedia.org/wiki/Reinventing_the_wheel).
 - Comply with GDPR turns out to be fairly easy
 - Advanced options can be grant spending a bit more of timing but still not that bad

> Disclaimer: do not take legal advices from this technical article.

# Feedback
Let me know your thoughts through my [Twitter account](https://twitter.com/delucioux). I'm looking forward to hearing from you!

Sources:
 - https://github.com/osano/cookieconsent
 - https://developers.google.com/analytics/devguides/collection/gtagjs
 - https://developers.google.com/analytics/devguides/collection/gtagjs/user-opt-out

To follow up:
 - https://stackoverflow.com/questions/10668292/is-there-a-setting-on-google-analytics-to-suppress-use-of-cookies-for-users-who
