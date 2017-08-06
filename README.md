# ES6 Learning Note
*Resource*: [You don't Know JS](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20&%20beyond/README.md#you-dont-know-js-es6--beyond)

# Arrow Function #
* All the capabilities of normal function parameters are available to arrow functions, including default values, destructuring, rest parameters, and so on
* While not a hard-and-fast rule, I'd say that the readability gains from => arrow function conversion are inversely proportional to the length of the function being converted. The longer the function, the less => helps; the shorter the function, the more => can shine.
* In addition to lexical this, arrow functions also have lexical arguments -- they don't have their own arguments array but instead inherit from their parent -- as well as lexical super and new.target.

# Block Scope #
* The behavior of the const keyword in JavaScript varies slightly from other languages. Const prevents a variable from being reassigned. However, complex data types like arrays and objects could still be mutated.

# Lazy Expressions (Default Value Expressions) #
* A default value could be in the form of a function call. This would be a lazy expressions because the function would not be called until it’s needed.

# Gather & Spread #
* Only data types that have an iterator can be spread.
