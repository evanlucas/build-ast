# build-ast documentation

## Usage

```js
const Builder = require('@helpdotcom/build-ast')
const ast = Builder.ast
```

### Builder()

Creates a new `Builder` object. The `Builder` object will be used
to generate AST nodes. All of the methods on `Builder.prototype` can be
chained together:

```js
Builder()
  .use('strict')
  .module('Test')
```

The static methods on `Builder` will return a [`<Object>`][].


#### Builder().and(...args)

Takes a variable amount of arguments that should all be an [`<Object>`][].
This joins two expressions using the `&&` operator.


#### Builder().array(items)

Creates an array of _items_.

* `items` [`<Array>`][] An array of nodes

```js
Builder().array([
  Builder.string('a')
, Builder.string('b')
])
// will be the ast for
['a', 'b']
```


#### Builder().assign(left, right, operator)

Assign the object-path [`<String>`][] _left_ to the _right_ value.

* `left` [`<String>`][] A dot notated string that can represent a nested object
* `right` Any The value
* `operator` [`<String>`][] The operator to use (`=`, `*=`, etc.)

```js
Builder().assign('Event.VERSION', Builder.string('1.0.0'))
// will build the ast for
Event.VERSION = '1.0.0'
```


#### Builder().block(body)

Creates a new `BlockStatement` node and pushes it onto the current `Builder`'s
body.

* `body` [`<Array>`][]


#### Builder().build()

Returns the builder's body.


#### Builder().callFunction(name, args)

Calls a function with _name_ with the _args_

* `name` [`<String>`][] The function name, a dot notated string that can
  represent a nested object
* `args` [`<Array>`][] Array of AST Nodes


#### Builder().declare(type, name, val)

Declare a variable.

* `type` [`<String>`][] `const`, `let`, or `var`
* `name` [`<String>`][] The variable name
* `val` Any

Example:

```js
Builder().declare('const', 'http', Builder.require('http'))
// will build the ast for
const http = require('http')
```


#### Builder().equals(left, right)

* `left` [`<Object>`][] The left hand side
* `right` [`<Object>`][] The right hand side


#### Builder().function(name, args, body)

Creates a [`<Function>`][]

* `name` [`<String>`][] The function name
* `args` [`<Array>`][] An array of arguments. These should be strings.
* `body` [`<Array>`][] The function body. This will be wrapped in a
  `BlockStatement`

```js
Builder().function('test', ['t'], [])
// will build the ast for
function test(t) {

}
```


#### Builder().if(test, block, alt)

Creates an `IfStatement`

* `test` AST Node
* `block` The consequent block. Should be wrapped in a `BlockStatement`
* `alt` The alternate block. Should be wrapped in a `BlockStatement`

```js
Builder().if(
  Builder.not(Builder.instanceOf(
    Builder.this(), Builder.id('Event')
  ))
, Builder.block(
    Builder.returns(Builder.new('Event', ['buffer']))
  )
)
// will build the ast for
if (!(this instanceof Event)) {
  return new Event(buffer)
}
```


#### Builder().ifNot(test, block, alt)

Shortcut for the following:

```js
Builder().if(Builder.not(test), consequent, alternate)
```


#### Builder().instanceOf(left, right)

Creates an `instanceof` check.

* `left` AST Node
* `right` AST Node


#### Builder().module(name)

Adds a module.exports statement.

* `name` [`<String>`][] The function name to export


#### Builder().new(name, args)

Calls `new` on the _name_ as a constructor.

* `name` [`<String>`][] The constructor name to call
* `args` [`<Array>`][] The arguments to pass to the constructor. In the event
  an item of _args_ is a string, it will be returned as an `Identifier`.


#### Builder().not(expr)

Negates the _expr_ by wrapping it in a `UnaryExpression` with `!` as the
operator.

* `expr` [`<Object>`][] AST Node


#### Builder().notEquals(left, right)

* `left` [`<Object>`][] The left hand side
* `right` [`<Object>`][] The right hand side

**Note: uses strict equality (`!==`)**


#### Builder().number(n)

Creates a raw number.

* `n` [`<Number>`][] The number. Will be coerced to a number.

The following will both return a node for the same value:

```js
Builder().number(3)
Builder().number('3')
// => 3
```


#### Builder().object(properties)

Creates an array of _properties_.

* `properties` [`<Object>`][] An object mapping properties to values

```js
Builder().object({
  foo: Builder.number('42')
, bar: Builder.string('baz')
})
// will be the ast for
{ foo: 42, bar: 'baz' }
```


#### Builder().or(...args)

Takes a variable amount of arguments that should all be an [`<Object>`][].
This joins two expressions using the `||` operator.


#### Builder().regex(re)

Creates a regular expression.

* `re` [`<String>`][] | [`<RegExp>`][] The string or regex

Must start with a slash (`/`).


#### Builder().require(str, prop)

Adds a require statement.

* `str` [`<String>`][] The module to require
* `prop` [`<String>`][] A dot notated string that can represent a nested object

```js
// To get require('path').join
Builder().require('path', 'join')

// To get require('path').posix.join
Builder().require('path', 'posix.join')
```


#### Builder().reset()

Clears the `Builder`'s body and resets the state.


#### Builder().returns(arg)

Creates a `ReturnStatement`.

* `arg` AST Node

```js
Builder().returns(ast.literal(true))
// will create the ast for
return true
```


#### Builder().string(str)

Creates a raw string.

* `str` [`<String>`][] The string. If not a string, it will be coerced.

```js
Builder().string('Hi!')
```


#### Builder().ternary(test, block, alt)

Creates a `ConditionalStatement`

* `test` AST Node
* `block` The consequent block.
* `alt` The alternate block.

```js
Builder().ternary(
  Builder.objectPath('isActive')
, Builder.string('User is active')
, Builder.string('User is offline')
)
// will build the ast for
isActive ? 'User is active' : 'User is offline'
```


#### Builder().throws(arg)

Creates a `ThrowStatement` throwing the given _arg_.

* `arg` [`<Object>`][] AST Node.


#### Builder().use(str)

Adds a `use` directive (`'use strict'` or `'use asm'`).

* `str` [`<String>`][]


```js
Builder().use('strict')
```


#### Builder().program()

Marks the builder as a program.


***

## Additional static methods

#### Builder.objectPath(str)

Returns a member expression for the given dot notated object path.

* `str` [`<String>`][] Dot notated string (ex. 'admin.id')

**Note**: To explicitly mark a segment as a computed property, prefix the
segment with a `^`. Ex: `admin.^i` would represent `admin[i]`.


#### Builder.Error(msg)

Returns the representation of an [`<Error>`][] object.

* `msg` [`<String>`][] | [`<Object>`][] The message

If `msg` is a `string`, it will be properly wrapped. Otherwise, it will be
unmodified.


#### Builder.TypeError(msg)

Returns the representation of a [`<TypeError>`][] object.

* `msg` [`<String>`][] | [`<Object>`][] The message

If `msg` is a `string`, it will be properly wrapped. Otherwise, it will be
unmodified.


#### Builder.RangeError(msg)

Returns the representation of a [`<RangeError>`][] object.

* `msg` [`<String>`][] | [`<Object>`][] The message

If `msg` is a `string`, it will be properly wrapped. Otherwise, it will be
unmodified.


#### Builder.class()

Returns a prototype to create a new class.


##### Class.declaration(name, superClass)

Forces the class to be a ClassDeclaration.

* `name` [`<String>`][] The class name
* `superClass` [`<String>`][] The superClass

Returns `this`


##### Class.expression(name, superClass)

Forces the class to be a ClassExpression.

* `name` [`<String>`][] The class name
* `superClass` [`<String>`][] The superClass

Returns `this`


##### Class.ctor(args, body)

Define the constructor of the class.

* `args` [`<Array>`][] The function arguments
* `body` [`<Array>`][] The function body block

Returns `this`


##### Class.method(name, args, body, options)

Define a method of the class.

* `name` [`<String>`][] The method name
* `args` [`<Array>`][] The function arguments
* `body` [`<Array>`][] The function body block

Returns `this`


##### Class.build()

Builds the class into either a `ClassExpression` or a `ClassDeclaration`.

Returns an [`<Object>`][]


[`<Array>`]: https://mdn.io/array
[`<Error>`]: https://mdn.io/Error
[`<Function>`]: https://mdn.io/function
[`<Number>`]: https://mdn.io/number
[`<Object>`]: https://mdn.io/object
[`<RangeError>`]: https://mdn.io/RangeError
[`<RegExp>`]: https://mdn.io/RegExp
[`<String>`]: https://mdn.io/string
[`<TypeError>`]: https://mdn.io/TypeError
