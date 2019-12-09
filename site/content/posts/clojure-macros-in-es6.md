+++
author = "Esteban"
categories = ["javascript", "es6"]
date = 2017-09-10T18:40:44Z
description = ""
draft = false
slug = "clojure-macros-in-es6"
tags = ["javascript", "es6"]
title = "Clojure threading macros in ES6"

+++


While working in the functionality for [getting a docker-compose path from the cursor position](https://estebansastre.com/getting-docker-compose-path-from-cursor-position/), I realized at some point I was writing constantly code like this:

```javascript
let first_result = func_call1(val);
let second_result = func_call2(first_result);
let third_result = func_call3(second_result);

...etc...
```

This is not necessarily bad, it improves code readability and helps to reason about the flow of execution, normally better than calling those functions in a nested way:

```javascript
func_call3(func_call2(func_call1(val))); // Phew.
```

But yet, it feels somewhat cumbersome to nest too many function calls that way. Some time ago I started looking into Clojure for fun and discovered [threading macros](https://clojure.org/guides/threading_macros). [Thread first](https://clojuredocs.org/clojure.core/-%3E) (**'->'**) and [Thread last](https://clojuredocs.org/clojure.core/-%3E%3E)(**->>**) macros pipe the result of the application of a function to a value to the next function in the list. The difference is that thread first adds the result as the first argument to the next function and thread last to the last one.


Wouldn't it be nice to have some kind of mechanism in Javascript?. Looking around I found someone had already written [a nice article about it](https://jondavidjohn.com/clojure-threading-macros-in-javascript/). The target is to have a function to be called like this:

```javascript
let result = thread("->", "3", 
                        parseInt,
                        [sum, 3],
                        [diff, 10],
                        str); // "-4"
```

And what would be going on under the hood is this:

```javascript
let sameresult = diff(sum(3, parseInt("3")), 10);
```

So I decided to reimplement the code taking advantage of the new features that came along with **ES6**(arrow functions, destructuring, rest parameters...).

```javascript
const thread = (operator, first, ...args) => {
    let isThreadFirst;
    switch (operator) {
        case '->>':
            isThreadFirst = false;
            break
        case '->':
            isThreadFirst = true;
            break;
        default:
            throw new Error('Operator not supported');
            break;
    }
    return args.reduce((prev, next) => {
        if (Array.isArray(next)) {
            const [head, ...tail] = next;
            return isThreadFirst ? head.apply(this, [prev, ...tail]) : head.apply(this, tail.concat(prev));
        }
        else {
            return next.call(this, prev);
        }
    }, first);
}
```


So when executing the code using the thread first operator for example:

```javascript
let result = thread("->", "3", 
                        parseInt,
                        [sum, 3],
                        [diff, 10],
                        str); // "-4"

console.log(result); // -4 
```

and using the thread last operator:

```javascript
let result = thread("->>", "3", 
                        parseInt,
                        [sum, 3],
                        [diff, 10],
                        str); // "-4"

console.log(result); // 4 
```


Have fun!




# Links:

* [Threading macros in Clojure](https://blog.nilenso.com/blog/2016/05/12/threading-macros-in-clojure/)
* [Threading macros in plain old javascript](https://jondavidjohn.com/clojure-threading-macros-in-javascript/)
* [Thread last clojure](https://clojuredocs.org/clojure.core/-%3E%3E)
* [Thread first clojure](https://clojuredocs.org/clojure.core/-%3E)

