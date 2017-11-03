---
layout: post
title:  "Types, Prototypes, and Constructor Functions in Javascript: Part 2"
date:   2017-11-02 08:00:00 -0400
categories: Javascript
---

This is the second part in a 2 part series on types, prototypes, and constructor functions in Javascript. If you haven't read part 1, you can do so here ([Part 1](https://szrharrison.github.io/javascript/2017/09/01/types-prototypes-and-constructor-functions-in-javascript-part-1.html)).

## Prototypes and Constructor Functions in Javascript

You may have noticed in code snippet 1 that I referenced the `__proto__` property before showing that it was equivalent to calling `Object.getPrototypeOf(data)`.  That same property also showed up in several of the returned values in that code snippet. Before I go into more detail about what that property is, I want to discourage against using the `__proto__` property. The reason for this (as stated by [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto)) is:
> While Object.prototype.__proto__ is supported today in most browsers, its existence and exact behavior has only been standardized in the ECMAScript 2015 specification as a legacy feature to ensure compatibility for web browsers. For better support, it is recommended that only [Object.getPrototypeOf()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf) be used instead.

Now that we know we shouldn't use the `__proto__` property, let's talk about how it gets there.

#### **Constructor Functions**

The reason that all of the non-primitive datatypes in Javascript are grouped together as **Objects** is because they all delegate behaviors to the **Object** prototype. What this means is that they all reference the properties and methods that are defined on that prototype object. But what is a prototype exactly? The simple answer is, it's a Javascript object that is referred to by other javascript objects. This is easier to see in action, so let's create our own prototype object. The easiest way to do this is with a constructor function.

```javascript
function Side(length) {
  this.length = length
}

Side
/* -> ƒ Side(length) {
 *      this.length = length
 *    }    */

Side.prototype
// -> {constructor: ƒ}

Side.__proto__
// -> ƒ () { [native code] }
```
Snippet 7
{: .snippet-label}

All that we are doing in the above code is defining a function called `Side`. Before we look at what that function does, let's talk about the function itself. Notice that it has a property on it (since functions are objects, they can have properties) called `prototype`. As you can see when comparing lines 11 and 14, this is different than the `__proto__` property. All functions have this property (even if they're not _used_ as constructor functions) and the value should be the same, assuming you haven't changed it yet. This property is, as it's name implies, a prototype object. Because this function has a prototype property (like all functions do), we can use this it to create objects that behave similarly. Here's how this is done:

The value of the `constructor` property of that prototype object is the function itself, so it's somewhat circular (i.e. `Side.prototype.constructor` is the same as `Side`.)
{: .note}

```javascript
function Side(length) {
  this.length = length
}

var mySide = new Side(3)

mySide
/* -> Side {length: 3}
 * Here is the same object, but expanded so you can see everything.
 * -> {
 *      length: 3,
 *      __proto__: {
 *        constructor: ƒ Side(length),
 *        __proto__: Object
 *      }
 *    }   */

Object.getPrototypeOf(mySide)
/* -> {
 *      constructor: ƒ Side(length),
 *      __proto__: Object
 *    }   */

Object.prototype.toString.call(mySide)
// -> "[object Object]"

mySide.constructor
/* -> ƒ Side(length) {
 *      this.length = length
 *    }   */

typeof mySide
// -> "object"

mySide instanceof Object
// -> true

mySide instanceof Function
// -> false

mySide instanceof Side
// -> true
```
Snippet 8
{: .snippet-label}

When we use the `new` operator, we are creating an **instance** of the object defined in our constructor function. We'll talk about how those instances are related to their constructor functions and prototypes a little later on.

Before we look at why we care about prototypes, a quick note about how `this` works in constructor functions. In constructor functions, when calling `new`, `this` refers to the object being constructed. If we were to try to call the constructor function without using the `new` operator, however, `this` would no longer refer to an object we're creating, and would instead refer to the global object.
```javascript
function Side(length) {
  this.length = length
}

Side(3)  // If we call the Side function, 'this' will refer to the global object.

length   // Because 'this' was the global object, we created a global variable called 'length'
// -> 3
```
Snippet 9
{: .snippet-label}

This isn't bad just because it's not the behavior we were trying to achieve, but also because [global variables are bad](http://wiki.c2.com/?GlobalVariablesAreBad). One way around this is to throw an error in our constructor function:
```javascript
function Side(length) {
  if(!(this instanceof Side)) {
    throw new Error('Side must be called with the "new" operator')
  }
  this.length = length
}

Side(3)  // If we call the Side function, it will throw an error
```
Snippet 10
{: .snippet-label}

Or, if we want to be able to call it as a function and still return a new Side instance:
```javascript
function Side(length) {
  if(!(this instanceof Side)) {
    return new Side(length)
  }
  this.length = length
}

Side(3)
// -> Side {length: 3}
```
Snippet 11
{: .snippet-label}

Now that we've seen how to make prototypes (by writing their constructor functions), let's look at why we use them. In the above code, you'll notice that the `mySide` object that gets created with the Side function has all of the properties we defined in the constructor function, but `Side.prototype` just has a property called constructor. This is because, when we create an instance by calling `new` on the constructor function, the object's properties get set as the function is being executed.

You may be thinking, why do we care about using the `new` operator. After all, we could just use our constructor functions to return the objects we want like so:
```javascript
function Side(length) {
  return {
    length: length
  }
}

Side(3)
// -> {length: 3}
```
Snippet 12
{: .snippet-label}

As you can see, line 8 in snippets 12 and 8 are almost identical. However, we no longer have an instance of a Side object (i.e. the object returned in snippet 12 is only an object, and has no other types defined for it.) While this is okay in simple examples such as this, it becomes insufficient when trying to model more complex systems. If you want to know what prototypes and constructor functions allow us to do that returning objects doesn't, keep an eye out for Object Oriented Programming (OOP) in Javascript sometime in the near future.
