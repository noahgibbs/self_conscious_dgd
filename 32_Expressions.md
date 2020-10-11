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

Relational operators return 1 or 0 for true or false. Logical operators take true or false values and do what you're used to - true && true is true, false || true is true, false && true is false, etc.

You can play funny games with the operator precedence here &mdash; "and" logical operators bind tighter than "or", for instance. The comparison operators like <= bind tigher than either one. That lets you write some common logical idioms (the or-of-ands notation for instance) very easily, while others need more parentheses than you might expect. When in doubt, add parentheses.

## Type Conversions

You can put the name of a type into parentheses in front of a value to ask DGD to convert it to another type. For instance, `(int)37.1` will give you the integer 37.

Be careful - rounding floats, in particular, will round them to ***the closest integer***, so rounding `(int)37.6` will actually give you 38, not 37. This is probably not what you're used to in other languages.

Obviously you can typecast between float and int pretty freely.

You can't typecast a nil value into anything non-nil. You can't cast an array or mapping to a number or string.

You can cast an int or float to a string.

When in doubt, experiment. But when in doubt, also assume DGD is fairly fussy about typecasts. It won't turn something into something else randomly. And it will nearly never change type "randomly" without being asked, even between ints and floats.

## Increment and Decrement Operators

DGD implements the C-style ++ and -- operators for increment (add one) and decrement (subtract one), respectively. You can put them before or after a variable name to change the value of the variable.

```
++count; /* Add one to the value of count */
--count; /* Subtract one from the value of count */
count++; /* Add one to the value of count */
count--; /* Subtract one from the value of count */
```

These operators work on both integer and floating-point variables.

## Bitwise Operators

You may be used to C-style (and/or JavaScript-style, Ruby-style, Java-style, Python-style...) bitwise operators which treat an integer as an array of bits and operate on each bit, or each pair of bits.

If not, I'd recommend [looking them up to get a feel for them](https://en.wikipedia.org/wiki/Bitwise_operation). DGD, like many languages, implements them.

They include:

* Binary bitwise "and" - &
* Binary bitwise "or" - |
* Binary bitwise "xor" - ^

## Assignment Operators and Expressions

DGD does, of course, let you assign to variables. The assignment also returns a value so that you can do multiple assignment. For instance:

```
# code { int a, b; a = b = 14; return a + b; }
$11 = 28
```

Another common idiom is to assign to a variable and perform an "if" clause it's true:

```
int error_code;

if (error_code = my_function()) {
    error("This function returned an error!");
}

/* ...otherwise do this... */
```

## Conditional Expressions

DGD has the ternary (three-argument) conditional, often called the "question-mark-colon operator." It's basically a compressed if/then/else statement.

For example:

```
# code (1 == 0) ? "sam" : "bob"
$10 = "bob"
```

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
