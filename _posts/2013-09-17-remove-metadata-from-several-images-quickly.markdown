---
layout: post
title:  "Remove Metadata From Several Images Quickly"
date:   2013-09-17 20:26:00 -0300
categories: metadata
---
## Goal

I will guide to you in a simple and clever way to remove EXIF information ([metadata][metadata-wikipedia]) from your images, using just command line's utilities.

## Prerequisites

Install `exiftool` command utility.

For Ubuntu:

{% highlight bash %}
  sudo apt-get install libimage-exiftool-perl
{% endhighlight %}

For other systems check [the exiftool website](https://exiftool.org).

## Prepare files

Optionally you can create a new directory with a copy of the files in case you want to conserve the original ones with the EXIF data on them.

 * Create a temporary directory where you can paste all the images to remove their metadata: `mkdir ~/Pictures/tmp`

 * Copy the images over this directory: `cp ~/Pictures/holidays ~/Pictures/tmp`

## Prepare script

 * Now create a file that will contain the script: `touch remove_metadata.sh`
 
 * Past the following the following code into the file:

{% highlight bash %}
  #!/bin/bash
  cd ~/Pictures/tmp
  exiftool -all= *
  echo "y" | exiftool -delete_original *
{% endhighlight %}

 * Make sure to replace `~/Pictures/tmp` with the desired path to the images to remove metadata. Note: it will replace them!

 * Save script and mark it as runnable by giving it executable permissions: `chmod +x remove_metadata.sh`

## Run script

The script can be ran from the command line or doing [double-click over an icon][how-to-click-icon] in you GNU/Linux system.

You won't have to worry anymore about any GEO localization, edits, dates, etc.

[metadata-wikipedia]: https://en.wikipedia.org/wiki/Metadata
[how-to-click-icon]: http://askubuntu.com/q/138908/62483
