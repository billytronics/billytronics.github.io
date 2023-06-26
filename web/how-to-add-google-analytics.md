---
layout: default
title: How to add Google Analytics
---

# How to add Google Analytics to your GitHub Pages

Hi! This is the minimal way to add Google Analytics to your standard GitHub Pages, which is backed by Jekyll by default

## Create a Google Analytics Account

1. Sign up at the [Google Analytics Website](https://analytics.google.com/)
2. Set up a new property and enter your website's URL (eg. billytronics.github.io for this website)
3. Get your tracking code snippet

## Add Tracking Code to your Website

1. Create the analytics.html file with the tracking code snippet under an _includes folder
2. Reference the above in your layout files by adding the following at the end of the <head> block:
``` javascript
{% include analytics.html %}
```

## Wrap up

1. If you are not sure, take a look at this repo as an example :)
2. Give GitHub a minute to load the changes after your updates. Happy tracking!
