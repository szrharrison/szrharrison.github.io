---
layout: post
title:  "Types, Prototypes, and Constructor Functions in Javascript: Part 1"
date:   2017-09-01 08:00:00 -0400
categories: Javascript
---
All functionality and features introduced in ES6 will be marked with a <sup><sup><em><strong><span class='tooltip'>6<span class='tooltiptext'>new in ES6</span></span></strong></em></sup></sup>. Also, I do not use semicolons when writing Javascript. Mislav Marohnić makes my argument for me [here](https://mislav.net/2010/05/semicolons/).
{: .note}

It's a common occurrence while programming to need to verify what types of data you're working with. Once you know what type of data you're working with, you know what functions you can call on that data. In Javascript there are many different ways you can find out the datatype of what you're working with. Here are a few examples with what each returns:

```javascript
function Data() {
  this.isData = true
  this.isConstructorFunction = true
}

var data = new Data()

data
/* -> Data {
 *        isData: true,
 *        isConstructorFunction: true,
 *        __proto__: Object
 *    }
 */

data.__proto__
/* -> {
 *        constructor: ƒ Data(),
 *        __proto__: Object
 *    }
 */

Object.getPrototypeOf(data)   // always returns the same as data.__proto__
/* -> {
 *        constructor: ƒ Data(),
 *        __proto__: Object
 *    }
 */

Object.prototype.toString.call(data)
/* -> [object Object]
*/

data.constructor
/* -> ƒ Data() {
 *      this.isData = true
 *      this.isConstructorFunction = true
 *    }
 */


typeof data
/* -> "object"
*/

data instanceof Data
/* -> true
*/
```
Snippet 1
{: .snippet-label}

Before we dive in to what each of these lines is doing and how they're getting the values that they are, it's import to understand exactly what types are in Javascript.

## Types in Javascript

In Javascript, types can be broken up into 2 different categories: **Primitives** and **Objects**.

**Primitives** are anything that is immutable (i.e. their values can't be changed) and include the following:
 + Boolean
 + Null
 + Number
 + String
 + Symbol<sup><sup><em><strong><span class='tooltip'>6<span class='tooltiptext'>new in ES6</span></span></strong></em></sup></sup>
 + Undefined

Note that, unlike many other languages, strings are immutable in Javascript.

All other datatypes fall under the category of **Object** and, as we'll soon see, so do the primitives. This includes, but isn't limited to, the following built in datatypes (this list is actually fairly extensive and a full version of it can be found [on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)):

 + Object
 + Function
 + Boolean
 + Symbol<sup><sup><em><strong><span class='tooltip'>6<span class='tooltiptext'>new in ES6</span></span></strong></em></sup></sup>
 + Error
 + Number
 + Math
 + Date
 + String
 + RegExp
 + Array
 + Promise

#### **The Primitive Datatypes**

You'll notice that some of these built in objects are the same as the primitive datatypes. This is because a primitive datatype can only be a value. It can't have any methods defined for it or have any properties. Therefore, in order to allow you to do things like:
```javascript
var string = 'this is a string'.split(' ')
```
Javascript has a built in String object as well as the string primitive datatype. Unfortunately, this leads to some issues when type checking strings, booleans, and numbers. Here is an example of how this can cause trouble:

```javascript
// String literal syntax, which involves the primitive datatype
var string1 = 'this is a string'

string1
// -> "this is a string"

Object.getPrototypeOf(string1)
// -> String {length: 0, formatUnicorn: ƒ, truncate: ƒ, splitOnLast: ƒ, contains: ƒ, …}

Object.prototype.toString.call(string1)
// -> "[object String]"

string1.constructor
// -> ƒ String() { [native code] }

typeof string1
// -> "string"

string1 instanceof String
// -> false

// Constructor function syntax, which involves the object datatype
var string2 = new String('this is a string too')

string2
/* -> S{
 *      0: "t",
 *      1: "h",
 *      2: "i",
 *      3: "s",
 *      4: " ",
 *      5: "i",
 *      6: "s",
 *      7: " ",
 *      8: "a",
 *      9: " ",
 *      10: "s",
 *      11: "t",
 *      12: "r",
 *      13: "i",
 *      14: "n",
 *      15: "g",
 *      16: " ",
 *      17: "t",
 *      18: "o",
 *      19: "o",
 *      length: 20,
 *      [[PrimitiveValue]]: "this is a string too"
 *    }     */

Object.getPrototypeOf(string2)
// -> String {length: 0, formatUnicorn: ƒ, truncate: ƒ, splitOnLast: ƒ, contains: ƒ, …}

Object.prototype.toString.call(string2)
// -> "[object String]"

string2.constructor
// -> ƒ String() { [native code] }

typeof string2
// -> "object"

string2 instanceof String
// -> true
```
Snippet 2
{: .snippet-label}

The difference between the 2 syntaxes for creating strings becomes clear when comparing the `typeof` and `instanceof` operators, along with the return values of the actual variables. The reason that the lines involving `.` calls all returned the same thing for both variables is because, in order to call a method or access a property, the primitive strings are converted into objects. This same process happens for the `Number` and `Boolean` objects and their corresponding primitive datatypes.

There are also 2 other unintuitive characteristics of the Javascript type system. In addition to the Number primitive type, the `NaN` and `Infinity` properties are defined on the global object (read more about the global object [on MDN](https://developer.mozilla.org/en-US/docs/Glossary/Global_object).) These 2 properties both are identified as numbers, despite any implication that they might not be. The reason for this has to do with memory. From [javascriptrefined.io](https://javascriptrefined.io/nan-and-typeof-36cd6e2a4e43):
> As Wikipedia states:
> > Computer arithmetic cannot directly operate on real numbers, but only on a finite subset of rational numbers, limited by the number of bits used to store them.
>
> In ordinary arithmetic, `3.2317006071311 * 10 ** 616` is a real finite number, but, by ECMAScript standards, it is simply too large (i.e, considerably greater than `Number.MAX_VALUE`), and is therefore represented as `Infinity`. Attempting to divide an infinity by an infinity yields `NaN`.

For more information on the limits of numbers in javascript, check out [this article](https://modernweb.com/what-every-javascript-developer-should-know-about-floating-points/).
{: .note}

Because Javascript doesn't allow for numbers over a certain size, it tries to find some other way to represent them. To do this, the "numbers" `NaN` and `Infinity` were added. Checking datatype for these works as follows:
```javascript
Infinity
// -> Infinity

Object.getPrototypeOf(Infinity)
// -> Number {constructor: ƒ, toExponential: ƒ, toFixed: ƒ, toPrecision: ƒ, toString: ƒ, …}

Object.prototype.toString.call(Infinity)
// -> "[object Number]"

Infinity.constructor
// -> ƒ Number() { [native code] }

typeof Infinity
// -> "number"

Infinity instanceof Number
// -> false

NaN
// -> NaN

Object.getPrototypeOf(NaN)
// -> Number {constructor: ƒ, toExponential: ƒ, toFixed: ƒ, toPrecision: ƒ, toString: ƒ, …}

Object.prototype.toString.call(NaN)
// -> "[object Number]"

NaN.constructor
// -> ƒ Number() { [native code] }

typeof NaN
// -> "number"

NaN instanceof Number
// -> false
```
Snippet 3
{: .snippet-label}

This should look fairly similar to the results of calling these same methods and operators on the primitive string datatype above.

#### **The Object Datatype**

Now that we have a better understanding of what the primitive types are in Javascript, we need to look at the more complex ways of differentiating data. I mentioned earlier that everything else in javascript falls under the category of the **Object** datatype. **Objects** are Javascript's way of storing data in memory. This is fundamentally different from how primitive types work. Consider the following code:
```javascript
/* Varaibles are pointers to locations in memory.
 * By reassigning a variable, we change where it is pointing.
 */
var x = 3
var y = x

x = 4

x    // -> 4

y    // -> 3

/* Since variables point to locations in memory,
 * if we change something at that location,
 * the value of the variable changes.
 */
var arrayX = ['string', 'other string', 2] // Remember arrays are objects too.
var arrayY = arrayX

arrayX    // -> ["string", "other string", 2]
arrayY    // -> ["string", "other string", 2]

arrayX.length = 0

arrayX    // -> []
arrayY    // -> []
```
Snippet 4
{: .snippet-label}

In the first example, `x` and `y` are both pointing to the primitive value `3`. `x` is then reassigned to be `4`. Since `3` and `4` don't exist in the same "location" in memory, `x` and `y` are no longer pointing at the same value.

In the second example, `arrayX` and `arrayY` are both pointing to a specific `Array` object. That object has the properties `0`, `1`, and `2` (each with the value of their respective indexes) along with a property `length`, which holds the number of items in the array. Since `x` and `y` are both pointing to the same location in memory, if we change a property of that location, it changes the values of both `x` and `y`.

This is the basic usage of **Object** in Javascript. Despite the simplicity of what an object is, they are powerful tools which you can use to accomplish a wide variety of things. To continue the demonstration of identifying datatypes from earlier:

```javascript
var obj = {}

obj
// -> {}

Object.getPrototypeOf(obj)
/* -> {
 *      __defineGetter__: ƒ,
 *      __defineSetter__: ƒ,
 *      hasOwnProperty: ƒ,
 *      __lookupGetter__: ƒ,
 *      __lookupSetter__: ƒ, 
 *      …
 *    }   */

Object.prototype.toString.call(obj)
// -> "[object Object]"

obj.constructor
// -> ƒ Object() { [native code] }

typeof obj
// -> "object"

obj instanceof Object
// -> true
```
Snippet 5
{: .snippet-label}

As you can see, these lines of code operate more like you might expect for objects than they did for the primitive datatypes. However, keep in mind that everything that isn't a primitive datatype is an object, so there may still be some things that surprise you. For example:

```javascript
function fn() {}

fn
// -> ƒ fn() {}

Object.getPrototypeOf(fn)
// -> ƒ () { [native code] }

Object.prototype.toString.call(fn)
// -> "[object Function]"

fn.constructor
// -> ƒ Function() { [native code] }

typeof fn
// -> "function"

fn instanceof Object
// -> true

fn instanceof Function
// -> true
```
Snippet 6
{: .snippet-label}

As you can see, functions are instances of both **Functions** _and_ **Objects**. In order to understand how and why that's the case, we need to explore **prototypes** and **constructors**, which we will cover in [Part 2](https://szrharrison.github.io/javascript/2017/11/02/types-prototypes-and-constructor-functions-in-javascript-part-2.html).
