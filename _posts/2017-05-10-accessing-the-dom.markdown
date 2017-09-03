---
layout: post
title:  "Accessing the DOM"
date:   2017-05-10 08:00:00 -0400
categories: HTML, Javascript
---
# The DOM
The Document Object Model (or DOM) is a browser's way of representing the content that is rendered on web page. Its main use is providing a way that the web page can be accessed and altered through the programs we write. It represents the structure of the web page using the following many data types, including the following:
  + window
  + document
  + element
  + nodeList
  + text

## window
The **window** object is the root of the web page. It is the first thing that gets loaded when you visit a page and will contain the **document** object. Other than the **document** object, the **window** object is also responsible for keeping track of what is displayed on the screen (i.e. the height/width of the window, or how far the window has been scrolled in the x or y direction). This is also the base context in which all of your functions are loaded and defined. If you were to `console.log(this)` in a function you should see `Window {stop: function, open: function, alert: function, confirm: function, prompt: function…}`

## document
The **document** object represents the parent node for all of the other nodes. Any node can be accessed through the **document** node (with the exception of iframes since those are technically separate **window** objects). If you `console.log(document)` you should see something like this:
```html
#document
  <!DOCTYPE html>
  <html>
  <head>...</head>
  <body>...</body>
  </html>
```
You can see that this is the parent for all other nodes, with a simple recursive function:
##### HTML
```html
<!DOCTYPE html>
<html>
<head>...</head>
<body>
  <ul>
    <li>This is a list item
    </li>
  </ul>
</body>
</html>
```
##### Javascript
```js
let li = document.getElementsByTagName("LI")[0]
function findParentNode(element) {
  if (element.parentNode) {
    return findParentNode( element.parentNode )
  }
  else {
    return element
  }
}

findParentNode(li)
```

this will output the document node as shown above.
## element
**element** nodes are the thing that most of us think of when we think of HTML nodes. Each **element** node will represent a single HTML element in the DOM. These are what get returned with the `getElementById()` function or other functions that select only a single **element**. These have a variety of useful properties like:
  + `attributes`
  + `classList`
  + `className`
  + `clientHeight`
  + `clientWidth`
  + `id`
  + `innerHTML`
  + `outerHTML`
  + `tagName`

Along with a large number of methods.

## nodeList
The **nodeList** object is similar to an array of different node objects (e.g. **element** nodes.) This is what gets returned when you use the `getElementsByTagName()` or `querySelectorAll()` functions. These have some, but not all of the iterator functions for the normal javascript array objects.

## text
The **text** node object is just a string that represents the text inside an element node.

# Traversing the DOM
Knowing the way the DOM is structured only useful if we can also traverse it. The most generalized method to do this with (in vanilla JS) is `querySelectorAll()`. This function takes in a CSS selector and returns all elements (in a **nodeList**) that match that selector. This gives us the full power of CSS selectors in selecting **element** nodes.

## CSS selectors
There are only three **base CSS selectors**:
  + element
  + class
  + id
They are used in the following way:

```js
// element selector
document.querySelectorAll('p') // is equivalent to
document.getElementsByTagName('p')

// class selector
document.querySelectorAll('.my-class')

// id selector
document.querySelectorAll('#my-id')
```

For element selectors, you just use the tag name (e.g. p, li, body, div), for class selectors you append the class with a `.`, and for id selectors you append the id with a `#`. You can also chain these together to further narrow down your selector. For example, `p.my-class` will only select `p` elements with the class `my-class`.

#### Combinators
Our first step towards being more specific with our CSS selectors is the introduction of **combinators**. **Combinators** are the way you show a relationship between two **base CSS selectors**.

There are only four different **combinators** and they are used in the following ways:

```js
// descendant combinator
document.querySelectorAll('div li')
```

This selects all `li` elements that are within a `div` element, regardless of how deeply nested they are.

```js
// child combinator
document.querySelectorAll('div > ul')
```

This selects all `ul` elements that have a `div` element as its direct parent.

```js
// adjacent sibling combinator
document.querySelectorAll('li.my-class + li')
```

This will select all `li` elements that come immediately after the `li` elements with the class `my-class`.

```js
// general sibling combinator
document.querySelectorAll('li#my-id ~ li')
```

This will select all `li` elements that come after the `li` element with the id `my-id`.

**NOTE:** _For the adjacent sibling **combinator** and the general sibling **combinator**, these will only select elements that exist below the first specified element in the relationship (i.e. they follow the first element in the DOM)_

#### Psuedo-Classes
The next way we have of being more specific is with **psuedo-classes**. These allow us to define what state the the elements are in. All **psuedo-classes** are indicated by a colon, followed by the name of the **psuedo-class**. Some useful **psuedo-classes** include:
  + `:empty`
  + `:first-child`
  + `:first-of-type`
  + `:focus`
  + `:hover`
  + `:nth-child(n)`
  + `:only-of-type`

A full list of **psuedo-classes** can be found here: [MDN psuedo-classes](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes)

Be careful using the **psuedo-classes** that involve temporary element states like `button:hover` or `input:focus`. If you want to do something when these happen, it is better to use an event listener like:

```js
document.querySelectorAll('button').addEventListener('onmouseover', callback)
document.querySelectorAll('input').addEventListener('onfocus', callback)
```

#### Psuedo-Elements
**Psuedo-elements** allow us to style specific parts of an element. You likely won't be creating any **psuedo-elements** with Javascript, but accessing them is still important. **Psuedo-elements** are indicated by a double colon, followed by the name of the **psuedo-element**. The most common ones include:

  + `::after`
  + `::before`

The full list can be found here: [MDN psuedo-elements](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements).

Just like with **psuedo-classes**, you should avoid using the **psuedo-elements** that involve temporary states like `p::selection`.

## Attribute Selectors
The last method we have for specifying our CSS selectors is the **attribute selector**. These allow us to specify that we only want elements with a certain attribute. The syntax for the **attribute selector** is `[attribute]` where _attribute_ is the name of the attribute you want to specify. For example:
```js
document.querySelectorAll('a[target]')
```

This will select all `a` elements with a `target` attribute (e.g. `<a target="_blank">link</a>`.) We also have the ability to specify what the attributes value should be in a variety of different ways:

```js
document.querySelectorAll('a[target="_blank"]')
```

This will select all `a` elements with the `target` attribute equal to `_blank`.

```js
document.querySelectorAll('[title~="flower"]')
```

This will select all elements with a `title` attribute that contains the word 'flower'.

```js
document.querySelectorAll('[class|="top"]')
```

This will select all elements with a class that starts with the word 'top'.

```js
document.querySelectorAll('[class^="top"]')
```
This will select all elements with a class that starts with the characters 'top'.

```js
document.querySelectorAll('[class$="test"]')
```

This will select all elements with a class that ends with the characters 'test'.

```js
document.querySelectorAll('[class*="te"]')
```
This will select all elements with a class that contains the characters 'te'.

With the use of these selectors and the **element** node properties like `parentNode`, it is possible to select any **element** from any other element. In practice, this is referred to as **traversing the DOM**.
