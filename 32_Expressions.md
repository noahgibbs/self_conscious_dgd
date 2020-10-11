# Types, Operators and Expressions

## Variable Names

A DGD identifier, including variable names and labels, starts with a letter or underscore. It can have digits as part of the identifier, but not as the first letter. They're case-sensitive, so "bOb" is different from "bob" or "Bob".

## Data Types

DGD data types include:

* int - a signed integer, usually with a maximum value of 2147483647. May not be nil.
* float - a floating-point number. May not be nil.
* string - a limited-length primitive string object; it can contain characters or the value "nil".
* object - an object, which is allowed to be a parent object, clone or LWO; can also be nil.
* mapping - a hash table, similar to a JavaScript object or Python dictionary; can also be nil.
* mixed - may contain a reference to any sort of data; can be nil.
* void - a variable can't be of type "void", but a function can use "void" to mean "no arguments" or "no return value."

Most variable types can hold a value of "nil", which essentially means "nothing there."

An uninitialised variable defaults to a value of 0, 0.0 or nil, depending on its type.

Array types aren't named as "array", but rather by what type they are an array of followed by one or more asterisks. An "int \*" variable points to an array of integers, while a "mixed \*\*" points to an array of arrays of any type(s) of data.

DGD also has character data, which doesn't get a separate type from "int". But a character constant can be written with single quotes: 'c'.

DGD's include files often include useful information about the types and how they work, such as the smallest representable floating-point number or the number of bits in a character. See limits.h or float.h, for instance.

## Mixed Variables and Typeof

Since a "mixed" variable may contain any of several different types of value, you'll often want to know which one. DGD uses the "typeof" operator for this. Typeof returns an integer value. /include/type.h defines constants like T_NIL, T_STRING and T_MAPPING for those integers.

```
# code typeof(nil)
$0 = 0
# code typeof(nil) == T_NIL
$1 = 1
# code typeof(({ }))
$2 = 5
# code typeof(({ })) == T_ARRAY
$3 = 1
```

## Constants

DGD allows lots of values to be expressed as constants.

### Truthy and Falsy values

DGD's "if" statement treats 0 and nil as false. Nonzero numbers are true.

```
# code if(0) { this_user()->message("Yup!\n"); }
$0 = nil
# code if(1) { this_user()->message("Yup!\n"); }
Yup!
$1 = nil
```

Similarly, the float 0.0 is false, but even very small other floats are true:

```
# code if(0.0)  { this_user()->message("Yup!\n"); }
$5 = nil
# code if(0.000001)  { this_user()->message("Yup!\n"); }
Yup!
$6 = nil
```

It treats arrays and mappings as true, even if they're empty:

```
# code if(({ })) { this_user()->message("Yup!\n"); }
Yup!
$2 = nil
# code if(([ ])) { this_user()->message("Yup!\n"); }
Yup!
$3 = nil
```

Strings, including empty strings, are true. Nil is still false, of course.

In other languages, we often refer to values that count as "true" for if as "truthy" values, and values that don't count as true as "falsy" values. In some languages there is a single falsy value, while DGD has several (0, 0.0 and nil.)

### Integer Constants

DGD allows "ordinary" decimal integer constants like "37". It also allows octal (base-8) constants which begin with a zero and hexadecimal (base-16) constants that begin with "0x" or "0X".

```
# code 17
$4 = 17
# code 017
$5 = 15
# code 031 == 25
$6 = 1
# code 0x21
$7 = 33
```

### Float Constants

You can also use floating-point constants. These can start with a decimal, or be in scientific notation.

```
# code 0.25
$8 = 0.25
# code .73
$9 = 0.73
# code 2.5e-21
$10 = 2.5e-21
```

### String Constants

DGD strings are double-quoted. They can use escaping (put a backslash in front) or character escapes like "\n". For instance:

* "abc" - the three-character string a, b, c
* "ab\n" - a and b followed by a newline
* "ab\\" - a and b followed by a backslash
* "\"" - one double-quote character by itself
* "'" - one single-quote character by itself

These are the special letters that can go after a backslash in an escape sequence:

```
\\a = 007 (bell)
\\b = 010 (backspace)
\\f = 014 (form feed)
\\n = 012 (newline)
\\r = 015 (carriage return)
\\t = 011 (horizontal tab)
\\v = 013 (vertical tab)
```

You can also use a backslash followed by three digits from 0-7 to make an octal escape:

```
# code "\\012"
$16 = "\\n"
```

And you can use a backslash and an x followed by three hexadecimal digits for a hexadecimal character code:

```
# code "\\x00A"
$19 = "\\n"
```

### Character Constants

A character constant is in single-quotes rather than double-quotes. It can use the same escapes that characters in strings can.

```
# code '\x00A'
$20 = 10
```

Keep in mind that a character is basically a restricted integer between 0 and 255. DGD uses ASCII encoding, as do nearly all common programming languages. For example, a capital A is the same as the integer 65:

```
# code 'A'
$22 = 65
```

## Arithmetic Operators

DGD has a normal set of arithmetic operators: +, -, \*, /. It uses % for integer modulus. Generally, operations need to use the same type on both sides &mdash; you can't add a float to an integer.

Integer division truncates. That is, it rounds down toward zero. Since integer typecast rounds to the nearest integer, that can present a few oddities:

```
# code (int)(38 / 10)
$26 = 3
# code (int)(38.0 / 10.0)
$27 = 4
```

## Relational and Logical Operators

There are expected operators like < or >, or <= or >= for numbers. They can also check strings. You can't compare an integer to a float without a typecast.

Relational operators return 1 or 0 for true or false. Logical operators take true or false values and do what you're used to - true && true is true, etc.

## Type Conversions

## Increment and Decrement Operators

## Bitwise Operators

## Assignment Operators and Expressions

## Conditional Expressions

## Precedence and Order of Evaluation

Operator precedence in LPC goes in groups - plus and minus are of equal precedence to each other, as are multiply and divide. Here are the groups, from highest-precedence (binds tightest) to lowest (binds most loosely.)

* simple values including arrays and mappings; parentheses; variable/function names, including Scoped::variables; function calls; catch statements using parentheses; "new" object allocations; arrows for call_other; arrow for type-check
* array dereference, array/mapping slice
* postfix ++ or --
* prefix ++, --, !, \~, +, -
* typecasts
* binary \*, /, %
* binary +, -
* <<, >>
* <, >, <=, >=
* ==, !=
* binary &
* binary ^
* binary |
* binary &&
* binary ||
* ternary ? :
* =, +=, -=, \*=, /=, %=, <<=, >>=, &=, ^=, |=
* multiple expressions separated by commas
