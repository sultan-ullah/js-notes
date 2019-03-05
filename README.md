# JavaScript: Understanding the Weird Parts
## Table of Contents
- [Execution Contexts & Lexical Environments](https://github.com/sultan-ullah/js-notes#execution-contexts--lexical-environments)
- [Types & Operators](https://github.com/sultan-ullah/js-notes#types--operators)
- [Objects & Functions](https://github.com/sultan-ullah/js-notes#objects--functions)
- [Building Objects](https://github.com/sultan-ullah/js-notes#building-objects)

# Execution Contexts & Lexical Environments
- **Syntax Parser:** A program that reads code and determines what it does & if its grammer is valid  
- **Lexical Environment:** Where something sits physicall in the code  
- **Execution Context:** A wrapper to help manage the code that is running   
- **Name/Value Pair:** A name which maps to a unique value  
- **Object:** A collection of name value pairs (value can be another object)  
```
Address: 
  {
    Street: 'Main',
    Number: 100,
    Apartment:
    {
      Floor: 3,
      Number: 301
    }
  }
```
### Global Execution Context

The global execution context (created at the start of the script) contains 4 things:
1. **Global Object:** In the case of a browser client this is the `window` object (current tab)
2. **'this':** This is a reference to the global object
3. **Outer Environment:** This is a link to the outer environment
(e.g if a function was created on the global context the execution context of THAT function will have a link to the global execution context since that is the outer environemnt of that particular function
4. **Code:** Any code is written within that execution context

Variables and functions created in the global execution context gets saved as a name value pair in the Global Object  

### Execution Context: Creation & Hoisting
During the creation of an execution context for example in the global context when a script is initially run we have the Global Object (window), 'this' which is a reference to the global object, and a link to the outer environment.  
Then we stup memory space for variables (set them to undefined) and do function hosting.  

**Hoisting:** In the creation phase before execution of the code functions in their entirety are placed in the memory space while so they can be referred at anytime even before actually defining them. Variables, however, have to wait till execution to be set with the intended value  
```
b();
console.log(a);

var a = 'Hello World!';

function b() {
  console.log('Called b!');
}

console.log(a);
```
**Output**    
```
Called b!
undefined
Hello World!
```

**Explanation**    
The reason `a` is `undefined` while `b` can be called is due to function hoisting, `a` only gets a string values during the execution stage where the code in the execution context is running line by line, not in the creation phase.  

*All declared but non-initialized variables are given the value of `undefined` in the creation phase, it is good practice to not manually set `undefined` and use `null` instead to show that the variable was explicity not given a valid value.*  

JavaScript is actually single threaded and synchronous in that in runs instructions one at a time. For each function that is called in the global execution context the JavaScript engine creates a new execution context that goes through the creation and the execution phases separatley from the surrounding execution context.  

Each function call is then pushed and popped off the stack in the order they have been written in the code.
```
function b() {
  var myVar;
  console.log(myVar);
}

function a() {
  var myVar = 2;
  b();
}

var myVar = 1;
a();
```
**Explananation**   
* The global execution context is pushed onto the stack
  * functions `a` and `b` are set in memory copied entirely
  * variable `myVar` is set up with memory space but given the value of `undefined` at creation
* `a()` is called and its execution context is put onto the stack, it has its own copy of `myVar` and sets it to `1`
* `b()` is called and its execution context is put onto the stack it also has its own copy of `myVar` which is then set to `undefined`

### Scope Chain

If the value referenced inside a function cannot be found within its execution context, the reference to the outer execution context will be used to grab the variable (if it has been defined there), we keep this going like a chain until we have reached the global execution context in whch case if it is still not defined it will result in an error

### Scope Chain Example
```
function a() {
  function b() {
    console.log(myVar)'
  }
  var myVar = 2;
  b();
}

var myVar = 1;
a();
```
**Explanation**  
Since `b` is defined within `a`'s execution context it will first look for the definition of myVar within the function `a` and if it cannot find it, it wil try to check the global context, if `a` was in another function it would look there first and so on.

If `b` was called before `myVar` is set within `a` then `myVar` would be `undefined` NOT `1` because thats the value given to `myVar` when the execution context of `a` finishes the creation phase  

`let`: This can be used instead of `var` but it is block scoped (if, for, while, etc.), cannot use the variable before definiton, where as you can use variables defined with `var` gets set with `undefined`

### Asychronous Callbacks

The way aynchronous call backs (or events) work is the events are stored on the event queue and when the script is running JavaScript will run synchronously and then process each even tone by one by running the callback function attached to the event.

# Types & Operators

### 6 Primitive Types in JavaScript

1. Undefined: represents lack of existence (do not set a value) 
2. Null: represents lack of existence (set a value to this)
3. Boolean: true or false
4. Number: floating point number
5. String: sequence of characters ("") and ('') can be used
6. Symbol: new in ES6

### Operator Precedence & Associativity

Operators follow a strict precedence and in the case that the expression contains operators of the same precedence of even the same operator used multiple times then we use associativity which is either right to left or left to right.  

We can set default values like this:
```
function greet(name) {
  name = name || "Default";
  console.log("Hello " + name);
}
```
But we have to be careful in case `0` is passed because that is coerced to false.

# Objects & Functions

### Objects

Objects have properties and methods and reference to them in memory  
Dot operator does not need quotation marks but the memory access operator does

- **JSON:** JavaScript object notation the major difference is we must use quotes for the property names and values, for JavaScript we do not use quotes on the names when creating an object using an object literal

- **Useful Methods:**  
```
JSON.stringify(obj) // turns obj (JavaScript object) into a JSON object
JSON.parse(jsonString) // turns a json string into valid JavaScript object
```
### Functions

In JavaScript function **ARE** objects (that is what is meant by 'first class' functions), we can pass them to other functions return them from functions, attach them to variables and everything we can do with objects we can do with functions  

Functions have two main properties: a name property and a code property that can be invoked   
More properties and even more functions can be added to a function

- **Expression:** A unit of code that results in a value
- **Function Statements**
```
function greet() {
  console.log('hi');
}
```
- **Function Expressions**
```
var greet = function() {
  console.log('hi');
}
```
- **Creating a function as a parameter to another function**
```
function log(a) {
  a();
}

log(function() {
  console.log(a)
});
```
### Pass by Value or Reference

* Variables are passed by **value**  
* objects (functions too since they are objects) are passed by **reference**  
* Equals operator sets up a new memory space

### 'this'

During the creation phase a function execution context as the variable environment, a link to the outer environment and the key word `'this'` which JavaScript decides what that should be a reference too and sometimes it is not obvious.

In the global context `'this'` is the global window object

If a function is attached to an object, '`this`' will refer to THAT particular object, if the function does not belong to any object then `'this'` is the global object

**Caveat**  
A function defined within a function that is attached to an object will have a `'this'` variable that points to the global window object NOT the object of the function that defined it
```
var c = {
  name: 'The c object',
  log: function() {
    this.name = 'Updated c object';
    console.log(this);
    
    var setname = function(newname) {
      this.name = newname;
    }
    setname('Updated again! The c object')
    console.log(this)
  }
}

c.log()
```
**Output**  
```
Object {name: "Updated c object", log: function}
Object {name: "Updated c object", log: function}
```
**Explanantion**  
We basically attached a variable called `name` to the global window object when we called `setname()` because that '`this`' points to the global window object  

We can save this by saving the first `'this'` to another variable which we can call inside `'setname()'`

### 'arguments'

Whenever an execution context is created for a function an array-like object called `arguments` is also created that holds all the parameters passed into that function

### The spread operator '...'

Can be used to represent the other arguments passed in without using the array-like `'arguments'`
**Example**
```
function greet(firstname, lastname, language, ...other) {
  
}
```
Here `other` is an array holding all extra arguments passed the firstname, lastname and language

**Immediatley Invoked Function Expressions (IIFE)
```
var greeting = function(name) {
  console.log('Hello ' + name);
}();

(function(name) {
  console.log('Goodbye');
})("John");
```
By making IIFEs we can make sure that all variables defined within the function will not collide with variables defined outside of the function execution context
**Closures
```
function greet(whattosay) {
  return function(name) {
    console.log(whattosay + ' ' + name;
  }
}

var sayHi = greet('Hi');
sayHi('Tony');
}
```
**Output**  
```
Hi Tony
```
**Explanation**  
- After calling `greet` the first time we get a function in return that will print out the `whattosay` passed in and the `name`
- When `sayHi` is called we still have access to the `whattosay` variable due closures which keep references to variables defined within an execution context of a function that has already executed in the functions that is defined within the already executed function
**Another Example**  
```
function buildFunctions() {
  var arr = [];
  
  for (var i = 0; i < 3; i++) {
    arr.push(function() {
      console.log(i);
    });
  }
  return arr;
}

var fs = buildFunctions();

fs[0]();
fs[1]();
fs[2]();
```

**Output**  
```
3
3
3
```
**Explanation**  
The closure for all those functions defined `i` as 3 since that is the last value of `i`  
**Solution**  
Have an IIFE defined within the loop so that each array member will be pointing to its own execution context (closure) that will hold the value that it wants
```
function buildFunctions2() {
  var arr = [];
  for (var i = 0; i < 3; i++) {
    arr.push(
      (function(j){
      
      }(i))
    )
  
  }
  return arr
}
```
### call(), apply() & bind()
- **bind():** called on a function and takes an object as a parameter and returns a functions where `'this'` will be a reference to the object that was passed into bind, creates a copy of the object
- **call():** invokes the function but the first parameter will be the object that is reference by the `'this'` variable and also other parameters can be passed into after that object
- **apply():** does the same thing as call() except it takes the parameters as an array

### Using bind() to set a default parameter
```
function multiply(a, b) {
  return a*b;
}

var multiplyByTwo = multiply.bind(this, 2);
console.log(multiplyByTwo(4));
```

### The Prototype
Every object (and function) has a prototype property which is a reference to another object, if a variable or function is called on that object and cannot be found directly attached we look at its prototype to see if it exists there this is called the **Prototype Chain**

- **hasOwnProperty():** A funciton of the base object that is the prototype of all objects that can be called on an object to see if the property is actually a property of the object and not of a prototype down the prototype chain

# Building Objects
```
function Person() {
  this.firstname = 'John';
  this.lastname = 'Doe';
}

var John = new Person();
```
The `new` keyword creates an empty object and called the function where `'this'` refers to the new empty object inside the function and as long as no other value is returned the function will return this new object to be set to a variable on the left side of the equals operator

### Prototype property of a Function
Prototype of any objects created if you are using that funciton as a function constructor, you can access Function.prototype and set new values and functions that will be automatically attached to any values created by that function constructor because Function.prototype points to the prototype of all constructed objects

### Pure Prototypal Inheritence
```
var person = {
  firstname: 'Default',
  lastname: 'Default',
  greet: function() {
    return 'Hi' + this.firstname;
  }
}

var john = Object.create(person);
john.firstname = "John";
john.lastname = "Doe";
```





 
