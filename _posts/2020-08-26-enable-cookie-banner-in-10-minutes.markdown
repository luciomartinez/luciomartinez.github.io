---
layout: post
title:  Enable Cookie Banner In 10 Minutes
date:   2020-09-18 19:51:00 +0200
categories: cookies
---
# Introduction
Cookies, not the sweet ones, but the digital ones are present in almost every website.
As a website owner, small or huge, you will need to check out how many people have visited the website lately.
The most popular solution for this is Google Analytics (GA), and it requires the usage of cookies.  

> The protection of natural persons in relation to the processing of personal data is a fundamental right

States the European General Data Protection Regulation (GDPR), and it includes cookies.
Users must, first, be informed of active cookies and their purpose.
Secondly, must be able to disable them if desired.

A banner act as an emerging window that notifies user.
It can also be extended to request for permission.
On this article, I'll explain how to implement a banner which notifies about cookies usage.

In case you're interested in how to request permission too, you can find a guide on its implementation on the [How To Opt In Google Analytics](/cookies/2020/09/18/how-to-implement-google-analytics-with-opt-in) article.

# Implementation
The development of a banner may take some hours or even days.
By taking advantage of open source, we can speed up this process and choose an existing tool that takes as little as 10 minutes.
Let's take a closer look at what tools are available and which one is the best to quickly use.

## Existing Tools
There are a couple of open source tools to show the cookie banner:
 - [Cookie Consent](https://www.npmjs.com/package/cookieconsent)
 - [GDPR Cookie Notice](https://www.npmjs.com/package/gdpr-cookie-notice)  
 - [Cookies EU banner](https://www.npmjs.com/package/cookies-eu-banner)  

The one with more weekly downloads over NPM is Cookie Consent. Hence, it was my first choice to use. 

## Integrating Cookie Consent
[Cookie Consent](https://www.osano.com/cookieconsent) is a free and open source tool developed by [Osano](https://www.osano.com).
The company offers more solutions targeting data privacy law for big tech companies, so you are definitely on good hands.
The JavaScript plugin is fairly easy to integrate so let's get into it.

> Note: latest version of plugin is v4 but seems like it has not been [released](https://twitter.com/delucioux/status/1298666264794222592/retweets/with_comments) yet.
Hence, following implementation is using v3.

__CSS dependency__  
```
<link rel="stylesheet" type="text/css" href="https://cdn.jsdelivr.net/npm/cookieconsent@3/build/cookieconsent.min.css" />
```
Place the tag on the `<head>`.

__JS dependency__  
```
<script src="https://cdn.jsdelivr.net/npm/cookieconsent@3/build/cookieconsent.min.js" data-cfasync="false"></script>
```
Place the script at the end of the `<head>` or at the end of the `<body>` and make sure is before the configuration block. 

__Configuration__  
The look and feeling can be customised to match your website style.
Go to the [download](https://www.osano.com/cookieconsent/download/) page to play with all the options and get the one fits best for you.
Here's a configuration example:

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

At the end of the implementation, the cookie banner should look similar to the next one.

![Cookie banner saying "This website uses cookies to ensure you get the best experience on our website." with a link that reads "Learn more" and a button saying "Got it!"](https://pbs.twimg.com/media/EgWRQ_7X0AA3GWw?format=jpg&name=medium)

Now you can see cookie banner is present letting visitors know that the website makes use of cookies.
However, should visitors want to disable them, they have no way to do so.
A better approach would be to let visitors decide whether enable or not cookies on the website.
I invite you to read the [How To Opt In Google Analytics](/cookies/2020/09/18/how-to-implement-google-analytics-with-opt-in) article where you can find this approach's implementation in detail.

# Conclusions
GDPR has been implemented since 2018.
On its first year, there was a report showing its most important [numbers for 2019](https://ec.europa.eu/info/sites/info/files/infographic-gdpr_in_numbers.pdf).
Almost all EU countries adapted their national legislation.
As the time pass, it becomes more and more important to comply with it.
Now we can see that is fairly easy to do so.

> Disclaimer: do not take legal advices from this technical article.

# Feedback
Let me know your thoughts through my [Twitter account](https://twitter.com/delucioux).
I'm looking forward to hearing from you!
