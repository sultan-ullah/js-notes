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

## Global Execution Content

The global execution context (created at the start of the script) contains 4 things:
1. **Global Object:** In the case of a browser client this is the `window` object (current tab)
2. **'this':** This is a reference to the global object
3. **Outer Environment:** This is a link to the outer environment
(e.g if a function was created on the global context the execution context of THAT function will have a link to the global execution context since that is the outer environemnt of that particular function
4. **Code:** Any code is written within that execution context

Variables and functions created in the global execution context gets saved as a name value pair in the Global Object  

## Execution Context: Creation & Hoisting
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

## Scope Chain

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

## Asychronous Callbacks

The way aynchronous call backs (or events) work is the events are stored on the event queue and when the script is running JavaScript will run synchronously and then process each even tone by one by running the callback function attached to the event.

# Types & Operators

## 6 Primitive Types in JavaScript

1. Undefined: represents lack of existence (do not set a value) 
2. Null: represents lack of existence (set a value to this)
3. Boolean: true or false
4. Number: floating point number
5. String: sequence of characters ("") and ('') can be used
6. Symbol: new in ES6

## Operator Precedence & Associativity

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

**JSON:** JavaScript object notation the major difference is we must use quotes for the property names and values, for JavaScript we do not use quotes on the names when creating an object using an object literal

#### Useful Methods:
```
JSON.stringify(obj) // turns obj (JavaScript object) into a JSON object
JSON.parse(jsonString) // turns a json string into valid JavaScript object
```
## Functions

In JavaScript function **ARE** objects (that is what is meant by 'first class' functions), we can pass them to functions return them from functions, attach them to variables etc.
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
- **Another use case of first class functions**
```
function log(a) {
  a();
}

log(function() {
  console.log(a)
});
```
#### Pass by Value or Reference

In JavaScript variables are passed by **value** while objects (functions too since they are objects) are passed by **reference**





 
