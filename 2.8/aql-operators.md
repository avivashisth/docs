---
layout: default
description: AQL operators and their precedence
---
Operators
=========

AQL supports a number of operators that can be used in expressions.  There are
comparison, logical, arithmetic, and the ternary operator.

#### Comparison operators

Comparison (or relational) operators compare two operands. They can be used with
any input data types, and will return a boolean result value.

The following comparison operators are supported:

- `==` equality
- `!=` inequality
- `<`  less than 
- `<=` less or equal
- `>`  greater than
- `>=` greater or equal
- `IN` test if a value is contained in an array
- `NOT IN` test if a value is not contained in an array

These operators accept any data types for the first and second operands.

Each of the comparison operators returns a boolean value if the comparison can
be evaluated and returns `true` if the comparison evaluates to true, and `false`
otherwise. Please note that the comparison operators will not perform any
implicit type casts if the compared operands have different types.

Some examples for comparison operations in AQL:

```
0 == null                 // false
1 > 0                     // true
true != null              // true
45 <= "yikes!"            // true
65 != "65"                // true
65 == 65                  // true
1.23 > 1.32               // false
1.5 IN [ 2, 3, 1.5 ]      // true
"foo" IN null             // false
42 NOT IN [ 17, 40, 50 ]  // true
```

#### Logical operators

The following logical operators are supported in AQL:

- `&&` logical and operator
- `||` logical or operator
- `!` logical not/negation operator

AQL also supports the following alternative forms for the logical operators:

- `AND` logical and operator
- `OR` logical or operator
- `NOT` logical not/negation operator

The alternative forms are aliases and functionally equivalent to the regular 
operators.

The two-operand logical operators in AQL will be executed with short-circuit 
evaluation. The result of the logical operators in AQL is defined as follows:

- `lhs && rhs` will return `lhs` if it is `false` or would be `false` when converted
  into a boolean. If `lhs` is `true` or would be `true` when converted to a boolean,
  `rhs` will be returned.
- `lhs || rhs` will return `lhs` if it is `true` or would be `true` when converted
  into a boolean. If `lhs` is `false` or would be `false` when converted to a boolean,
  `rhs` will be returned.
- `! value` will return the negated value of `value` converted into a boolean

Some examples for logical operations in AQL:

    u.age > 15 && u.address.city != ""
    true || false
    ! u.isInvalid
    1 || ! 0

Older versions of ArangoDB required the operands of all logical operators to
be boolean values and failed when non-boolean values were passed into the 
operators. Additionally, the result of any logical operation always was a
boolean value.

This behavior has changed in ArangoDB 2.3. Passing non-boolean values to a
logical operator is now allowed. Any-non boolean operands will be casted
to boolean implicitly by the operator, without making the query abort.

The *conversion to a boolean value* works as follows:
- `null` will be converted to `false`
- boolean values remain unchanged
- all numbers unequal to zero are `true`, zero is `false`
- the empty string is `false`, all other strings are `true`
- arrays (`[ ]`) and objects / documents (`{ }`) are `true`, regardless of their contents

The result of *logical and* and *logical or* operations can now have any data 
type and is not necessarily a boolean value.

For example, the following logical operations will return a boolean values:

    25 > 1 && 42 != 7                          // true
    22 IN [ 23, 42 ] || 23 NOT IN [ 22, 7 ]    // true
    25 != 25                                   // false

whereas the following logical operations will not return boolean values:

    1 || 7                                     // 1
    null || "foo"                              // "foo"
    null && true                               // null
    true && 23                                 // 23

   
#### Arithmetic operators

Arithmetic operators perform an arithmetic operation on two numeric
operands. The result of an arithmetic operation is again a numeric value.
Operators are supported.

AQL supports the following arithmetic operators:

- `+` addition
- `-` subtraction
- `*` multiplication
- `/` division
- `%` modulus

The unary plus and unary minus are supported as well.

Some example arithmetic operations:

    1 + 1
    33 - 99
    12.4 * 4.5
    13.0 / 0.1
    23 % 7
    -15
    +9.99

The arithmetic operators accept operands of any type. This behavior has changed in 
ArangoDB 2.3. Passing non-numeric values to an arithmetic operator is now allow.
Any-non numeric operands will be casted to numbers implicitly by the operator,
without making the query abort. 

The *conversion to a numeric value* works as follows:
- `null` will be converted to `0`
- `false` will be converted to `0`, true will be converted to `1`
- a valid numeric value remains unchanged, but NaN and Infinity will be converted to `null`
- string values are converted to a number if they contain a valid string representation
  of a number. Any whitespace at the start or the end of the string is ignored. Strings
  with any other contents are converted to `null`
- an empty array is converted to `0`, an array with one member is converted to the numeric
  representation of its sole member. Arrays with more members are converted 
  to `null`
- objects / documents are converted to `null`

If the conversion to a number produces a value of `null` for one of the operands,
the result of the whole arithmetic operation will also be `null`. An arithmetic operation
that produces an invalid value, such as `1 / 0` will also produce a value of `null`.

Here are a few examples:

    1 + "a"                 // null
    1 + "99"                // 100
    1 + null                // 1
    null + 1                // 1
    3 + [ ]                 // 3
    24 + [ 2 ]              // 26
    24 + [ 2, 4 ]           // null
    25 - null               // 25
    17 - true               // 16
    23 * { }                // null
    5 * [ 7 ]               // 35
    24 / "12"               // 2
    1 / 0                   // null


#### Ternary operator

AQL also supports a ternary operator that can be used for conditional
evaluation. The ternary operator expects a boolean condition as its first
operand, and it returns the result of the second operand if the condition
evaluates to true, and the third operand otherwise.

*Examples*

    u.age > 15 || u.active == true ? u.userId : null

#### Range operator

AQL supports expressing simple numeric ranges with the `..` operator.
This operator can be used to easily iterate over a sequence of numeric
values.    

The `..` operator will produce an array of values in the defined range, with 
both bounding values included.

*Examples*

    2010..2013

will produce the following result:

    [ 2010, 2011, 2012, 2013 ]

#### Operator precedence

The operator precedence in AQL is similar as in other familiar languages (lowest precedence first):

- `? :` ternary operator
- `||` logical or
- `&&` logical and
- `==`, `!=` equality and inequality
- `IN` in operator
- `<`, `<=`, `>=`, `>` less than, less equal,
  greater equal, greater than
- `+`, `-` addition, subtraction
- `*`, `/`, `%` multiplication, division, modulus
- `!`, `+`, `-` logical negation, unary plus, unary minus
- `[*]` expansion
- `()` function call
- `.` member access
- `[]` indexed value access

The parentheses `(` and `)` can be used to enforce a different operator
evaluation order.
