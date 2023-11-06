---
layout: post
title: "When I Write React for Three Times - Animated Countdown"
date:   2023-09-18 20:21:39 +1000
categories: react
---



## 

When I write the first version, I haven't learn SOLID principles and Component design. 

- I used a lot of **useRefs** to manipulate **DOM** elements, which is not professional.


```javascript
import './AnimatedCountdown.css';
import { useRef, useEffect } from 'react';

function AnimatedCountdown() {
    const div_count = useRef(null);
    const div_goContainer = useRef(null);
    const div_countDownContainer = useRef(null);

    useEffect(() => {
        myfunction();
        return () => {
            console.log('return:', div_count.current);
        };
    }, []);

    const myfunction = () => {
        let count = 5;
        div_count.current.textContent = count;
        div_count.current.classList.add('in');
        div_goContainer.current.hidden = true;
        div_goContainer.current.classList.remove('in');
        div_countDownContainer.current.classList.remove('out');

        let c = 0;//async timer to remove counter animation
        setTimeout(() => {
            c += 1;
            if (div_count.current === null) { return }
            div_count.current.classList.remove('in');
            const myInterval2 = setInterval(() => {
                if (div_count.current === null) { return }
                div_count.current.classList.remove('in');
                if (c == count + 1) {
                    clearInterval(myInterval2);
                    return;
                }
            }, 1000)
        }, 900);

        addAnimation();
        function addAnimation() {
            let timer = setTimeout(() => {
                count -= 1;
                if (count < 0) {
                    console.log(count);
                    div_countDownContainer.current.classList.add('out');
                    div_goContainer.current.hidden = false;
                    div_goContainer.current.classList.add('in');
                    clearTimeout(timer);
                    return;
                }
                console.log('now count:', count);
                if (div_count.current === null) {
                    console.log('div_count.current is null!');
                    clearTimeout(timer);
                    return;
                } 
                div_count.current.textContent = count;
                div_count.current.classList.add('in');
                addAnimation();
            }, 1000);
        }
    }

    return (
        <div className='AnimatedCountdown_container'>
            <div ref={div_countDownContainer} id="countDownContainer">
                <div ref={div_count} id="count"></div>
                <p>Get Ready</p>
            </div>
            <div ref={div_goContainer} id="goContainer">
                <p>Go</p>
                <button onClick={myfunction}>Start again</button>
            </div>
        </div>
    )
}
export { AnimatedCountdown };
```





