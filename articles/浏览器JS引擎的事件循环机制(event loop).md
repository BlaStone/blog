# 浏览器JS引擎的事件循环机制(event loop)

### 尚未完成======

js执行栈中的任务分为两种：宏任务(macro task)，微任务(micro task)，
同一轮事件循环中，微任务总是优先于宏任务执行；

每一轮的事件循环都是从宏任务开始的，以微任务的结束为结束；

宏任务包含script，settimeout，setinterval等，settimeout，setinterval属异步事件，需添加至宏任务的event quene；所谓settimeout与setinterval的第二个参数的延时执行，并不是说到了时间就立即执行，而是在到时间时，将其挂载至宏任务的event quene中，当宏任务的非异步事件与此轮微任务执行完时，下一轮宏任务开始执行上一轮挂载的event quene中的事件；

微任务包含promise中的then（需挂载至微任务的event quene，等宏任务的非异步事件处理完成再处理），process.nexttick()，Object.observe，MutationObserver等；

因promise可用微任务实现亦可用宏任务实现，不同浏览器的实现方式可能不同，所以在不同浏览器上可能存在不同情况，但一般都默认为微任务。

事件循环的顺序，决定js代码的执行顺序。进入整体代码(宏任务)后，开始第一次循环。接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务.

```js

console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})

process.nextTick(function() {
    console.log('6');
})

new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})


```