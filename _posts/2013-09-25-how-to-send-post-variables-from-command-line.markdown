---
layout: post
title:  "How to send POST variables using command line"
date:   2013-09-17 19:41:00 -0300
categories: command-line terminal
---
When you are developing using PHP or another technology, you can use the POST variables to send values from the *client* to the *server* page.

Now, for debug and application, you can use the UNIX command line to send POST variables. It is much more easy, because you won't need a complete HTML form or AJAX script or other complex way.

To send the variables we are going to use the CURL command line utility.
After install this application use the next commands to send the POST variables:

Send a simple variable:

{% highlight bash %}
curl -d 'name=value' http://localhost/index.php
{% endhighlight %}

Send multiple variables:

{% highlight bash %}
curl -d 'first=value&second=value' http://localhost/index.php
{% endhighlight %}

This tool give you another interesting options, too see them all run:

{% highlight bash %}
curl --help
{% endhighlight %}
