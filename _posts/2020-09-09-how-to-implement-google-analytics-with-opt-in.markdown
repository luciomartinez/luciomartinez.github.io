---
layout: post
title:  How To Implement Google Analytics With Opt In
date:   2020-09-18 21:34:00 +0200
categories: cookies
---
# Introduction
It's very easy to [enable cookie banner](/cookies/2020/09/18/enable-cookie-banner-in-10-minutes) to warn our website visitors about cookies usage.
What if we want to allow them to optionally opt in?
Disabling cookies by default is becoming more and more standard.
Here you are going to find how to implement opt in for Google Analytics.

# Implementation
Before digging deep, I'd like to mention that if you feel lost at any point of the article,
you can look at the [implementation](https://github.com/luciomartinez/luciomartinez.github.io/pull/5) done at my blog and use it as a reference.

The first thing to look at is the point of code that uses cookies and disable it.

## Disable Cookies
Cookies are going to be disable by default.
Following is the injection of GA updated according.

```
   <script async src="https://www.googletagmanager.com/gtag/js?id=UA-XXXXXX-Y"></script>
-- <script>
-- window.dataLayer = window.dataLayer || [];
-- function gtag(){dataLayer.push(arguments);}
-- gtag('js', new Date());
-- gtag('config', 'UA-XXXXXX-Y');
-- </script>
``` 

As you can see above, the script fetching the library remains in place.
Removed is the initialisation code. Actually, it's moved, we'll see where to put it next.

## Integrate Analytics With Cookie Banner
Since GA doesn't offer opt in builtin support, [GTag Opt In](https://www.npmjs.com/package/gtag-opt-in) will help to achieve this.

`GTagOptIn` registers a single method called `register` which registers the Analytics ID and returns an object.
The returned object contains two methods, `optin` and `optout` respectively.
When `optin` is called for the first time it will initialise GA.
In further calls it will just enable the cookie over the already initialised GA.
When `optout` is called, it will make sure if GA is initialised then the cookies are, even then, disabled.

Now let's integrate this tool with the Cookie Consent plugin.

```
<script src="https://www.npmcdn.com/gtag-opt-in@1.2.0/dist/index.js"></script>
<script>
  const gtag = GTagOptIn.register('UA-126456490-1');
  window.cookieconsent.initialise({
    "palette": {
      "popup": {
        "background": "#000"
      },
      "button": {
        "background": "#f1d600"
      }
    },
    type: 'opt-in',
    onInitialise: function () {
      const type = this.options.type;
      const didConsent = this.hasConsented();
      if (type === 'opt-in' && didConsent) {
        gtag.optin();
      } else if (type === 'opt-out' && !didConsent) {
        gtag.optout();
      }
    },
    onStatusChange: function() {
      const type = this.options.type;
      const didConsent = this.hasConsented();
      if (type === 'opt-in' && didConsent) {
        gtag.optin();
      } else if (type === 'opt-out' && !didConsent) {
        gtag.optout();
      }
    },
    onRevokeChoice: function() {
      const type = this.options.type;
      if (type === 'opt-in') {
        gtag.optout();
      } else if (type === 'opt-out') {
        gtag.optin();
      }
    }
  });
</script>
```

At the top, is loaded the library from NPM CDN.
Then, a new `gtag` object with the `GTagOptIn.register` method.
There's the plugin's `type` property set to `opt-in` which changes from enabled-by-default to disabled-by-default.
Last but not least, three new properties added into plugin:
 - `onInitialise` 
 - `onStatusChange` 
 - `onRevokeChoice`

These are callbacks that reacts to user interactions (eg. user clicks on "Allow Cookies").
Each callback is then relying on GTag Opt In to act according, enabling/disabling analytics and its cookies. 

The cookie banner should now look similar to the next one.

![Cookie banner saying "This website uses cookies to ensure you get the best experience on our website." with a link that reads "Learn more" and two buttons where the first one says "Decline" and the second one says "Allow cookies"](https://pbs.twimg.com/media/Ega4GsdXsAEKWrz?format=jpg&name=medium)

In case you encounter issues, you can take a look at the [implementation](https://github.com/luciomartinez/luciomartinez.github.io/pull/5) done at my blog and use it as a reference.

# Conclusions
 - Open source is awesome! Is quicker to make use of existing tools and avoid [reinventing the wheel](https://en.wikipedia.org/wiki/Reinventing_the_wheel).
 - Comply with GDPR turns out to be fairly easy
 - Advanced options can be grant spending a bit more of timing but still not that bad

> Disclaimer: do not take legal advices from this technical article.

# Feedback
Let me know your thoughts through my [Twitter account](https://twitter.com/delucioux).
I'm looking forward to hearing from you!
