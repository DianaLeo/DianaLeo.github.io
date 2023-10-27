---
layout: post
title: "Why We Use Debounce And useCallback Together"
date:   2023-10-27 20:01:39 +1000
categories: fullstack
---


## The actual function we want to run

```
const fn = send some request
```
A way to control and limit the frequency of a function execution - debounce 


## A simple implementation of debounce

It is a closure

```
function debounce (fn, delay) {
    let timer = null
    return function (...args) => {
        clearTimeOut(timer)
        timer = setTimeOut(()=>{
            fn(...args)
        }, delay)
    }
}
```


## Call a debounced function

Instead of calling the actual function, we call a debounced function

How do we get the debounced function?
When we call the debounce function, it returns a debounced function

```
const debouncedFn = debounce(fn, 1000)
```
Then we can call `debouncedFn`

```
debouncedFn()
debouncedFn()
debouncedFn()
debouncedFn()
debouncedFn()
```

Executing it five times within 1000ms will only send one request


## Core of this implementation

is `let timer = null` in the **closure**

No matter how many times we call `debouncedFn`, there is only one `timer` in the memory,
and all we do is executing the following for five times
```
    clearTimeOut(timer)
    timer = setTimeOut(()=>{
        fn(...args)
    }, delay)
```


## Problem

But what if we are using it in a react component?

What if a state get updated during the five calls?

The whole lot inside the component will run.


`const debouncedFn = debounce(fn, 1000)` will run again

which means `let timer = null` will run again

which means another `debouncedFn` and another `timer` is created in the memory!

Then, executing `debouncedFn()` five times within 1000ms will **NOT** only send one request

The result may be unpredictable


## Solution

`useCallback` hook is used for optimizing performance by memoizing functions

It memoize the function and ensuring it doesn't change between renders unless its dependencies change

So we should write it like this:

```
const debouncedFn = useCallback(
    debounce(fn, 1000),
    [dependencies]
)
```

