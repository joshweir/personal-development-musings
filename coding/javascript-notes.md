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

## Lambda

An anonymous function.

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
