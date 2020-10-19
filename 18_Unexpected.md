# What's Unexpected?

If you're used to C or JavaScript, what parts of DGD are you likely to trip over a few times before you remember them?

## Integer Typecasts

(NOTE: MOVE THIS) In C or JavaScript, typecasting is constant. JavaScript will usually automatically convert your values for you, though you often wish it wouldn't. DGD is more finicky than JavaScript ***or*** C, and it requires a lot of explicit declarations that you want to turn something into a string before adding it to another string, or convert it to a floating-point number before you take the cosine of it.

An integer typecast in LPC will round to the ***nearest whole number***. Not truncate towards zero &mdash; the ***nearest*** whole number.

```
(int)7.1  /* it's 7, like you'd expect */
(int)7.6  /* it's 8, which isn't how much languages do it */
```

## Illegal Call_outs

If you do a call_out to a function that doesn't exist on another object, or otherwise do a call_out that you shouldn't, the default is that it fails completely silently. There are ways you can add code to try and notice these problems. But mostly, a bad call_out is a silent call_out.

This is used idiomatically sometimes. A call_out to a function that doesn't exist can still do ***something***. For instance the Cloud Server will call the function "???" on an object to make sure it has been "touched" in some cases, that its data fields were allocated and any call_touch handlers have been called on it. Since "???" is not a legal function name, this relies on a failed call_out doing nothing. But it also doesn't want or need to call a real function.
