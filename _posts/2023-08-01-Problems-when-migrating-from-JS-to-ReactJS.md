---
layout: post
title: "Problems when migrating from vanila JS to ReactJS"
date:   2023-08-01 15:36:59 +1000
categories: React
---

I am migrating 50projects50days from plain HTML+CSS+JS to React. When I learnt React at the beginning, I couldn't get the hang of some hooks. But migrating projects is a very good chance to deeply understand React. 

Here are some errors I met and solutions, thru which I got a further understanding of useState, useRef, and useEffect.

# 0. Auto text effect
This is the final effect. By increasing or decreasing the number input, the text typing speed changes automatically.
![Auto text effect.png](https://dianaleo.github.io/assets/images/01-08-2023/Auto-text-effect.png)

Origianlly, I just copy the .js file into the first half of a function component, and copy the .html file into the second half inside return. (CSS is not included in this blog, no big changes). It looks like this:
```
function AutoTextEffect() {
    const text = "Lorem..."
    const textP = document.querySelector('#text');
    const speedNumber = document.querySelector('input');

    let speed = speedNumber.value;
    let i = 0;

    timer();
    speedNumber.addEventListener('input',(e)=>{
        speed = e.target.value;
    })

    function timer() {
        textP.textContent = text.slice(0,i+1);
        i++;
        if (i >= text.length) {i = 0;}
        setTimeout(timer, 500/speed);
    }

    return (
        <section className='AutoTextEffect_container'>
            <div id="textContainer">
                <p id="text"></p>
            </div>
            <div id="speedContainer">
                <span>Speed:  </span>
                <input type="number" id="number" value='1' min="1" max="10"></input>
            </div>
        </section>
    )
}
```
# 1. Error when using Queryselector and getElementby
**Cannot read properties of null (reading 'value')**
![Error when loading page and switching tabs.png](https://dianaleo.github.io/assets/images/01-08-2023/Error-when-switching-tabs-using-queryselector.png)
Queryselector and getElementby are not recommanded in React. DOM doesn't exist When calling this method. Page is still loading. But even if I put them under useEffect, same error still exist.

#### Solution: In React way, useRef and useEffect should be used instead.
```
    let myRef = useRef(null);
    ...
    <p ref={myRef}></p>
    ...
    //Everytime we operate a DOM, we operate myRef.current
    myRef.current
```

# 2. Error when using useRef on page loading
But still the **Cannot read properties of null (reading 'value')** problem exists. Because the current property is not null only when DOM finishes loading. If we only access the current property within a UI eventhandler which is trigged by user interactions, then no problem.

#### Solution: put the code accessing current property into useEffect
```
    useEffect(() => {
        timer();
    }, []);
```

By passing an empty array, the useEffect hook will only run a single time. 

# 3. Why do we use refs
1. refs make it easier to uniquely identify + select in linear time the corresponding element (as compared to id which multiple elements can, by mistake, have the same value for + compared to document.querySelector which needs to scan the DOM to select the correct element)
2. refs are aware of react component lifecycle, so react would make sure that refs are updated to null when component unmounts and more out of the box convenience.
3. refs as a concept + syntax are platform agnostic, so you can use the same understanding in react native and the browser, while query selector is a browser thing
4. for SSR, where there is no DOM, refs can still be used to target react elements

# 4. Problem on addEventListener
No error now on page loading. But the interaction is not correct. AddEventListener is not working.
![Use-onInput-event-instead-of-addeventlistener.png](https://dianaleo.github.io/assets/images/01-08-2023/Use-onInput-event-instead-of-addeventlistener.png)

#### Solution: add eventHandler this way 
```
    onInput={e => onInputHandler(e)};
    ...
    function onInputHandler(e) {
        setSpeed(e.target.value);
    }
```
and use State
```
    const [speed, setSpeed] = useState(1);
    ...
    value={speed} //instead of value='1'

```
Difference btw onChange & onInput:
onChange is called when the element loses focus, onInput is called immediately.

Currently, my code looks like this:
```
function AutoTextEffect() {
    const text = "Lorem..."
    let i = 0;

    const [speed, setSpeed] = useState(1);
    let textP = useRef(null);
    let speedNumber = useRef(null);

    useEffect(() => {
        timer();
    }, []);

    function onInputHandler(e) {
        setSpeed(e.target.value);
    }

    function timer() {
        textP.current.textContent = text.slice(0, i + 1);
        i++;
        if (i >= text.length) { i = 0; }
        setTimeout(timer, 500 / speedRef.current);
    }

    return (
        <section className='AutoTextEffect_container'>
            <div id="textContainer">
                <p ref={textP} id="text"></p>
            </div>
            <div id="speedContainer">
                <span>Speed:  </span>
                <input ref={speedNumber} type="number" id="number" value={speed} min="1" max="10"></input>
            </div>
        </section>
    )
}
```

# 5. UseEffect is called twice
Now I connect the **speed** variable with **input** element, but the typing speed is not right. When speed=1, the letters are printed out two by two, not one by one. It looks like **timer()** is called twice.

1. This is not a bug, it is a new feature of React 18.
2. This only happened in development mode, and in Strict Mode. It is only called once in production mode.
3. The reason is to simulate unmount and remount component. For help developer find bugs that are caused by repeat mounting.

#### Solutions
1. Cancel the strict mode, not recommended

```
root.render(
  //<React.StrictMode>
    <App />
  //</React.StrictMode>
);
```
2. The right question isn’t “how to run an Effect once,” but “how to fix my Effect so that it works after remounting”.
```
    useEffect(() => {
        timer();
        return () => {
            if (myTimeout != null)
                clearTimeout(myTimeout);
        }
    }, []);
```
Problem solved!

#  6. setTimeout always get the old state value.
The last problem is about using useState in setTimeout.

When I hit the **^** button, The **speed** variable is increasing, but the actual speed is not increasing. If I print out the **speed** variable within **setTimeout** function, it is always the old value, which is 1.

#### Soluiton； useRef
```
    const [speed, setSpeed] = useState(1);
    let speedRef = useRef(null);
    speedRef.current = speed;
    ...
    //within timer()
    myTimeout = setTimeout(timer, 500 / speedRef.current);
    //instead of 500/speed
```

That's all the problems I met during migration.
Final code here:
```
function AutoTextEffect() {
    const text = "Lorem..."

    const [speed, setSpeed] = useState(1);
    let speedRef = useRef(null);
    speedRef.current = speed;
    let textP = useRef(null);
    let speedNumber = useRef(null);
    let i = 0;
    let myTimeout = null;

    useEffect(() => {
        timer();
        return () => {
            if (myTimeout != null) clearTimeout(myTimeout);
        }
    }, []);

    function onInputHandler(e) {
        setSpeed(e.target.value);
    }

    function timer() {
        if (textP.current != null)
            textP.current.textContent = text.slice(0, i + 1);
        i++;
        if (i >= text.length) { i = 0; }
        myTimeout = setTimeout(timer, 500 / speedRef.current);
    }

    return (
        <section className='AutoTextEffect_container'>
            <div id="textContainer">
                <p ref={textP} id="text"></p>
            </div>
            <div id="speedContainer">
                <span>Speed:  </span>
                <input ref={speedNumber} onInput={e => onInputHandler(e)} type="number" id="number" value={speed} min="1" max="10"></input>
            </div>
        </section>
    )
}

```




