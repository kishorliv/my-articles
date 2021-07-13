Like everyday I started my morining with some coffee and tech articles(or talks) in pursuit of improving myself as a developer. Although, I am not into coffee, I have found that good engineers always have it so in hopes of becoming one, I now have it daily. And I think with that rate, I will be a decent one soon. If not, at least I will be a caffeine addict instead of becoming null.

The video was about writing better code and it was pretty good. The context was to improve a piece of javascript code given below. The function would take a number and we need to convert that number into a string such that negative number is parenthesized. (For eg: input of -10 gives output as (10), input of 5 gives output as 5).

```js
function toParenthesized(n) {
    if (n < 0) {
        return "(" + Math.abs(n) + ")";
    } else if (n >= 0) {
        return n;
    }
}
```

Think about the problems this snippet has. One issue is with redundant if-statement for n >= 0 since we already checked for n < 0. Another issue is if we pass a positive number, the function returns a number type but it returns string when the input is negative. The major issue with this code is it does not handle the different types of data the user might pass in this function.
What if the user passes null or undefined?!

```js
let x = null;
toParenthesized(x); // null
toParenthesized(); // undefined
```

So, the obvious thing to do is handle null and undefined. But before that, lets explore a bit about them.

## Exploring null and undefined

null and undefined both are primitive data types in javascript. 'null' represents an absence of any value. 'undefined' represents a variable or object that is declared but not defined( not initialized).

```js
let x = null; // null
let y;

console.log(x); // null
console.log(y); // undefined
```

Now, lets see how we can actually check whether a certain value is null or undefined. We know we can use typeof operator for primitives.

```js
let x = null;
let y;

console.log(typeof x); // object , wtf?!
console.log(typeof y); // undefined
```

But 'typeof x' gave us 'object'! Its actually a bug and has been for a long time since the first version of javascript. It has not been fixed because it would break a lot of existing code. The gist is that we cannot use typeof operator for 'null' even if its a primitive. Maybe we can use equality operators!

```js
let y;

console.log(x == null); // true
console.log(x === null); // true
console.log(y == undefined); // true
console.log(y === undefined); // true
console.log(x == y); // true
console.log(x === y); // false
```

This means that we can use loose equality operator to check for null and undefined at the same time. So, lets see refactor the snippet we started with.

```js
// wraps a negative number with a parenthesis removing '-ve' sign
function toParenthesizedString(num) {
    if (num == null) return; // handle null or undefined
    if (num < 0) return `(${Math.abs(num)})`;
    return num.toString();
}
```

It seems like we have fixed all the issues. However, a function is meant to be used somewhere else. What if the returned value of this function is used somewhere else and the returned value is undefined? We arrived at the same problem we started with!
So, now the solution might not be obvious and relies on the practice and architecture. The above code is fine if there is some sort of layer for input validation. But my preference in this case would be to throw an error so that I would catch it early in development.

```js
function toParenthesizedString(num) {
    if (num == null) throw new Error("Invalid input!"); // handle null or undefined
    if (num < 0) return `(${Math.abs(num)})`;
    return num.toString();
}
```

There are still few things we can improve. We could also return some default value instead of throwing error. The naming convention, usage of if(one liners or not), ternery operators and so on and many of these depend on preferences I guess so I'll leave that to you.

## Conclusion

It is pretty important to understand null and undefined as a javascript developer. Since, they are going nowhere, in my opinion I would follow these:

1. Avoid null initialization if possible
2. Always put validation logic somewhere in the application based on the design
3. Fail as early as possible and,
4. Drink more coffee!
