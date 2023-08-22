---
layout: post
title: "How to explain promises in layman's terms"
date:   2023-08-22 11:07:39 +1000
categories: Javascript
---



## You wanted to buy some oranges. You went to a shop. 

 - **Syncronized situation:** No orange was available. You had to wait for several hours there and could do nothing else ```(blocking)```. After several hours, you may or may not get the oranges

 - **Asyncronized situaiton:** No orange was available. Staff gave you a small device ```(Promise)``` with two lights ```(currently no light on=>pending status)```. When oranges get there, you see the green one on, and you can pick them up ```(resolve(data)=>fulfilled status)``` . When there is a problem and oranges are not coming, you see the red light on ```(reject(err)=>rejected status)```. You didn't have to wait for several hours, and could do whatever you like ```(non-blocking)```. 

 ```
 let myPromise = new Promise(function(resolve, reject) {
  setTimeout(function() { resolve("I love You !!"); }, 3000);
});
```
"I love You !!" is the "oranges".
 




## After several hours, you may have got the oranges

Staff didn't care what you are going to do with the oranges. So you took them **out** of the shop, and **then** did whatever you like with them.

If you want to eat them raw (finish using the data), there will be no more return. Your task can stop here. 

```
myPromise.then((value)=>{
  document.getElementById("demo").innerHTML = value;
},(err)=>{});
```

But you wanted to take them to a cake shop, and use them as materials to make a cakes. 

## Then you went to the second shop

The second shop gave you another device with two lights.```(then returns a promise as well)``` 

```
myPromise
.then((oranges)=>{
  //making a cake
  return cake;
},(err)=>{//deal with err})
.then((cake)=>{console.log(cake)},(err)=>{//do something about err});
```

Then you can go to the third shop, the forth shop. That is called ```Promise Chain```