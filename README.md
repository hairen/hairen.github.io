# ES6 Learning Note
*Resource*: [You don't Know JS](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20&%20beyond/README.md#you-dont-know-js-es6--beyond)

## Arrow Function ##
* All the capabilities of normal function parameters are available to arrow functions, including default values, destructuring, rest parameters, and so on
* While not a hard-and-fast rule, I'd say that the readability gains from => arrow function conversion are inversely proportional to the length of the function being converted. The longer the function, the less => helps; the shorter the function, the more => can shine.
* In addition to lexical this, arrow functions also have lexical arguments -- they don't have their own arguments array but instead inherit from their parent -- as well as lexical super and new.target.


## Block Scope ##
* The behavior of the const keyword in JavaScript varies slightly from other languages. Const prevents a variable from being reassigned. However, complex data types like arrays and objects could still be mutated.
* Assigning an object or array as a constant means that value will not be able to be garbage collected until that constant's lexical scope goes away, as the reference to the value can never be unset. My advice: to avoid potentially confusing code, only use const for variables that you're intentionally and obviously signaling will not change. In other words, don't rely on const for code behavior, but instead use it as a tool for signaling intent, when intent can be signaled clearly.


## Lazy Expressions (Default Value Expressions) ##
* A default value could be in the form of a function call. This would be a lazy expressions because the function would not be called until it’s needed.


## Gather & Spread ##
* Only data types that have an iterator can be spread.


## Destructuring ##
* Not Just Declarations  

```javascript
var a, b, c, x, y, z;

[a,b,c] = foo();
( { x, y, z } = bar() );

console.log( a, b, c );				// 1 2 3
console.log( x, y, z );				// 4 5 6
```

For the object destructuring form specifically, when leaving off a var/let/const declarator, we had to surround the whole assignment expression in ( ), because otherwise the { .. } on the lefthand side as the first element in the statement is taken to be a block statement instead of an object.


## Object Literal Extensions ##
* You should only use concise methods if you're never going to need them to do recursion or event binding/unbinding.


## For..Of vs Foo ..In loop ##
* **for..in** loops over the keys/indexes in the a array, while **for..of** loops over the values in a array.
* Standard built-in values in JavaScript that are by default iterables (or provide them) include:
  * Arrays
  * Strings
  * Generators
  * Collections / TypedArrays
* **for..of** loops can be prematurely stopped, just like other loops, with _break_, _continue_, _return_ (if in a function), and _thrown exceptions_.




# Scope & Closures Learning Note
*Resource*: [You don't Know JS](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20&%20closures/README.md#you-dont-know-js-scope--closures)

## What is Scope?
* *Scope* is set of rules about definding variables, where to store and find them at a later time.
* JS is a compiled project, but it's _not_ well compiled as other traditionally-compiled languages.
* For JavaScript, the compilation that occurs happens, in many cases, mere microseconds (or less!) before the code is executed. 
* Two mechanisms in JavaScript can "cheat" lexical scope: _eval(..)_ and _with_. The former can modify existing lexical scope (at runtime) by evaluating a string of "code" which has one or more declarations in it. The latter essentially creates a whole new lexical scope (again, at runtime) by treating an object reference as a "scope" and that object's properties as scoped identifiers.
  
  The downside to these mechanisms is that it defeats the Engine's ability to perform compile-time optimizations regarding scope look-up, because the Engine has to assume pessimistically that such optimizations will be invalid. Code will run slower as a result of using either feature. Don't use them.

## Named Function Expressions: Benefits
* Handy function self-reference
* More debuggable stack traces
* More self-documenting code

## Hoisting
* Both function declarations and variable declarations are hoisted. But, functions are hosted _**first**_, and then variables.
* It's probably best to avoid declaring functions in blocks.
* Declarations themselves are hoisted, but assignments, even assignments of function expressions, are not hoisted.

## Closure
* Definition: Closure is when a function "remember" its lexical scope even when the function is executed outside that lexical scope.

## Object-Orienting
* `this` is neither a reference to the _function itself_, nor is it a reference to the _function's lexical scope_.
* `this` is actually a binding that is made when a function is invoked, and _what_ it references is determined entirely by the call-site where the function is called.
* Every function, while it's executing, has a reference to its current execution context call "`this`". The "`this`" reference is JavaScript's version of dynamic scrop.
* `this` is determined by how function is called.
* Determining the _this_ binding for an executing function requires finding the direct call-site of that function.
  1. Called with `new`? Use the newly constructed object.
  2. Called with `call` or `apply` (or `bind`)? Use the specified object.
  3. Called with a context object owning the call? Use that context object.
  4. Default: `undefined` in `strict mode`, global object otherwise.
* Instead of the four standard binding rules, ES6 arrow-functions use lexical scoping for `this` binding, which means they adopt the `this` binding (whatever it is) from its enclosing function call. They are essentially a syntactic replacement of `self = this` in pre-ES6 coding.

## Prototype
* A `constructor` makes an object _linked to_ its own prototype.
* In JavaScript, every object is built by a constructor function and does not mean classes are being instanitate. When a constructor function is called, a new object is created with a link to the object's prototype.

## Type
* `undefined`, `string`, `number`, `boolean`, `Object` are build-in types in Javascript. `symbol` is added in ES6. `function` is a subtype of the `object` type.
* `NaN` means number conversion failed.
* There is a original bug in _typof null_. 
  ```javascript
    typeof null === "object"; // true
  ```
  If you want to test for a `null` value using its type, you need a compound condition:
  ```javascript
    var a = null;

    (!a && typeof a === "object"); // true
  ```
* Function it's actually a "subtype" of object. Specifically, a function is referred to as a "callable object" -- an object that has an internal `[[Call]]` property that allows it to be invoked.
* If you use `typeof` against a variable, it's not asking "what's the type of the vaiable?" as it may seem, since JS variables have no types. Instead, it's asking "what's the type of the value _in_ the variable?"
* There's also a special behavior associated with typeof as it relates to undeclared variables that even further reinforces the confusion. Consider:
  ```javascript
  var a;

  typeof a; // "undefined"

  typeof b; // "undefined"
  ```
  The `typeof` operator returns `"undefined"` even for "undeclared" (or "not defined") variables. Notice that there was no error thrown when we executed `typeof b`, even though `b` is an undeclared variable. This is a special safety guard in the behavior of `typeof`.

## Value
### Arrays
* Generally, it's not a great idea to add `string` keys/properties to arrays. Use `object`s for holding values in keys/properties, and save `array`s for strictly numerically indexed values.
  See the following snippet:
  ```javascript
  var a = [ ];

  a["13"] = 42;

  a.length; // 14
  ```
### Array-Likes
* There will be occasions where you need to convert an `array`-like value (a numerically indexed collection of values) into a true `array`. One very common way to make such a conversion is to borrow the `slice(..)`utility against the value:
  ```javascript
  function foo() {
	  var arr = Array.prototype.slice.call( arguments );
    arr.push( "bam" );
    console.log( arr );
  }

  foo( "bar", "baz" ); // ["bar","baz","bam"]
  ```
* As of ES6, there's also a built-in utility called `Array.from()` that can do the same task:
  ```javascript
  var arr = Array.from( arguments );
  ```
### Value vs.Reference
* Let's illustrate:
  ```javascript
  var a = 2;
  var b = a; // `b` is always a copy of the value in `a`
  b++;
  a; // 2
  b; // 3

  var c = [1,2,3];
  var d = c; // `d` is a reference to the shared `[1,2,3]` value
  d.push( 4 );
  c; // [1,2,3,4]
  d; // [1,2,3,4]
  ```
  Simple values (aka scalar primitives) are always assigned/passed by _value-copy_: `null`, `undefined`, `string`, `number`, `boolean`, and ES6's `symbol`.

  Compound values -- objects (including `array`s, and all boxed object wrappers) and `function`s -- _always_ create a copy of the reference on assignment or passing. Refercens are not like references/pointers in other languages -- they're never pointed at other variables/references, only at the underlying values.

# Helpful Git Commands #
## Checking Out and Testing Pull Requests ##
* Fetch all pull request branches  
`git fetch origin`

* Checkout out a given pull request branch based on its number  
`git checkout -b 999 pull/origin/999`
