---
title: Trampoline
---

A trampoline is a loop that iteratively invokes *thunk-returning functions* to implement *continuations* and *control flow operators* in a language that **does not** feature ***first-class continuation*** and ***tail-call optimization***, but **do have** ***first-class functions***.

* If with first-class contiuation support, ***CPS*** can be used.
* If without first-class functions, tail calls can be converted into `goto` in the trampoline.

## Code examples

```js
// recursive version
function fib_r(n) {
    return n >= 0 ?
        (n < 2 ? n : fib_r(n-1) + fib_r(n-2)) :
        (n > -2 ? -n : fib_r(n+2) - fib_r(n+1))
}
// iterative version
function fib_i(n) {
    const sign = n > 0 ? 1 : (n < 0 ? -1 : 0)
    let a = 0, b = 1
    while(n) {
        [a, b] = [b, a + sign * b]
        n -= sign
    }
    return a
}
// trampoline version
const trampoline = fn =>
    (...args) => {
        let res = fn.apply(this, args)
        while(res instanceof Function) res = res()
        return res
    }
function fib_t(n) {
    const thunk = (a, b, n) => {
        const sign = n > 0 ? 1 : (n < 0 ? -1 : 0)
        return !n ?
            () => a :
            () => thunk(b, a + sign * b, n - sign)
    }
    return trampoline(thunk)(0, 1, n)
}
// to validate
function range(length, st = 0) {
    return Array.from({ length }, (e, i) => st + i)
}
function arrSame(arr) {
    return arr.every(e => e === arr[0])
}
// due to the lack of TCO in JS, this should be less than 20 when fib_r enabled 
const size = 10 
range(2 * size + 1, -size)
    .map(e => [
            // fib_r,
            fib_i,
            fib_t
        ].map(fn => fn(e)))
    .every(arrSame)
// => true
```

## Ref

* [Trampoline (computing)](https://en.wikipedia.org/wiki/Trampoline_(computing))
* [Asynchronous programming and continuation-passing style in JavaScript](https://2ality.com/2012/06/continuation-passing-style.html)
* [On Recursion, Continuations and Trampolines](https://eli.thegreenplace.net/2017/on-recursion-continuations-and-trampolines/)
