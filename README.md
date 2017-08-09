# ES6 Learning Note
*Resource*: [You don't Know JS](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20&%20beyond/README.md#you-dont-know-js-es6--beyond)

## Arrow Function ##
* All the capabilities of normal function parameters are available to arrow functions, including default values, destructuring, rest parameters, and so on
* While not a hard-and-fast rule, I'd say that the readability gains from => arrow function conversion are inversely proportional to the length of the function being converted. The longer the function, the less => helps; the shorter the function, the more => can shine.
* In addition to lexical this, arrow functions also have lexical arguments -- they don't have their own arguments array but instead inherit from their parent -- as well as lexical super and new.target.

## Block Scope ##
* The behavior of the const keyword in JavaScript varies slightly from other languages. Const prevents a variable from being reassigned. However, complex data types like arrays and objects could still be mutated.

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
# You should only use concise methods if you're never going to need them to do recursion or event binding/unbinding.

# Helpful Git Commands #
## Checking Out and Testing Pull Requests ##
* Fetch all pull request branches  
`git fetch origin`

* Checkout out a given pull request branch based on its number  
`git checkout -b 999 pull/origin/999`
