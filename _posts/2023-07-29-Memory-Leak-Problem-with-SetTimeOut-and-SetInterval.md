---
layout: post
title:  "Memory Leak with SetTimeOut and SetInterval"
date:   2023-07-29 19:39:59 +1000
categories: git
---

切换页面的时候计时器还没工作完毕，但是切换页面的时候，DOM就消失了，componentwillunmount，那么用ref.current就会=null，则access一切dom元素属性都会出错。
我是靠if条件判断加return解决了这个问题：if myref.current!=null do actions, else return.
以及达到clearInterval或clearTimeout的触发条件后，也要接return，否则即使页面切换，在后台timer也会一直跑。
即使用setTimeOut迭代方法代替setInterval，页面切换的时候依然涉及这个问题，并没有觉得用setTimeOut迭代方法代替setInterval会变好用。     

遇到的第二个问题是，DOM加载完毕ref.current才会不等于null，对于一些event handeler，交互事件触发才用到ref.current没问题，但如果和用户交互触发事件无关，而是一开始加载的时候需要用到此dom，则会报错，因为ref.current还是null。
解决办法，用useeffect。React 将 useEffect 中的代码延迟到了视图渲染完成之后执行。

额外知识点：ref.current不能作为其他hook的依赖项