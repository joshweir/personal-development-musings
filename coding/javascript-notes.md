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

## Factory functions are better than classes and constructor functions

As described above ([Invocation and `this`](#invocation-and-this)), `this` is annoying, it's much easier to avoid worrying about `this` (or `new` for that matter) and just use factory functions - enabling private access to variables through closures.

Example (adapted from [this](https://www.youtube.com/watch?v=ImwrezYhw4w)):

```javascript
//using a constructor function (or a class for that matter)...
let MyThing = function (myProp) {
	this.myProp = myProp;
  this.myFunc = function () {
  	document.writeln(this.myProp);
  }
}

let myThing = new MyThing('prop value');
//$("button").click(myClass.myFunc) //wont work - binds this to the dom element
//have to bind the function to the constructed object:
//$("button").click(myClass.myFunc.bind(myClass))
//or call the function within a function (when calling function as a method call - this becomes the object calling the method):
//$("button").click(() => myThing.myFunc())

//!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
// a factory function takes away the need to use `this` at all!
//!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
let makeThing = function (myProp) {
	return {
  	myFunc () {
      //access `myProp` through closure - it's private and don't need `this`!
    	document.writeln(myProp);
    }
  }
}
let thing = makeThing('prop value');
$("button").click(thing.myFunc);
```

Factory functions are also useful to use partial application, injecting factory functions with common dependencies during app scaffolding, not having to concern with those dependencies when using the factory itself:

```javascript
let makeMyFactory = ({ connection = null }) => ({ myProp = 'default1' }) => {
	return {
  	myFunc () {
    	document.writeln(`Using connection dependency: ${connection} and myProp: ${myProp}`);
    }
  }
}

//make the factory first, in the app's main
myFactory = makeMyFactory({connection: 'my connection'});

//then when I need to use the factory for specific cases
myInstanceFromTheFactory = myFactory({myProp: 'my prop value'});
anotherInstanceFromTheFactory = myFactory({}); //myProp will default

myInstanceFromTheFactory.myFunc();
anotherInstanceFromTheFactory.myFunc();
```

## Composition is better than inheritance

TODO

## Functional Programming

* Functions should be pure (given inputs, will always produce the same output). This also means, do not mutate objects passed as input to the function - as these objects are passed by reference - instead clone the object first [before modifying the clone](https://gist.github.com/ericelliott/43a86f0e7ffb22b6c53f#file-pure-add-to-cart-js):

```javascript
const addToCart = (cart, item, quantity) => {
  const newCart = lodash.cloneDeep(cart);
  newCart.items.push({
    item,
    quantity
  });
  return newCart;
};
```

* Build programs on re-usable functions. Through higher order functions (functions that can be passed around) we can use these re-usable functions as building blocks through function composition (output of functions being input of another function and so on). Perfect example [here](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-function-composition-20dfb109a1a0). Make use of lodash (or ramda) functions `compose` (or `pipe` which is opposite of `compose`) and `curry` to break up a multi input function into a curried version of the same function.

* For traversing / computing arrays, rather than using imperative programming (eg. `for(var i=0; i<arr.length; i++`) use the functional methods available on `Array` (eg. `map`, `filter`, etc).

TODO: Partial application, currying, compose all with ramda

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

### ES6 vs ES5 functions

* `arguments` is not available in ES6 functions
* in ES6 functions, `this` is whatever `this` is just before the function is called. In ES5, it is whatever owns the function (if not called as an object method, it's usually window or global).

### ES6 Computed Object Keys

```javascript
my_key = () => 'ahh_hello'
let obj = {
  [my_key()]: 'bar'
}
console.log(obj) //{ahh_hello: 'bar'}
```

### CORS

Usually by default, if you make an xml http request (such as ajax) to an endpoint and the origin url domain name of the request does not match the server url domain name, the request will fail with an error like `No 'Access-Control-Allow-Origin' header is present..`. This is a security measure that the server can specify what endpoint's it trusts to make xhr requests to it. The server could potentially specify, with the CORS headers, what endpoint's it trusts and/or what routes to enable these headers for.


### TODO

Ramda library

## import map     from 'ramda/src/map'
## import filter  from 'ramda/src/filter'
## import compose from 'ramda/src/compose'
## import path from 'ramda/src/path'

# `path` - import path from 'ramda/src/path'

Rather than having to do the ampersand stairway to hell:
response &&
response.body &&
response.body.data &&
response.body.data.name
use `path`:

import path from 'ramda/src/path'
const pluckName =3D path(['body', 'data', 'name'])
pluckName(response) //undefined (no error) if cannot find



import map     from 'ramda/src/map'
import filter  from 'ramda/src/filter'
import compose from 'ramda/src/compose'


Object.create(foo) creates a new object but makes foo the prototype, so
that any object properties on the new object that cannot be found are
searched up the prototype chain to find. Object.assign assigns the
properties of remaining arguments directly to the object in the first
argument, the argument returned is the same object as the first argument.


factory function / dependency injection examples:

// from prototype (to save memory)
// but have to call init method to initialize properties
const proto =3D {
init ({make =3D 'honda', color =3D 'red'}) {
this.make =3D make;
    this.color =3D color;
},
drive () {
console.log('driving...', 'make: ', this.make);
}
}
const car =3D function () {
//object created inherits the prototype
return Object.create(proto);
}
const myCar =3D car();
myCar.init({make: 'toyota'})
myCar.drive();
myCar.color;

// factory function inject dependencies
const makeCar =3D ({connection, otherDep}) =3D> ({make =3D 'toyota', color =
=3D
'red'}) =3D> {
return {
drive () {
console.log('driving...' + color + ' ' + make + ' connection: ' +
connection)
}
};
}
const connection =3D 'connection';
const otherDep =3D 'other dep';
const car =3D makeCar({connection, otherDep});
const myCar =3D car({color: 'blue'});
myCar.drive();
