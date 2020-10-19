# References, Call-By-Value and Call-By-Reference

If you're familiar with "Call by Value" versus "Call by Reference" and several variations like "Call by Name," you already know these things vary a lot from language to language. There are often significant subtleties to how they work in one place versus another.

DGD doesn't make its variation particularly difficult, but it's worth talking about how it works.

## Call by Value or Call by Reference?

DGD passes primitive values by value. "Primitive" values include integers, floats and strings. For instance:

```
# code { string a, b; a = "bobo"; b = a; b[3] = 'c'; return a; }
$11 = "bobo"
# code { string a, b; a = "bobo"; b = a; b[3] = 'c'; return b; }
$12 = "bobc"
```

What you're seeing above is that a and b are separate strings. Assigning to one of them doesn't affect the other. Similarly, if they were integers and you used ++ or += on one of them, it wouldn't affect the other.

Arrays, mappings and objects (including clones and LWOs) are passed by reference:

```
# code { mapping a, b; a = ([ 1: 2, 3: 4, 5: 6 ]); b = a; b[7] = 8; return a; }
$15 = ([ 1:2, 3:4, 5:6, 7:8 ])
```

In the example above, we add a new key to variable b and then return variable a, but the new key is there too. That's because a and b point to the same object instead of making a new object. Primitives are all separate from each other &mdash; your current integer 7 is entirely separate from every other copy of 7 in DGD. But mappings, arrays and objects are references and can point to the same object. You'll notice that when you modify that object and all the variables change.

In the section on array slices I mentioned an idiom to copy an array. You might have thought, "why would I want another copy of an array I already have?" The answer is that you might want to modify your copy or allow somebody else to modify theirs. By making your own copy, you make sure that your modifications don't affect them and theirs don't affect you.

## Shallow and Deep

I said that your number 7 is separate from every other 7. But what's going on here?

```
# code { mapping a, b; a = ([ "a": ({ 7 }) ]); b = a[..]; b["a"][0] = 9; return a; }
$16 = ([ "a":({ 9 }) ])
```

B has its own copy of the mapping. We modified b and returned a, but a's 7 has been changed to 9!

The problem is the array inside the mapping. We copied the mapping but not the array inside it, then we modified the array. That's where we were storing our copy of 7. If we gave B its own copy of the array, that would fix the problem.

A copy of a single data structure is often called a "shallow copy." For instance, we copied the mapping above but just kept a reference to the array inside. If you copy every internal structure including arrays, mappings and objects you get a "deep" copy.

Of course, copying an object is often easier said than done. There's not a standard deep-copy operation in DGD, probably for that exact reason. You could easily write a function that would deep-copy arrays and mappings but ***not*** objects. Deep-copying all possible objects would be much harder. You can use new_object to copy an LWO, but it's a shallow copy.

## Memory Arenas and References

DGD has memory arenas for its objects. All these primitives, arrays, LWOs and so on live inside some first-class DGD object. Remember that references (other than to first-class non-LWO objects) aren't allowed to cross the boundaries of DGD's memory arenas after the current timeslice (task) ends. Instead, they get copied.

If you have an array referenced by four different DGD objects at the end of the timeslice, you'll get four copies of that array for the next timeslice. If it had a mapping inside it, that mapping will be copied too. The only references that will remain references are to first-class non-LWO DGD objects such as clones or cloneables.

## How Much Memory?

If you read about DGD's configuration file, you'll learn that DGD tracks its memory in blocks called "sectors." Each top-level DGD object takes up a certain number of sectors, which lets you determine how much of your limited memory it requires.

How does that work with all these arrays and mappings?

The answer is that all the data held by an object is packed together. You could easily have many integers in a single sector, while a string or mapping could take many sectors by itself.

## Multidimensional Arrays

As you saw in the chapter on arrays and mappings, an array can hold a reference to another array. The same is true of mappings, of course. If you want a matrix (an array of arrays) you'll built it yourself out of those pieces.

For instance here's one way to build a 10x8 array of arrays of integers:

```
# code {
    int **a;
    int i;
    a = allocate(10);
    for(i=0; i < 10; i++) {
        a[i] = allocate_int(8);
    }
    return a;
}
$19 = ({ ({ 0, 0, 0, 0, 0, 0, 0, 0 }), ({ 0, 0, 0, 0, 0, 0, 0, 0 }), ({ 0, 0, 0, 0, 0, 0, 0, 0 }), ({ 0, 0, 0, 0, 0, 0, 0, 0 }), ({ 0, 0, 0, 0, 0, 0, 0, 0 }), ({ 0, 0, 0, 0, 0, 0, 0, 0 }), ({ 0, 0, 0, 0, 0, 0, 0, 0 }), ({ 0, 0, 0, 0, 0, 0, 0, 0 }), ({ 0, 0, 0, 0, 0, 0, 0, 0 }), ({ 0, 0, 0, 0, 0, 0, 0, 0 }) })
```
