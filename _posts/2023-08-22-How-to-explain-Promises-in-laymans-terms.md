---
layout: post
title: "Interview Questions"
date:   2023-08-28 14:57:39 +1000
categories: fullstack
---



## What HTML5 techniques have you used?

New features:
- media tags:<autio><video>
- more powerful <form>
- <canvas>
- geographic api
- web storage: localStorage.setItem/getItem, sessionStorage
- web worker for multi-thread

## Catch-control, cookies, web storage

catch-control is for storing static files,
cookies and web storage are for storing business data.

catch-control and cookies are set under http header,
web storage don't communicate with server.
If you use cookies, server end generates a sessionID, response back to browser. Browser adds it to header everytime it sends a request. 

user data (token) can be stored in both cookies and localStorage. Differences are that token is added to http header AUTOMATICALLY when using cookies, token is added to header MANUALLY when using localStorage. 

token should be put in header or body? header, because more secure.