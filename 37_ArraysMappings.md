# Arrays and Mappings

DGD has built-in array and mapping (hash) types. That's nice!

Arrays use a braces-in-parens syntax:

```
# code ({ 1, 2, 3 })
$9 = ({ 1, 2, 3 })
```

Mappings use square-braces-in-parens, with colons separating the keys from the values:

```
# code ([ 1: 2 ])
$4 = ([ 1:2 ])
# code ([ "a": 2 ])
$5 = ([ "a":2 ])
```

Note that both integers and strings work fine here as keys for a mapping. You can use all sorts of DGD objects as keys but strings and integers are often the easiest and best choices.

## Writing Types

Mappings have a simple enough type: "mapping". Arrays can be more complicated, particularly when combined with other types.

LPC inherits its array type syntax from C. That means "array of" is normally expressed with an asterisk (star) character. Multiple asterisks mean multiple layers of "array of." For instance "int \*" means "array of int" while "int \*\*" means "array of array of int."

The trick is to remember that LPC is a dynamic language with bounds checking, much like Java or JavaScript. It only ***looks*** like C in these cases. And further, remember that an array is an object much like a mapping, initialised to nil:

```
# code { int **a; a = allocate(5); a[0] = allocate_int(3); return a; }
$18 = ({ ({ 0, 0, 0 }), nil, nil, nil, nil })
```

In that code, "int\*\*" means "array of arrays of integer." The top-level allocation (`a = allocate(5)`) is creating an array of 5 arrays, initially nil. Then the first one is assigned its own list of 3 integers. With a for loop or similar, you could allocate a block of 10 arrays of 50 integers each or whatever you please.

Note that in an array of arrays of integers, they aren't required to all be the same length, or all be non-nil at the same time. You can mix and match. If you intend your array to hold a variety of different values, you may want a "mixed \*", where each element can be any DGD-legal value in any combination. A "mixed \*" means approximately "an array of any combination of elements."

DGD inherits C's unintuitive syntax for declaring multiple arrays. For instance, the following will declare two integer arrays a and b:

```
int *a, *b;
```

If you instead wrote the obvious thing, `int *a, b;` you would be declaring an integer array called "a" and an integer (***not an array***) called b. By far the easiest way to handle this is to declare each variable on its own line to make everything clear:

```
int *a;
int *b;  /* Clearly an array. */
```

Remember that all variables must be declared at the beginning of their block, which may be the start of a function.

## KFuns

DGD supplies a few built-in functions to deal with arrays and mappings. For arrays, there's "sizeof". This is unrelated to the C compile-time operation. It dynamically, at runtime, returns the current size of the array.

```
# code sizeof( ({ 1, 2, 3 }) )
$5 = 3
```

There are also mapping functions to return the size, indices and values of the mapping, called respectively map_sizeof, map_indices and map_values.

```
# code map_sizeof( ([ "b": 3, "c": 7 ]) )
$6 = 2
# code map_indices( ([ "b": 3, "c": 7 ]) )
$7 = ({ "b", "c" })
# code map_values( ([ "b": 3, "c": 7 ]) )
$8 = ({ 3, 7 })
```

There are also built-in functions to create arrays:

```
# code allocate(4)
$13 = ({ nil, nil, nil, nil })
# code allocate_int(7)
$14 = ({ 0, 0, 0, 0, 0, 0, 0 })
# code allocate_float(5)
$15 = ({ 0.0, 0.0, 0.0, 0.0, 0.0 })
```

You'll note there's no allocate_object, allocate_mixed, allocate_mapping, etc. Allocate is divided by initial value, not by allowed type. You can assign an array of nil to various types:

```
# code { object *a; a = allocate(5); return a; }
$16 = ({ nil, nil, nil, nil, nil })
# code { mapping *a; a = allocate(7); return a; }
$17 = ({ nil, nil, nil, nil, nil, nil, nil })
```

## Equality Operators

DGD's basic equality operator checks whether two arrays or two mappings point to the same exact object. It does ***not*** check whether they contain equal elements. In other words:

```
# code ({ }) == ({ })
$0 = 0
# code ({ 1, 2, 3 }) == ({ 1, 2, 3 })
$1 = 0
# code { a = ({ }); return a == a; }
$2 = 1
```

So two empty arrays aren't equal to each other. Two arrays with the same elements aren't equal to each other. But two references to the same array are equal to each other.

The same rules apply for mappings:

```
# code ([ ]) == ([ ])
$6 = 0
# code ([ 1: 2, 3: 4 ]) == ([ 1: 2, 3: 4 ])
$7 = 0
# code { a = ([ 1: 2 ]); return a == a; }
$8 = 1
```

## Set-Operators on Arrays

Arrays allow you to perform set-unions and set-intersections on them, using the \| and \& operators respectively.

```
# code ({ 1, 2, 3 }) & ({ 2, 5, 7 })
$9 = ({ 2 })
# code ({ 1, 2, 3 }) | ({ 2, 5, 7 })
$10 = ({ 1, 2, 3, 5, 7 })
```

Mappings don't have the same operators defined, but keep in mind that you can get the indices of the mappings and perform unions and intersections if that's useful.

## Mapping Operators

You can add two mappings. The keys of the second will, in effect, be added to the keys of the first:

```
# code ([ "a": 1, "b": 2 ]) + ([ "b": 3, "c": 7 ])
$2 = ([ "a":1, "b":3, "c":7 ])
```

You can't subtract two mappings from each other. But if you subtract an array from the mapping, it will be treated as an array of keys:

```
# code ([ "a": 1, "b": 2 ]) - ([ "b": 3, "c": 7 ])
/usr/admin/_code, 10: bad argument types for - (mapping, mapping)
Error: Failed to compile "/usr/admin/_code".
# code ([ "a": 1, "b": 2 ]) - ({ "b" })
$3 = ([ "a":1 ])
```

## Adding to Mappings

The most straightforward way to add to a mapping is to assign to a new element.

```
# code { mapping a; a = ([ "jim": 1, "fred": 2, "sam": 3 ]); a["john"] = 9; return a; }
$19 = ([ "fred":2, "jim":1, "john":9, "sam":3 ])
```

You can also add two mappings with the "+" operator, or add into a mapping with "+=". Remember that you can't have multiple values for the same key. If there are duplicate keys, one will overwrite the other.

## Adding to Arrays

You can use the += operator to add a new item to the end of an existing array.

```
# code { int *a; a = allocate_int(4); a += ({ 9, 8, 1 }); return a; }
$20 = ({ 0, 0, 0, 0, 9, 8, 1 })
```

The + operator will concatenate two arrays. It's also possible to allocate a very long array using the allocate() family of kfuns and copy the shorter array over top of it with a for() loop.

## Subtracting from Arrays and Mappings

If you assign nil to an item in a mapping, it will be deleted:

```
# code { mapping a; a = ([ "a": 1, "b": 2 ]); a["a"] = nil; return a; }
$11 = ([ "b":2 ])
```

You can also subtract an array from the mapping, containing the key or keys to be deleted.

## Array-Slices

In DGD, as in many languages, you can take a "slice" of an array -- a shorter array from inside a longer array. For instance, the array-slice `arr[1..4]` grabs the second through fifth elements of the array, assuming all of them exist. You can also leave off the first and/or last index to start from the beginning or end of the list.

DGD does ***not*** use negative offsets to count from the end of the list. For instance, -1 does not mean the index at the end of the array, but instead means one index before the beginning of the array. That element is, of course, not present.

The array-slice is a copy of the array. Using neither a beginning nor end index in an array-slice is thus a simple idiom for "make me a copy of this array." The elements inside the array aren't copied &mdash; that is, it's a "shallow copy," not a "deep copy."

```
# code { int *a; a = ({ 6, 7, 8, 9, 10, 11 }); return a[0..1]; }
$0 = ({ 6, 7 })
# code { int *a; a = ({ 6, 7, 8, 9, 10, 11 }); return a[0..-1]; }
$1 = ({ })
# code { int *a; a = ({ 6, 7, 8, 9, 10, 11 }); return a[2..]; }
$2 = ({ 8, 9, 10, 11 })
# code { int *a; a = ({ 6, 7, 8, 9, 10, 11 }); return a[..5]; }
$3 = ({ 6, 7, 8, 9, 10, 11 })
# code { int *a; a = ({ 6, 7, 8, 9, 10, 11 }); return a[..]; }
$4 = ({ 6, 7, 8, 9, 10, 11 })
```

## Mapping-Slices

You can take slices of mappings, too. This is most obviously useful when you use integers as your keys:

```
# code { mapping a; a = ([ 6: 7, 8: 9, 10: 11, 12: 13 ]); return a[..]; }
$5 = ([ 6:7, 8:9, 10:11, 12:13 ])
# code { mapping a; a = ([ 6: 7, 8: 9, 10: 11, 12: 13 ]); return a[ 6..8]; }
$6 = ([ 6:7, 8:9 ])
# code { mapping a; a = ([ 6: 7, 8: 9, 10: 11, 12: 13 ]); return a[ 6..10 ]; }
$7 = ([ 6:7, 8:9, 10:11 ])
```

The slice checks the values of the keys, not the order in the code:

```
# code { mapping a; a = ([ 10: 11,12: 13, 6: 7, 8: 9 ]); return a[ 6..10 ]; }
$9 = ([ 6:7, 8:9, 10:11 ])
```

Notice that the same idiom for "make a copy" works with mappings as arrays.

You can also use string keys and take slices of your mappings:

```
# code { mapping a; a = ([ "a": 1, "apple": 2, "aardvark": 3, "b": 4, "biscuit": 5, "c": 6, "zebra": 100 ]); return a["apple".."biscuit"]; }
$8 = ([ "apple":2, "b":4, "biscuit":5 ])
```

If you use more complex keys like objects or mappings, I wouldn't normally suggest using slices except to make a copy. Ordering is easiest for integers and strings. Remember that you can use the code command to check things like string ordering:

```
# code "bob" < "b"
$10 = 0
```

## Keys in Mappings

You can technically use other objects, including mappings, as keys for a mapping. But because of equality rules (see [Equality Operators](#Equality%20Operators)) you would need to make sure you used ***exactly the same mapping object*** to retrieve the value, not just one with the same things in it. In other words, if you used the empty mapping as a key, you couldn't just dereference with an empty mapping. You'd have to use ***the same mapping object***:

```
# code ([ ([ ]): 1 ])[([ ])]
$11 = nil
# code { mapping a; a = ([ ]); return ([ a: 1 ])[a]; }
$12 = 1
```

That limits the value of a mapping as the key to another mapping for most uses. Similarly you can use objects as keys in a mapping. That's often more useful since you don't normally instantiate new empty objects as values in the way you might with an array or a mapping:

```
# code { object a; a = find_object("/usr/System/initd"); return ([ a: 1 ]); }
$14 = ([ </usr/System/initd>:1 ])
```

Of course, object names are unique. So you could also use the object's name as the key in the mapping, which is likely to be easier to read as debugging output.

## Size Limits

Both arrays and mappings have limited sizes. That means you can't use them for arrays or mappings that will grow enormous.

You can get the maximum mapping or array size from the status() object. Note that it may be different for copies of DGD with different compile-time settings, and it can be set to a lower-than-compiled value in DGD's configuration file.

```
# code status()[ST_ARRAYSIZE]
$1 = 32767
```

If you need to store a larger array or mapping than is allowed by the compile-time limit, it's possible to make an object or LWO serve that purpose. You can see a similar trick done for strings in the Cloud Server's lib/string library.

The idea, if you need it, is that if a single mapping can only have 32,000 items, nothing stops you from making a mapping-of-mappings to get far more. The same trick works with an array-of-arrays, mapping-of-arrays, etc.

Ordinarily you're better off just using normal mappings and arrays until you need the larger object with more complexity.
