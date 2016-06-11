---
layout: post
title:  "Remove METADATA from several pictures quickly"
date:   2013-09-17 20:26:00 -0300
categories: metadata
---
I will guide to you in a simple and clever way to remove EXIF information ([metadata][metadata-wikipedia]) from your pictures, using just command line's utilities.

 * First you will need a terminal and the `exiftool` command utility. To install this application enter the next command in a terminal:

{% highlight bash %}
  sudo apt-get install libimage-exiftool-perl
{% endhighlight %}

 * Create a folder where you can paste all your pictures.

 * Now create a file that will contain the following bash script:

{% highlight bash %}
  #!/bin/bash
  cd ~/Pictures/tmp
  exiftool -all= *
  echo "y" | exiftool -delete_original *
{% endhighlight %}

 * Save this file with any name, I will use ScriptFileName.sh (extremely innovator)

 * In order to make this script runnable, give it executable permissions with `chmod +x ScriptFileName.sh`

Now you can run this script from the command line or doing [double-click over an icon][how-to-click-icon] in you GNU/Linux system.

If you need to remove metadata from several files, put those files into your folder and run the script. That's all.
You won't have to worry anymore about any GEO localization, edits, dates and more.

[metadata-wikipedia]: https://en.wikipedia.org/wiki/Metadata
[how-to-click-icon]: http://askubuntu.com/q/138908/62483
