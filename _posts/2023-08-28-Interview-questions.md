---
layout: post
title: "Interview Questions"
date:   2023-08-28 14:57:39 +1000
categories: fullstack
---

## CV tips

Skill: Javascript (Vanilla JS | Node JS)
Skill level: 能用（用eslint实现了代码规范），会用（加感悟：用react做了一个大型可复用的表单）（用eslint实现了代码规范，使团队协作更加简单）

## Why do you use React

Vanilla JS -> Problem: lengthy code accessing DOM
|
JQuery -> Solved: a simple syntax for accessing DOM, a unified syntax for cross-browser problem
|      -> Problem: Projects got bigger and bigger. When JQuery was invented, there were no hugh projects.
|
React -> for bigger projects, we need declarative management instead of instructive management
|         declarative thoughts aim on the result, instructive thoughts aim on the process
|         declarative methods is more friendly to new people, easier to manage team for team leader.
|      -> React concentrates on states and logic, instead of manipulating DOM. Better for **SOLID & RMR**
Some other framework in the future:
       -> Features may change, syntax may change, but declarative thoughts and component-based thoughts will never change



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

## React states and props differences

mutable and immutable

## React performance improvement

reduce uneccessary rerenders by using useMemo