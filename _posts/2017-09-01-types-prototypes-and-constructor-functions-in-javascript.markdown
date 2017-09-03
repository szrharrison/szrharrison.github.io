---
layout: post
title:  "Types, Prototypes, and Constructor Functions In Javascript"
date:   2017-09-01 08:00:00 -0400
categories: Javascript
---
<span class='note'>Note: All functionality and features exclusive to ES6 will be marked with a <sup><sup>**_6_**</sup></sup>. Also, I do not use semicolons when writing Javascript. Mislav Marohnić makes my argument for me [here](https://mislav.net/2010/05/semicolons/)</span>

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

Before we dive in to what each of these lines is doing and how they're getting the values that they are, it's import to understand exactly what types are in Javascript.

## Types in Javascript

In Javascript, types can be broken up into 2 different categories: **Primitives** and **Objects**.

**Primitives** are anything that is immutable (i.e. their values can't be changed) and include the following:
 + Boolean
 + Null
 + Number
 + String
 + Symbol<sup><sup>**_6_**</sup></sup>
 + Undefined

Note that, unlike many other languages, strings are immutable in Javascript.

All other datatypes fall under the category of **Object** and, as we'll soon see, so do the primitives. This includes, but isn't limited to, the following built in datatypes (this list is actually fairly extensive and a full version of it can be found [on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)):

 + Object
 + Function
 + Boolean
 + Symbol<sup><sup>**_6_**</sup></sup>
 + Error
 + Number
 + Math
 + Date
 + String
 + RegExp
 + Array
 + Promise

#### The Primitive Datatypes

You'll notice that Some of these built in objects are the same as the primitive datatypes. This is because a primitive datatype can only be a value. It can't have any methods defined for it, or have any properties. Therefore, in order to allow you to do things like:
```javascript
var string = 'this is a string'.split(' ')
```
Javascript has built in String object as well as the string primitive datatype. Unfortunately, this leads to some issues when type checking strings, booleans, and numbers. Here is an example of how this can cause trouble:

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
// -> S{0: "t", 1: "h", 2: "i", 3: "s", 4: " ", 5: "i", 6: "s", 7: " ", 8: "a", 9: " ", 10: "s", 11: "t", 12: "r", 13: "i", 14: "n", 15: "g", 16: " ", 17: "t", 18: "o", 19: "o", length: 20, [[PrimitiveValue]]: "this is a string too"}

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

The difference between the 2 syntaxes for creating strings becomes clear when comparing the `typeof` and `instanceof` operators, along with the return values of the actual variables. The reason that the lines involving `.` calls all returned the same thing for both variables is because, in order to call a method or access a property, the primitive strings are converted into objects. This same process happens for the `Number` and `Boolean` objects and there corresponding primitive datatypes.

There are also 2 other unintuitive characteristics of the Javascript type system. In addition to the Number primitive type, the `NaN` and `Infinity` properties are defined on the global object (read more about the global object [on MDN](https://developer.mozilla.org/en-US/docs/Glossary/Global_object).) These 2 properties both are identified as numbers, despite any implication that they might not be. The reason for this has to do with memory. From [javascriptrefined.io]():
> As Wikipedia states:
> > Computer arithmetic cannot directly operate on real numbers, but only on a finite subset of rational numbers, limited by the number of bits used to store them.

>In ordinary arithmetic, `3.2317006071311 * 10 ** 616` is a real finite number, but, by ECMAScript standards, it is simply too large (i.e, considerably greater than `Number.MAX_VALUE`), and is therefore represented as `Infinity`. Attempting to divide an infinity by an infinity yields `NaN`.

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

This should look fairly similar to the results of calling these same methods and operators on the primitive string datatype above.

#### The Object Datatype

So now that we have a better understanding of what the primitive types are in Javascript, we need to look at the more complex ways of differentiating data. I mentioned earlier that everything else in javascript falls under the category of the **Object** datatype. **Objects** are Javascript's way of storing data in memory. This is fundamentally different from how primitive types work. Consider the following code:
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
In the first example, `x` and `y` are both pointing to the primitive value `3`. `x` is then reassigned to be `4`. Since `3` and `4` don't exist in the same "location" in memory, `x` and `y` are no longer pointing at the same value.

In the second example, `arrayX` and `arrayY` are both pointing to a specific `Array` object. That object has the properties `0`, `1`, and `2` (each with the value of their respective indexes) along with a property `length`, which holds the number of items in the array. Since `x` and `y` are both pointing to the same location in memory, if we change a property of that location, it changes the values of both `x` and `y`.

This is the basic usage of **Object** in Javascript. Despite the simplicity of what an object is, they are powerful tools which you can use to accomplish a wide variety of things. To continue the demonstration of identifying datatypes from earlier:

```javascript
var obj = {}

obj
// -> {}

Object.getPrototypeOf(obj)
// -> {__defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, __lookupSetter__: ƒ, …}

Object.prototype.toString.call(obj)
// -> "[object Object]"

obj.constructor
// -> ƒ Object() { [native code] }

typeof obj
// -> "object"

obj instanceof Object
// -> true
```

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
As you can see, functions are instances of both **Functions** _and_ **Objects**. In order to understand how and why that's the case, we need to explore **prototypes** and **constructors**.

## Prototypes In Javascript

You may have noticed in the code sample in this post I referenced the `.__proto__`, before showing that it was equivalent to calling `Object.getPrototypeOf(data)`.  That same property also showed up in a couple of places in the returns of those lines of code. Before I go into more detail about what that property is, I want to discourage against using the `.__proto__` property. The reason for this (as stated by [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto)) is:
> While Object.prototype.__proto__ is supported today in most browsers, its existence and exact behavior has only been standardized in the ECMAScript 2015 specification as a legacy feature to ensure compatibility for web browsers. For better support, it is recommended that only [Object.getPrototypeOf()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf) be used instead.

Now that we know we shouldn't use the `.__proto__` property, let's talk about what it is.

The reason that all of the non-primitive datatypes in Javascript are grouped together as **Objects** is because they all inherit from the **Object** prototype. What this means is that they all share the properties and methods that are defined on that prototype. But what is a prototype exactly? The simple answer is, it's a Javascript object that is used as a template. This is easier to see in action, so lets create our own prototype object. The easiest way to do this is with a constructor function.

```javascript
function myConstructorFn(sample, data, goes, here) {
  this.sample = sample
  this.data = data
  this.goes = function(where) {
    this.location = goes(where)
  }
  this.location = here
}

myConstructorFn
/* -> ƒ myConstructorFn(sample, data, goes, here) {
 *      this.sample = sample
 *      this.data = data
 *      this.goes = function(where) {
 *        this.location = goes(where)
 *      }
 *      this.location = here
 *    }    */

myConstructorFn.prototype
// -> {constructor: ƒ}

Object.getPrototypeOf(myConstructorFn)
// -> ƒ () { [native code] }
```
All that we are doing in the above code is defining a function called `myConstructorFn`. Before we look at what it's doing, notice that it has a property on it (since functions are objects, they can have properties) called `prototype`. This is different than the `__proto__` property, which you can see when I call `Object.getPrototypeOf(myConstructorFn)`. This is a property that is inherited from the **Function** prototype. All functions have this property (even if they're not used as constructor functions) and they should all return a similar object, assuming you haven't changed that object yet. This property is, as it's name implies, a prototype object. As a side note, the value of the `constructor` property of that prototype object is the function itself, so it's somewhat circular (i.e. `myConstructorFn.prototype.constructor` is the same as `myConstructorFn`.) We can now use this object to create more objects that behave the same way. Here's how to do this:

```javascript
function Painting(title, artist, period, dimensions) {
  var periods = [
    'Renaissance',
    'Renaissance to Neoclassicism',
    'Romanticism',
    'Romanticism to Modern',
    'Modern',
    'Contemporary'
  ]

  if(periods.indexOf(period) !== -1) {
    this.period = period
  }

  if(Array.isArray(dimensions)) {
    this.height = dimensions[0]
    this.width = dimensions[1]
    this.area = this.height * this.width
  }
  this.artist = artist
  this.title = title
}

var laVie = new Painting('La Vie', 'Pablo Picasso', 'Modern', ['6 ft', '4 ft'])

laVie
// -> Painting {period: "Modern", height: "6 ft", width: "4 ft", area: NaN, artist: "Pablo Picasso", …}

Object.getPrototypeOf(laVie)
// -> {constructor: ƒ}

Object.prototype.toString.call(laVie)
// -> "[object Object]"

laVie.constructor
/* -> ƒ Painting(title, artist, period, dimensions) {
 *      var periods = [
 *        'Renaissance',
 *        'Renaissance to Neoclassicism',
 *        'Romanticism',
 *        'Romanticism to Modern',
 *        'Modern',
 *        'Contemporary'…    */

typeof laVie
// -> "object"

laVie instanceof Painting
// -> true

laVie instanceof Function
// -> false

laVie instanceof Object
// -> true
```

Now that we've seen how to make prototypes (by writing their constructor functions), let's look at how to edit them. In the above code, you'll notice that the object that gets created from the Painting prototype has all of the properties we defined in the constructor, but the prototype just has a constructor property. Because of this, we could accidentally define global variables if we aren't careful with how we call our constructor function.
```javascript
function Side(length) {
  this.length = length
}

Side(3)  // If we call the Side function, 'this' will refer to the global object.

length
// -> 3
```
One way around this is to throw an error in our constructor function:
```javascript
function Side(length) {
  if(!(this instanceof Side)) {
    throw new Error('Side must be called with the "new" operator')
  }
  this.length = length
}

Side(3)  // If we call the Side function, it will throw an error
```
Or, if we want to be able to call it as a function, and still return a new Side object:
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
This may make you wonder why we don't just use our constructor functions to return objects like so:
```javascript
function Side(length) {
  return {
    length: length
  }
}

Side(3)
// -> {length: 3}
```

The main reason for this is to allow us to use OOP techniques. While the term _prototypal inheritance_ is commonly accepted and used throughout the Javascript community, inheritance isn't really the write word. Instead, it's more appropriate to think of it as **behavior delegation**. Consider the following code:
```javascript
function Shape(numSides) {
  this.sides = numSides
}

function Triangle(sides, angles) {
  this.sideAB = sides[0]
  this.sideBC = sides[1]
  this.sideCA = sides[2]
  this.angleCAB = angles[0]
  this.angleABC = angles[1]
  this.angleBCA = angles[2]
}

Triangle.prototype = new Shape(3)
Triangle.prototype.constructor = Triangle
Triangle.areaFunction = '1/2 * base * height'

function EquilateralTriangle(sideLength) {
  var sides = [sideLength, sideLength, sideLength]
  var angles = [60, 60, 60]
  Triangle.call(this, sides, angles)
}

EquilateralTriangle.prototype = new Triangle([],[60, 60, 60])
EquilateralTriangle.constructor = EquilateralTriangle

var tri = new EquilateralTriangle(2)

tri
// -> EquilateralTriangle {sideAB: 2, sideBC: 2, sideCA: 2, angleCAB: 60, angleABC: 60, …}

// The full tri object looks like this:
{
  angleABC: 60,
  angleBCA: 60,
  angleCAB: 60,
  sideAB: 2,
  sideBC: 2,
  sideCA: 2,
  __proto__: Triangle
}
```
Okay, this is a lot to take in. Before I go over the types of everything, let's see what's actually going on in the code.

 1. First, I create a function called `Shape` that takes in a number of sides. I'll be using this as a constructor function later.  (Note that this is also an object)
 2. Next, I create a function called `Triangle` that takes in 3 lengths and 3 angles as arrays.  (Note that this is also an object)
 3. I then set the prototype of the `Triangle` object using the predefined `prototype` property to be an object made from calling the `Shape` function. Since triangles all have 3 sides, I make sure to pass that into the `Shape` function.
 4. Next, I set the constructor back to the `Triangle` function.
 5. After that, I create a new property on the `Triangle` object called `areaFunction`.
 6. I then create a function called `EquilateralTriangle`.  (Note that this is also an object)
 7. Inside that function, I use `Triangle.call` to make sure that the `Triangle` function gets run so all of the sides have values assigned to them.
 8. I then set the prototype of the `EquilateralTriangle` object to be an object made from the `Triangle` function.
 9. Finally, I set the constructor back to the `EquilateralTriangle` function.

Okay, let's pause here and think about what some of these steps mean. The first 2 steps should make sense, so let's start with the third step.

> 3. I then set the prototype of the `Triangle` function using the predefined `prototype` property to be an object made from the `Shape` function. Since triangles all have 3 sides, I make sure to pass that into the `Shape` function.

The `prototype` property on **Function** objects is what is used to set the `__proto__` property of any object created when calling the `new` operator on a function. This creates a chain, referred to as the _prototype chain_, of objects. For the Shape, Triangle, EquilateralTriangle example, the prototype chain will look like this:
```javascript
tri {
  __proto__: [[EquilateralTriangle]] {
    __proto__: [[Triangle]] {
      __proto__: [[Shape]] {
        __proto__: [[Object]] {
          __proto__: null
        }
      }
    }
  }
}
```
This chain is used by Javascript to find any functions and properties that you try to access on an object. For example, if I were to try to access the `sides` property of the `tri` object, it would return `3`. However, if you look back up at the code block, you can see that there is no property called `sides` defined. This is the **behavior delegation** I was referring to earlier.

If a property or method doesn't exist on an object that you are calling it on, that object **_delegates_** that **_behavior_** to the next object down the chain. This is where _prototypal inheritance_ is different from _classical inheritance_ and why the usage of the word inheritance seems confusing to me. The `tri` object doesn't have the property `sides` (i.e. it didn't inherit it). Instead, the behavior get's delegated down the prototype chain until an object with that property is found. If no object with that property is found (i.e. `null` is reached on the prototype chain), than `undefined` is returned instead.

When we set the `prototype` property of the `Triangle` function, we are telling Javascript that if it's reached this point in the prototype chain and still can't find the property it's looking for, the next place it should look is in the object we made from calling new on the `Shape` function. Another way to think of this is that any object made by calling new on the `Triangle` function will have the same object as their `__proto__` property. This is still just simple Javascript objects that we are dealing with. Here is a similar situation:
```javascript
var animal = {eats: 'living matter'}

var mammal = {hair: true}
mammal.kingdom = animal

var carnivore = {/* ... */}
carnivore.class = mammal

var canine = {order: carnivore, /* ... */}

var feline = {order: carnivore, /* ... */}

var rodent = {/* ... */}
rodent.class = mammal
```
In this example, assigning the `kingdom` property of the `mammal` object, or the `class` property of the `carnivore` and `rodent` objects is functionally equivalent to assigning the `prototype` property on the `Triangle` function (which is also an object).
