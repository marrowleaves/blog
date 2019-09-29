---
title: Print Numbers
date: 2016-06-08 14:18:06
---

## It all started with an interview problem ...

> print the binary representation of a given number

It's easy to extend `binary` into more radixes:

 ```C
char *reverse(char *str, int len) {
    char *p1, *p2;

    if (!str || !*str) return str;
    for (p1 = str, p2 = str + len - 1; p1 < p2; p1++, p2--) {
        *p1 = *p1 ^ *p2;
        *p2 = *p1 ^ *p2;
        *p1 = *p1 ^ *p2;
    }
    return str;
}

char *itoa(int num, char *buffer, int radix) {
    int i;
    int sign;
    int rem;

    if (!num) {
        buffer = "0";
        return buffer;
    }

    if ((sign = num) < 0) num = -num;

    i = 0;
    do {
        rem = num % radix;
        buffer[i++] = (rem > 9) ? 'a' + (rem - 10) : '0' + rem;
    } while(num /= radix);

    if (sign < 0) buffer[i++] = '-';

    buffer[i] = '\0';
    buffer = reverse(buffer, i);

    return buffer;
}
```

And there's a native API in JS to achieve the same result:

```JS
let itoa = (num, radix) => num.toString(radix)
```

Or we can just translate the C code into JS:

```JS
let itoa = (num, radix) => {
    let chars = []
    let sign = num < 0

    if (sign) num *= -1
    if (!Math.floor(num)) return ''

    do {
        let rem = num % radix
        chars.push((rem > 9) ? String.fromCharCode('a'.charCodeAt(0) + rem - 10) : rem)
    } while(num = Math.floor(num / radix)) // we cannot use '~~' here since it overflows

    if (sign < 0) chars.push('-')

    return chars.reverse().join('')
}
```

## What about the fractional parts?

```JS
let ftoa = (num, radix) => {
    let frac = num - Math.floor(num)
    let chars = ['0.']
    let t = 1

    if (!num) return ''

    while (t = t / radix) {
        if (frac >= t) {
            let q = ~~(frac / t)
            //TODO: use a dict here
            chars.push((q > 9) ? String.fromCharCode('a'.charCodeAt(0) + q - 10) : q)
            frac -= t * q
        } else {
            chars.push('0')
        }
        if (!frac) return chars.join('')
    }
}
```

### A V8 bug?

For `Number.MIN_VALUE` => `5e-324` (2<sup>-1077</sup>)

* in V8 `Number.MIN_VALUE.toString(2)` => `0`
* in my implementation `ftoa(Number.MIN_VALUE, 2)` => `0.0...01` (1074 digits after the ~~decimal~~ radix point)

### Other helper functions

* to check the range of radix:

```JS
let radixInRange = radix => Array.from(Array(35).keys()).map(i => i + 2).includes(radix)
```

* combine them together:

```JS
let toString = (num, radix) => radixInRange(radix) && (itoa(num, radix) + ftoa(num, radix))
```

## What about parsing?

```JS
let ch2Num = radix => c => {
    let res = +c

    if (Number.isNaN(res)) {
        res = c.charCodeAt(0) - 'a'.charCodeAt(0) + 10
    }

    if (res >= radix) throw new Error('invalid character: ' + c)

    return res
}
let parseInt = (numStr, radix) => [].map.call(numStr, ch2Num(radix))
    .reduce((last, cur) => last * radix + cur)

let parseFrac = (numStr, radix) => [].map.call(numStr.substr(numStr.indexOf('.') + 1), ch2Num(radix))
    .reduce((last, cur, i) => last + cur * Math.pow(radix, -i - 1), 0)

let parse = (numStr, radix) => checkRadix(radix) && (parseInt(numStr, radix) + parseFrac(numStr, radix))
```

Check the results:

* `parseFrac('0.1', 3).toString(3)` => `"0.1"`
* `parseFrac('0.2', 3).toString(3)` => `"0.2"`
* ***sadly*** `parseFrac('0.11', 3).toString(3)` => `"0.1022222222222222222222222222222222"`, requires some special trick here

### Optimization (todo)

http://www.numberworld.org/y-cruncher/internals/radix-conversion.html
https://en.wikipedia.org/wiki/Exponentiation_by_squaring
