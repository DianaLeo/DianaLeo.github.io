---
layout: post
title: "[Internship Acquisition] Npm Start vs Run Directly What Inside Start"
date:   2023-09-18 13:02:39 +1000
categories: fullstack
---



## Problem

![Problem](https://dianaleo.github.io/assets/images/family-album/problem.jpg)

package.json -> scripts -> start : func start

If I run ```func start``` directly, there is an error:

Worker was unable to load entry point ```dist/src/functions/*.js``` Found zero files matching the supplied pattern

There is no ```dist``` path under the current folder.

## How?

```dist``` sounds like something about deploy.

Look at the line right above 'start':
```"prestart": "npm run clean && npm run build",```

This sentence will be automatically run before ```npm start```.
And inside prestart, there is a ```npm run build```.

So, running ```npm start``` instead of running **what inside start** will solve this problem.
