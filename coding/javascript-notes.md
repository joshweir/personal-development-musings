# JavaScript Notes

## Lexical scope

A function can access variables declared outside it's scope. Every inner function can access its outer levels variables.

```javascript
let outer = 'outer';
lexicalEg = function() {
  console.log(outer, 'l1 output: outer');
  return function() {
  	console.log(outer, 'l2 output: outer');
  }
};
lexicalEg()();
```

## Closure

A function accessing parent scope, even after the parent function has closed. Allows creating the module pattern with private access to variables:

```javascript
// _privateFoo is accessed within the getFoo, setFoo,
// concatFoo inner functions (closure) and is private
// to myModule even though the iife has already closed.
const myModule = (function(){
  let _privateFoo = 'foo',
  getFoo = () => _privateFoo,
  setFoo = foo => _privateFoo = foo,
  concatFoo = withVal => _privateFoo + withVal;
  return {
    getFoo, concatFoo, setFoo
  };
})();
const { getFoo, setFoo, concatFoo } = myModule;
console.log(getFoo(), '//foo');
setFoo('bar');
console.log(getFoo(), '//bar');
console.log(concatFoo('baz'), '//barbaz');
```

## Objects, Function Constructors, Modules

This gives a good recap: https://gist.github.com/hallettj/64478

```javascript
//if you only need a single instance of an object can do this:
var obj = {
	myvar: 2,
	getvar: function() {console.log(this.myvar);}
};
obj.getvar();
//but now there is public access to it's variables:
obj.myvar = 3;
obj.getvar();

//so better to use the module pattern:
var objModule = (function(){
	var _myvar = 5,
  getvar = function() {console.log(_myvar)};
  return {
  	getvar: getvar
  };
})();
objModule.getvar(); //can access the private variable (closure) through public method

//but if I need to create many instances of an object, better to use the constructor in combination with prototype to save on memory:
function Obj(myvar) {
	this.myvar = myvar;
}
//implementing getvar on the object prototype will ensure that only a single instance of the getvar function is instantiated even with multiple instances of Obj - saving memory:
Obj.prototype.getvar = function() {console.log(this.myvar);};
/*
note that this could also be written like this:
Obj.prototype = {
	getvar: function() {console.log(this.myvar);}
}
or:
parentObj = {
	getvar: function() {console.log(this.myvar);}
};
Obj.prototype = parentObj;
so we see how inheritance comes into play now, Obj has inherited all the methods and variables from parentObj. It would also inherit the parentObj.prototype. Therefore Obj could potentially access a function defined on it's grandfather's prototype in this way. This proptotype bubbling is called delegation.
*/
const obj1 = new Obj(1);
const obj2 = new Obj(2);
//but this example has public access to the myvar variable:
obj1.myvar = 'whoops';
obj1.getvar();
obj2.getvar();

//if you want to make a constructor with a private variable you cannot use the prototype (because the prototype will not have access to the private variable) so will need to define the method within the constructor function also. I dont think it's worth it, because I am using the constructor to instantiate many objects - so memory usage will suffer - my trade off would be to use the prototype to save memory and allow public access to the variable.
function ObjWithPrivate(myvar) {
	var _myvar = myvar;
  this.getvar = function() {console.log(_myvar)};
}
const objpriv1 = new ObjWithPrivate(1);
objpriv1.getvar();
console.log(objpriv1._myvar, '//undefined');
```

### Invocation and `this`

Summary: `this` is annoying - depending on the way a function is invoked, `this` will point to [different things](https://medium.com/quick-code/understanding-the-this-keyword-in-javascript-cb76d4c7c5e8):
* By default, `this` refers to global object which is global in case of NodeJS and window object in case of browser
* When a method is called as a property of object, then `this` refers to the parent object. Which means only when called on property of object: `myObj.func()` where if you assign the function to a variable and invoke, then `this` becomes the global object: `let func = myObj.func; func()`
* When a function is called with `new` operator then `this` refers to the newly created instance.
* When a function is called using `call` and `apply` method then `this` refers to the value passed as first argument of `call` or `apply` method.

```javascript
//function invocation
var foo = function() {
  //this is the global object
  console.log(this.foo.toString());
}
foo();

//method invocation
var myObj = {
  myVal: 1,
  foo: function() {
    //this is myObj
    console.log(this.myVal);
  }
}
myObj.foo();

//function invocation within a method
var myObj2 = {
  myVal: 1,
  foo: function() {
    var helper = function() {
      //this is still the global object (unintuitively)
      console.log(this.myObj2);
    }
    helper()
  }
}
myObj2.foo();

//constructor invocation
//capitalize the function name as convention
MyObj = function(foo) {
  this.foo = foo;
}
MyObj.prototype.getFoo = function() {
  return this.foo
}
//this refers to the current object
myConstructed = new MyObj('myfoo');
console.log(myConstructed.getFoo());

//apply (or call) invocation
//choose what I want this to be when invoking a function
var someObj = {foo: 'otherFoo'}
console.log(MyObj.prototype.getFoo.apply(someObj))

//generic function to extend objects with methods easily:
Function.prototype.method = function (name, func) {
  if (!this.prototype[name]) {
    this.prototype[name] = func;
  }
};
//add trim to string:
String.method('addBang', function () {
  return this + '!';
});
console.log('boom'.addBang());
```

## Lambda

A function that is passed as an argument to another function, returned as a value from a function, or assigned to variables or data structures.

## Partial Application and Currying

TODO

## Factory functions are better than classes

TODO

## Composition is better than inheritance

TODO

## Functional Programming

TODO

## Don't pollute global scope

Instead of putting many variables on the global object, create a single name space variable on global object for your app, and put objects in there:

```javascript
var myApp = {
	var1: {
		var1_1: () => console.log('hello');
	}
};
myApp.var1.var1_1();
```

### var vs let vs const

* `let` and `const` has block scope, `var` has function scope
* `var` is hoisted
* `const` cannot be re assigned, but you can modify its properties (if say it's an object), or add/remove elements if it's an array.

### CORS

Usually by default, if you make an xml http request (such as ajax) to an endpoint and the origin url domain name of the request does not match the server url domain name, the request will fail with an error like `No 'Access-Control-Allow-Origin' header is present..`. This is a security measure that the server can specify what endpoint's it trusts to make xhr requests to it. The server could potentially specify, with the CORS headers, what endpoint's it trusts and/or what routes to enable these headers for.
