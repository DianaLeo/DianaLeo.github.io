---
layout: post
title: "Interview Questions"
date:   2023-08-28 14:57:39 +1000
categories: fullstack
---

## CV tips

Skill: Javascript (Vanilla JS | Node JS)
Skill level: 能用（用eslint实现了代码规范），会用（加感悟：用react做了一个大型可复用的表单）（用eslint实现了代码规范，使团队协作更加简单）

## Interview tips

Avoid yes/no answer.
1. Yes/no
2. Why yes/no
3. Example why, if not what will happen
4. Your own understanding if have

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

## Why do we need to import React from 'react' at the beginning of every component file even if React is never used?

Because the reason that JSX can be used is that React transpile JSX into Javascript behind the scene. Elements will be transpiled to React.createElement, which calls document.createElement inside. Babel is embeded in the dependencies, to do the compiling. Before **React 17**, we need to import React in every file.

From **React 17**, there is a new transpiling function. We don't have to import React in every file, if our project has configured the new JSX transpiling, for example, we used `npx create-react-app`. 

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

reduce unnecessary rerenders by using React.memo, useMemo, useCallback, createEntityAdaptor(get Ids)

## Do you like to write codes with ';' or not

I prefer ';', because if I write an IIFE without a ';' at the end of the last row, this IFFE will be hoisted to the end of the last row. There won't be any line breaks.
```javascript
(function getData(){
fetch('/').then(res=>res.json)
})()
```