# ES6 Learning Notes
*Resource*: [You don't Know JS](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20&%20beyond/README.md#you-dont-know-js-es6--beyond)

# Arrow Function #
* All the capabilities of normal function parameters are available to arrow functions, including default values, destructuring, rest parameters, and so on
* While not a hard-and-fast rule, I'd say that the readability gains from => arrow function conversion are inversely proportional to the length of the function being converted. The longer the function, the less => helps; the shorter the function, the more => can shine.
* In addition to lexical this, arrow functions also have lexical arguments -- they don't have their own arguments array but instead inherit from their parent -- as well as lexical super and new.target.

# Arrow Function #
* The behavior of the const keyword in JavaScript varies slightly from other languages. Const prevents a variable from being reassigned. However, complex data types like arrays and objects could still be mutated.
