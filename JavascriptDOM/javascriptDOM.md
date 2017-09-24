# JavaScript DOM Manipulation

My notes from a 4 part crash course on the DOM and its manipulation using JS (*Traversy Media Youtube channel*). It also includes additional information based on MDN and StackOverFlow resources.

[Link to the Playlist on Youtube](https://www.youtube.com/watch?v=0ik6X4DJKCc&list=PLillGF-RfqbYE6Ik_EuXA2iZFcE082B3s)

Missing content from the above listed course has been covered by the following ones:
- [NetNinja JS DOM Playlist](https://www.youtube.com/playlist?list=PL4cUxeGkcC9gfoKa5la9dsdCNpuey2s-V)
- [The Modern JavaScript Tutorial - Part 2: Browser: Document, DOM, Interfaces](http://javascript.info/)

## Examining the `document` object

```javascript=
console.dir(document); // View inside the object (its methods & props)
console.log(document); // View the inner HTML
console.log(document.domain);  // Domain name of the site/page
console.log(document.URL); // Entire URL of the page
console.log(document.doctype); // Ex: '<!DOCTYPE html>'

console.log(document.title); // Prints the title of the page (whatever is present inside <title> tags)
// If <title> changes then document.title will reflect the change too.
```

#### **1.** `document` properties are **NOT** read-only. They can be changed.
```javascript=
document.title = 123;
console.log(document.title); // 123 (& text inside <title> also changes)
```

#### **2.** `document` has reference to `head` and `body`:
```javascript=
console.log(document.head);
console.log(document.body);
```

#### **3.** Just like other elements, it has event listeners:
```javascript=
document.onkeypress = function() { 
    console.log(1); // Logs 1 every time a key is pressed
};
```

#### **4.** `document.all` is an *HTML collection* (array-like structure). It contains a reference to all the elements on the page in the order they are written
```javascript=
console.log(document.all);
console.log(document.all[10]); // Ex: <h1 class="hdr"><h1>
document.all[10].
    = 'Hello'; // Again, it is NOT read-only
console.log(document.all[10]); // Ex: <h1 class="hdr">Hello<h1>
```
Any element added to the DOM gets referenced in `document.all`. It is **not** a good practice to use `document.all` to fetch or edit elements since their positions (indices) will change when elements are added or deleted.

#### **5.** Fetching *forms*, *images*, *scripts*, *links*, etc from `document`. Returns an **HTML Collection** (array-like structure).
```
console.log(document.forms); // gets all the forms in the order they appear
console.log(document.scripts); // gets all the scripts in the order they appear
console.log(document.links); // gets all the links in the order they appear
console.log(document.images); // gets all the images in the order they appear
```

## JavaScript Selectors and Their Properties & Methods (Fetching DOM Elements)

#### **1.** Fetch Element by ID (`getElementById(<id>)`):
```javascript=
var headerTitle = document.getElementById('header-title');
console.log(headerTitle); // <h1 id="header-title">Item Lister <span>123</span></h1>
console.log(headerTitle.textContent); // "Item Lister 123"
console.log(headerTitle.innerText); // "Item Lister"
headerTitle.innerHTML = '<h3>Hello</h3>';
console.log(headerTitle); // <h1 id="header-title"><h3>Hello</h3></h1>
```

Difference between `textContent` and `innerText`: The former will output the text of all the child elements also (concatenated) but the latter outputs only the text of the outer (selected) element.

[Differences between `textContent` and `innerText`](https://stackoverflow.com/questions/24427621/innertext-vs-innerhtml-vs-label-vs-text-vs-textcontent-vs-outertext)

`innerHTML` replaces the HTML *inside* the selected element with what is supplied. `outerHTML` replaces the selected element itself (the whole element).

`textContent`, `innerText`, `innerHTML`, and `outerHTML` act as both *setters* and *getters*. If we supply a value, it sets it. If we do not assign but use/return these properties, we will have access to their values.

**Note:** **Changing Styles (CSS)**. We use the `style` property on the DOM element(s) (selected). We need to use *camel case* for the CSS properties (since '-' cannot be used within property names). The styles are added to the **style attribute** of the element(s).
```javascript=
// syntax: <selectedElement(s)>.style.<cssPropertyInCamelCase> = '<property rule(RHS)>';

var header = document.getElementById('main-header');
header.style.borderBottom = '1px solid #0d426c';
```

#### **2.** Fetch Elements by Class (`getElementsByClassName(<className>)`):
```javascript=
var items = document.getElementsByClassName('list-group-item'); // Fetches all '.list-group-item' elements
console.log(items); // lists all the matched DOM elements (as an HTMLCollection)
console.log(items[1]); // Lists the second DOM element that was matched (0 based index)

items[1].style.backgroundColor = '#FF0'; // Change background color of 2nd matched item to '#FF0'
items[2].textContent = 'I am third on the list'; // Changes text of the 3rd matched element
console.log(items[2].textContent); // 'I am the third on the list'

items.style.backgroundColor = '#C00'; // Error! 'items' is an HTMLCollection of DOM elements (not DOM itself)
```

**Note:** 
1. DOM element properties such as `textContent`, `innerText`, `innerHTML`, `outerHTML` etc can be used upon *any selected DOM element*, it does not matter if it was selected using id/class/any other method.
2. **Adding/Fetching common properties from all elements of an HTMLCollection**: We have to loop through the elements of the collection (collection comes with a `length` property). Every index contains a DOM element upon which we can work (Useful for methods that return a collection of DOM elements, such as `getElementsByClassName`).
```javascript=
var items = document.getElementsByClassName('list-group-item'); 
for(var i = 0; i < items.length; i++) {
	items[i].style.fontStyle = 'italic';
}
```

#### **3.** Fetch Elements by Tag Name (`getElementsByTagName(<tagName>)`): 

It is very similar to `getElementsByClassName` but it selects all elements of a certain type (i.e by html tags).

```javascript=
var li = document.getElementsByTagName('li'); // Fetches all 'li' tag elements
console.log(li); // lists all the matched DOM list elements (as an HTMLCollection)
console.log(li[1]); // Lists the second DOM list element that was matched (0 based index)

li[1].style.backgroundColor = '#FF0'; // Change background color of 2nd matched list item to '#FF0'
li[2].textContent = 'I am third on the list'; // Changes text of the 3rd matched list element
console.log(li[2].textContent); // 'I am the third on the list'

// li.style.backgroundColor = '#C00'; // Error! 'items' is an HTMLCollection of DOM elements (not DOM itself)

// Applying/fetching common properties/methods on all list items:
var li = document.getElementsByClassName('li'); 
for(var i = 0; i < li.length; i++) {
	li[i].style.fontStyle = 'italic';
}
```

#### **4.** Fetch Elements by Using Query Selector (`querySelector(<css-Selector>)`): 

`querySelector()` is very similar to jquery's `$()` or `jquery()`. That is, it takes in CSS selectors as input ('#', '.', '\[\]', etc). The difference between this and `$()` is that `$()` selects all the matching elements (like an HTMLCollection) but `querySelector()` matches **only one element (first match)**. Therefore, `querySelector()` returns a **DOM element**(not HTMLCollection), similar to `getElementById`.

```javascript=
var header = document.querySelector('#main-header');
header.style.color = '#0d426c';

var input = document.querySelector('input'); // Will select only the 1st input (even if there are more)
input.value = 'Hello World'; // We 
```

**Note:** **Accessing Attributes of DOM Elements (set & get):**
We can access attributes of a DOM element with the attribute name (using the 'dot' reference). Syntax: `<DOMElement>.<Attribute>` (get) and `<DOMElement>.<Attribute> = <Value>;` (set).

```javascript=
var submit = document.querySelector('input[type="submit"]'); // selects only the submit input (if there is only one)
submit.value = 'SEND';
```

We can use **Pseudo Selectors** to match the exact element with `querySelector()` (Not just the first match):
```javascript=
var item = document.querySelector('.list-group-item'); // gets only the first item
item.style.color = 'red'; // changes 

// Pseudo selectors last-child, first-child and nth-child(n):
var lastItem = document.querySelector('.list-group-item:last-child');
lastItem.style.color = 'blue'; 

var secondItem = document.querySelector('.list-group-item:nth-child(2)');
secondItem.style.color = 'green';
```

#### **5.** Fetch All Elements by Using Query Selector All (`querySelectorAll(<css-Selector>)`): 

This, again, is similar to `$()` and `querySelector()`. The difference is that it returns a **list** of matched elements (Not just the first match like in `querySelector()`). But, it is not the same as `$()` since it returns a **`NodeList`**. The NodeList is an array (not HTMLCollection) because we can invoke array methods like `reverse()` on it (not just length property).

- `$()`: Returns `Object`
- `querySelector()`: Returns a DOM element (first match)
- `getElementById()`: Returns a DOM element
- `getElementsByClassName()` & `getElementsByTagName()`: Returns `HTMLCollection` object(array-like).
- `querySelectorAll()`: Returns a `NodeList` (An Array).

```javascript=
var titles = document.querySelectorAll('.title');
console.log(titles); // logs a NodeList containing all the matched DOM elements
titles[0].textContent = 'Hello';
```

A more useful example:
```javascript=
var odd = document.querySelectorAll('li:nth-child(odd)');
var even = document.querySelectorAll('li:nth-child(even)');
for(var i = 0; i < odd.length; i++) {
	odd[i].style.backgroundColor = '#dfe1e8';
	even[i].style.backgroundColor = '#ccc';
}
```

## Traversing the DOM

Sample HTML for the examples that follow:
```htmlmixed=
<body>
  <header id="main-header" class="bg-success text-white p-4 mb-3">
    <div class="container">
      <h1 id="header-title">Item Lister <span style="display:none">123</span></h1>
    </div>
  </header>
  <div class="container">
   <div id="main" class="card card-body">
    <h2 class="title">Add Items</h2>
    <form class="form-inline mb-3">
      <input type="text" class="form-control mr-2">
      <input type="submit" class="btn btn-dark" value="Submit">
    </form>
    <h2 class="title">Items</h2>
    <ul id="items" class="list-group">
      <li class="list-group-item">Item 1</li>
      <li class="list-group-item">Item 2</li>
      <li class="list-group-item">Item 3</li>
      <li class="list-group-item">Item 4</li>
    </ul>
    <span></span>
   </div>
  </div>

  <script src="dom.js"></script>
</body>
```

#### **1.** Fetching the Parent Node of an Element (`parentNode`):

We can use the `parentNode` property that comes with every element. Also, this property can be **chained** in order to fetch the ancestors.

```javascript=
var itemsList = document.querySelector('#items');
console.log(itemsList.parentNode); // parent of '#items' element is '#main' element

itemsList.parentNode.style.backgroundColor = '#f4f4f4'; // parent node is a DOM too!

itemsList.parentNode.parentNode; // returns '.container' DOM element
itemsList.parentNode.parentNode.parentNode; // returns 'body' DOM element
```

Alternative to `parentNode`: **`parentElement`**

`parentElement` is new to Firefox 9 and to DOM4, but it has been present in all other major browsers for ages.

In most cases, it is the **same** as `parentNode`:
```javascript=
var itemsList = document.querySelector('#items');
console.log(itemsList.parentElement); // parent of '#items' element is '#main' element

itemsList.parentElement.style.backgroundColor = '#f4f4f4'; // parent node is a DOM too!

itemsList.parentElement.parentElement; // returns '.container' DOM element
itemsList.parentElement.parentElement.parentElement; // returns 'body' DOM element
```

The only **difference** comes when a node's parentNode is **not an element**. If so, `parentElement` is `null`:
```javascript=
document.body.parentNode; // the <html> element
document.body.parentElement; // the <html> element

document.documentElement.parentNode; // the document node
document.documentElement.parentElement; // null
```

Since the `<html>` element (`document.documentElement`) doesn't have a parent that is an element, `parentElement` is `null`. (There are other, more unlikely, cases where `parentElement` could be `null`, but you'll probably never come across them.)

**Use `parentNode` for all practical purposes.**

#### **2.** Fetching the Child Nodes of an Element (`childNodes`):

`childNodes` property of an element returns a list of all the child nodes (as a `NodeList`) in the order they appear.
```javascript=
var itemsList = document.querySelector('#items');
console.log(itemsList.childNodes); // all child nodes including text nodes.
// Ex: (9) [text, li.list-group-item, text, li.list-group-item, text, li.list-group-item, text, li.list-group-item, text]
```

The **problem** with `childNodes` is that it will return even text nodes. For example, if there is space (or newline or tab) between child elements, they are also returned as an element of the list.

Alternative to `childNodes`: **`children`**

`children` returns only the HTML elements and **not** text nodes. The difference, however, is that it is an `HTMLCollection` (and not a `NodeList`).

```javascript=
var itemsList = document.querySelector('#items');
console.log(itemsList.children); // html collection of all child elements (excluding text nodes)
console.log(itemsList.children[1]); // log the second child
itemsList.children[1].style.backgroundColor = 'yellow'; // every childElement is a DOM element as well.
```

**Use `children` for all practical purposes.**

**Fetching specific child nodes:**
1. Fetching the First Child Node:
    - Use `firstChild` but this will again return a text node (say, space/newline) if it is the first child. 
    - Therefore, use `firstElementChild` property instead (Will always match an element node and not text).
```javascript=
var itemsList = document.querySelector('#items');
console.log(itemsList.firstChild); // '> #text'
console.log(itemsList.firstElementChild); // logs the first child of '#items'
itemsList.firstElementChild.style.backgroundColor = 'teal'; // first element child is a DOM element too!
```

2. Fetching the Last Child Node:
    - Use `lastChild` but this will again return a text node (say, space/newline) if it is the last child. 
    - Therefore, use `lastElementChild` property instead (Will always match an element node and not text).
```javascript=
var itemsList = document.querySelector('#items');
console.log(itemsList.lastChild); // '> #text' (the last text before tag close)
console.log(itemsList.lastElementChild); // logs the first child of '#items'
itemsList.lastElementChild.style.backgroundColor = 'red'; // last element child is a DOM element too!
```

#### **3.** Fetching Sibling DOM Elements

1. To fetch the next sibling, use `nextSibling` but once again, it has a drawback - matches text nodes also. Therefore, use `nextElementSibling` instead!
2. To fetch the previous sibling, use `previousSibling` but again, it has a drawback - matches text nodes also. Therefore, use `previousElementSibling` instead!

```javascript=
var itemsList = document.querySelector('#items');
console.log(itemsList.nextSibling); // '> #text'
console.log(itemsList.nextElementSibling); // logs '<span></span>' DOM element
console.log(itemsList.previousSibling); // '> #text'
console.log(itemsList.previousElementSibling); // logs '<h2 class="title">Items</h2>' DOM element
itemsList.previousElementSibling.style.backgroundColor = '#f4f4f4'; // nextElementSibling & previousElementSibling are DOM elements too!
```

## Creating New Elements and Inserting into DOM

#### **1.** Creating an element node `document.createElement()`:

- Use `document.createElement()` method. It creates the HTML element specified by tagName.
- Set `class` name and `id` to created nodes with `className` and `id` properties (get/set).

```javascript=
var newDiv = document.createElement('div');
console.log(newDiv); // <div></div>

newDiv.className = 'header-info';
console.log(newDiv); // <div class="header-info"></div>

newDiv.id = 'subheader';
console.log(newDiv); // <div class"header-info" id="subheader"></div>
```

#### **2.** Setting and Getting Attributes:

We can set and get attributes for any DOM element using `setAttribute(<attr-name>, <attr-value>)` and `getAttribute(<attr-name>)`.

```javascript=
newDiv.setAttribute('title', 'List of items');
console.log(newDiv); // <div class"header-info" id="subheader" title="List of items"></div>
newDiv.getAttribute('title'); // "List of items"
```

Two other ways to set and get attributes:
```javascript=
// 1. Dot method: Only if property name does not contain dashes/spaces/etc.
newDiv.customAttr = 'Hi';
console.log(newDiv); // Does not show customAttr in the log (Since customAttr is a "property" (not attr.))
console.log(newDiv.customAttr); // "Hi"

// 2. Square Bracket Notation: Works in any case:
newDiv['newAttr'] = 'bye';
console.log(newDiv); // Does not show newAttr in the log (Since customAttr is a "property" (not attr.))
console.log(newDiv['newAttr']); // "bye"
```

**Note:** 
1. To set the`class` of the element, you have to use `className` as attribute name instead.
2. Properties vs Attributes: **Attributes are defined by HTML**. **Properties are defined by DOM**. Some HTML attributes have 1:1 mapping onto properties. `id` is one example of such. But, custom attributes that we add are actually "properties". Hence, `customAttr` and `newAttr` above were properties.

#### **3.** Creating Text Nodes:

Use `document.createTextNode(<text>)`.

```javascript=
var text = document.createTextNode('Listing items');
text.textContent; // Listing items
```

#### **4.** Appending Nodes to an Element (`appendChild()`):
```javascript=
newDiv.appendChild(text);
console.log(newDiv); // <div class"header-info" id="subheader">Listing items</div>
```
#### **5.** Inserting Newly Created DOM Element @ Specific Position (`<parent>.insertBefore()`):

Use `<parentElement>.insertBefore(<newelementtoinsert>, <existingnode>)`. It places the new element before the existing node within that parent.

```javascript=
var container = document.querySelector('header .container');
var h1 = document.querySelector('header h1');
container.insertBefore(newDiv, h1); // inserts newDiv inside 'header .container' and before 'header h1'.
```

**Note:**
1. There is no `Node.insertAfter()` DOM function in Javascript (jQuery has it).
2. `ParentNode.prepend(nodesToPrepend);` is like an alternative to appendChild(). **It is experimental, only available in new DOM versions (do not use it just yet)**.

#### **6.** Removing Child Nodes from DOM

We can remove a child node by calling `removeChild` on its parent node. (`appendChild()` & `removeChild()` can be used together)

```javascript=
var mainHeader = document.querySelector('#main-header .container'),
	headerTitle = document.getElementById('header-title');
mainHeader.removeChild(headerTitle);
```
The child element must be a **direct child** of the parent node.

## Adding Event Listeners

Handling click, hover, blur, etc events on DOM elements.

#### **1.** Old way of adding event listeners (still works):

We can have an attribute, say `onclick`, on the DOM element and pass it either some JS expression to execute or a JS function to handle it.
```htmlmixed=
<button id="button" onclick="hey()">Click me<button> <!-- calls some JS function 'hey' when clicked -->
<button id="new-button" onclick="alert(1)">Annoy yourself<button> <!-- Alerts 1 every time you click -->
```

#### **2.** Using `addEventListener` method:

We want to keep markup and functionality separate (so method 1 should not be used, ideally). `addEventListener` is a method on an HTML element which accepts two parameters: (a) Event type (string) and (b) Callback function (reference to it). It can have an optional third parameter.

```javascript=
var button = document.getElementById('button');
button.addEventListener('click', function() { // Setting function expression as callback
    console.log('You clicked me!'); 
});

// We can also use the function handle/reference:
button.addEventListener('mouseover', hoverHandler); 
function hoverHandler() { 
    console.log('Try clicking me'); 
} 
```

**Note:**
Whenever an event occurs, an event object holds the details to it. This events object is *passed* to our callback function (listener) as the *first parameter*. We can name it anything we want but it's usually `e` or `event`. We can log it to see all its properties. 

One of the most important properties of the event object is `target` which holds the DOM element that caused the event to get triggered (target of the event).

```javascript=
// <button id="button" class="btn btn--s">Click me</button>
var button = document.getElementById('button');
button.addEventListener('click', function(e) { 
	console.log(e); // Ex: '> MouseEvent {isTrusted: true, clientX: 294, clientY: 365, …}
	console.log(e.target); // Ex: Logs the '#button' DOM element
    console.log(e.target.id); // "button" (target is just like any other DOM element!)
    console.log(e.target.className); // 'btn btn--s' (the whole string of class attribute)
});
```

**Note:** 
1. If you want to access the class names *"one-by-one"* then you should use the `classList` property of a DOM element. This returns an **array** where each element is a class name.
```javascript=
console.log(e.target.classList); // Ex: ["btn", "btn-dark", "btn-block", value: "btn btn-dark btn-block"]
```

List of all the events (reference): [MDN Page](https://developer.mozilla.org/en-US/docs/Web/Events)

**Note 1:** More event properties:
1. `e.type` will tell us what event occurred (Ex: 'click' for a `click` event).
2. `e.clientX` will give the mouse x-axis position for that event from the `window`.
3. `e.clientY` will give the mouse y-axis position for that event from the `window`.
4. `e.offsetX` will give the mouse x-axis position for that event from the current DOM element `e.target`.
5. `e.offsetY` will give the mouse y-axis position for that event from the current DOM element `e.target`.

```javascript=
var button = document.getElementById('button');
button.addEventListener('click', function(e) { 
	console.log(e.clientX, e.clientY); // Ex: 459 339
	console.log(e.offsetX, e.offsetY); // Ex: 352 -1
	console.log(e.type); // click
});
```

**Note 2:** Checking if `alt`, `ctrl` or `shift` keys were pressed during the event (Useful if we want to implement a different functionality for a click):
1. `e.altKey`: `true` if alt key was pressed during event.
2. `e.ctrlKey`: `true` if ctrl key was pressed during event.
3. `e.shiftKey`: `true` if shift key was pressed during event.
```javascript=
var button = document.getElementById('button');
button.addEventListener('click', function(e) { 
	console.log(e.altKey);
	console.log(e.ctrlKey);
	console.log(e.shiftKey);
});
```

**Note 3:** Mouse events:
1. `click` (Single click)
2. `dblclick` (Double click)
3. `mousedown` (Similar to click but triggered as soon as the mouse is pressed)
4. `mouseup` (Similar to click but triggered only when a pressed mouse is released)
5. `mouseenter` (Triggered when mouse enters target element - not triggered for entering child elements)
6. `mouseover` (Triggered when mouse enters target element - triggered for entering child elements also)
7. `mouseleave` (Triggered when mouse leaves target element - not triggered when leaving child elements)
8. `mouseout` (Triggered when mouse leaves target element - triggered when leaving child elements also)
9. `mousemove` (Triggered whenever the mouse moves inside the target element. So as long as we are moving the mouse inside the element, it keeps getting triggered. We can use `e.clientX`, `e.offsetY`, etc to get mouse position)

Check the differences with these examples:
```javascript=
// <div id="box" style="width: 500px; height: 300px">
//     <h1>Hello</h1>
// </box>
var box = document.getElementById('box');
box.addEventListener('mouseenter', logEvent); 
box.addEventListener('mouseover', logEvent); 
function logEvent(e) {
	console.log(e.type);
}
```

```javascript=
// <div id="box" style="width: 500px; height: 300px">
//     <h1>Hello</h1>
// </box>
var box = document.getElementById('box');
box.addEventListener('mouseleave', logEvent); 
box.addEventListener('mouseout', logEvent); 
function logEvent(e) {
	console.log(e.type);
}
```

`mousemove` event example:
```javascript=
var box = document.getElementById('box');
box.addEventListener('mousemove', changePageColor); 
function changePageColor(e) {
	console.log(e.type);
	console.log(e.offsetX, e.offsetY);
	document.body.style.backgroundColor = 'rgb(' + e.offsetX + ', ' + e.offsetY + ', 40)';
}
```

**Note 3:** Input & Keyboard events:

Input & Keyboard events usually go together since we type into the inputs and forms.

The keyboard events are as follows: 
1. `keydown` (Triggered when key is pressed down)
2. `keyup` (Triggered when pressed key is released)
3. `keypress` (Triggered once when key is pressed)

```javascript=
var input = document.querySelector('input[type="text"]');
input.addEventListener('keypress', logEvent);
function logEvent(e) {
	console.log(e.type, e.keyCode, String.fromCharCode(e.keyCode));
    if(String.fromCharCode(e.keyCode) === 'a') {
        console.log('You pressed "a"');
    }
}
```

`e.keyCode` will return a numeric value which is a map to a character.
If you want the ASCII character from the code, use `String.fromCharCode(e.keyCode)` (calling a function directly from the `String` prototype which converts numeric key to corresponding ASCII character.

[ASCII and Key Codes mapping](https://www.cambiaresearch.com/articles/15/javascript-char-codes-key-codes)

The input box events are as follows:
1. `focus` (Triggered when you click into the input - brings input into focus (if not already focused))
2. `blur` (Triggered when you click out of the input and it was in focus prior to it)
3. `change` (Triggered when value inside input changes(edited). Gets triggered when you change some text and click out of the input (i.e blur))
4. `cut` (Triggered when you cut some text from input)
5. `cut` (Triggered when you copy some text from input)
6. `paste` (Triggered when you paste something into the input)
7. `input` (Triggered whenever any of the above input events, except focus and blur, are triggered.

```javascript=
var input = document.querySelector('input[type="text"]');
input.addEventListener('focus', logEvent);
input.addEventListener('blur', logEvent);
function logEvent(e) {
	console.log(e.type);
}
```

```javascript=
var input = document.querySelector('input[type="text"]');
input.addEventListener('change', logEvent);
function logEvent(e) {
	console.log(e.type);
	console.log(e.target.value);
}
```

```javascript=
var input = document.querySelector('input[type="text"]');
input.addEventListener('cut', logEvent);
input.addEventListener('copy', logEvent);
input.addEventListener('paste', logEvent);
function logEvent(e) {
	console.log(e); // logged as ClipboardEvent
	console.log(e.type); // cut or copy or  paste

	var clipboardData = e.clipboardData || window.clipboardData; // window.clipboardData is like backup(?)
    console.log(clipboardData.getData('Text')); // works only with paste
}
```

```javascript=
// Another copy example:
document.addEventListener('copy', function(e){
    e.clipboardData.setData('text/plain', 'Hello, world!');
    e.clipboardData.setData('text/html', '<b>Hello, world!</b>');
    e.preventDefault(); // We want our data, not data from any selection, to be written to the clipboard
});
```

**Note 4:** Select Input (Dropdown) events:

The usual input events get triggered for a select (dropdown) element too (Whichever is applicable). The most common event that we will be needing is the `change` event. Whenever a different dropdown \<option\> is chosen from the \<select\>, the `change` event gets triggered. The selected value is accessible with `e.target.value`.

```javascript=
var select = document.querySelector('select');
select.addEventListener('change', logEvent);
function logEvent(e) {
	console.log(e.type, e.target.value); // logs: change <value-of-selected-input>

}
```

**Note 5:** Form Submit event:
Whenever a form submit happens (click on `input[type="submit"]` button), we can listen to it by attaching a handler to the `submit` event of that form.

A point to note is that the form's default action is to submit the form to the specified page (if page is the same, it reloads). We do not want this behavior in the submit event listener, especially if we are going to submit the form ourselves (via ajax).

Use `e.preventDefault()` to stop the default action from occurring (For form it is submit to specified page, for links it is to open the specified page, etc.)

```javascript=
var form = document.querySelector('form');
form.addEventListener('submit', logEvent);
function logEvent(e) {
	e.preventDefault(); // if this was not there, default behavior of form submit takes place
	console.log(e.type);
}
```

**`e.preventDefault()` vs `e.stopPropagation()` vs `return false;`**
1. `e.preventDefault()` – It stops the browsers default behaviour.
2. `event.stopPropagation()` – It prevents the event from propagating (or “bubbling up”) the DOM (To either unexecuted event listeners on the same DOM element or to the event listeners of the parent/ancestors (bubbling)).
3. `return false;` - Does both `e.preventDefault()` and `event.stopPropagation()`. It also stops current callback execution and returns immediately when called.

---

## `DOMContentLoaded` vs `jQuery.ready` vs `load`

The above are different page load triggers (`ready` is a jquery utility; listed for comparison).

MDN Definition of `DOMContentLoaded`:
> The DOMContentLoaded event is fired when the initial HTML document has been completely loaded and parsed, without waiting for stylesheets, images, and subframes to finish loading. A very different event load should be used only to detect a fully-loaded page. It is an incredibly popular mistake to use load where DOMContentLoaded would be much more appropriate, so be cautious.

Synchronous JavaScript **pauses** parsing of the DOM. Maybe we can place our script at the end of the body or use `async` or `defer` attributes. `jQuery(document).ready()`.

MDN Definition of `load`:
> The load event is fired when a resource and its dependent resources have finished loading.

`jQuery.ready` / `DOMContentLoaded` occurs when all of the HTML is ready to interact with, but often before its been rendered to the screen.

The `load` event occurs when all of the HTML is loaded, and any subresources like images are loaded.

[Codepen example](https://codepen.io/LukeAskew/pen/LnJsE)

Most Often, to work with HTML elements, we wait for the DOM to be parsed (HTML constructed) and then do all our computation:
```javascript=
window.addEventListener('DOMContentLoaded', function(){
    // Fetch , add, append, remove, add listeners, etc. 
});

// jQuery equivalent code:
jQuery.ready(function(){
  document.querySelector('#my-awesome-el').innerHTML = new Date
});
// OR
$(function(){
  document.querySelector('#my-awesome-el').innerHTML = new Date
});
```

Run When All Images And Other Resources Have Loaded:
```javascript=
window.addEventListener('load', function(){
  // Everything has loaded!
});
```

**Note:** `load` event need not be called only on the window (calling it on window makes sure to execute callback only after *all the resources have finished loading*). We can call it on a **specific element** as well (For example, we can call it on an \<img\> DOM element, waiting for it to load).
```javascript=
document.querySelector('img.my-image').addEventListener('load', function(){
  // The image is ready!
});
```

**Note:** Changes you make in your JavaScript code often don't actually render to the page immediately. In the interest of speed, the browser often waits until the *next event loop cycle* to render changes. Alternatively, it will wait until you request a property which will likely change after any pending renders happen. If you need that rendering to occur, you can either *wait* for the next event *loop tick* or *request a property* which is known to **trigger a render of anything pending**:
```javascript=
el.offsetHeight // Trigger render

// el will have rendered here
```

---
