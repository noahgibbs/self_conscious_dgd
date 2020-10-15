# Inheritance

Object inheriting from other objects can be a bit complicated. Let's talk about how it works.

## Limitations Due to Persistence

In the Cloud Server, a base class is always an ***abstract*** base class. To put that another way, if you can inherit from an object then you can't clone (instantiate) it. You're also not allowed to get a reference to the object or call functions on it directly. That's usually allowed in DGD. You can do it with cloneable objects, but not inheritable objects.

That's because objects can hold data fields in the Cloud Server. DGD wants parent classes to always be easy to recompile any time. And that would destroy whatever data they held. So: they're abstract base classes and hold no data, so they can be recompiled at any time.

That's not to say there are no interesting problems with recompiling: if an base class is recompiled, abstract or not, then all the child objects that inherited from it can also change. You'll learn more about that when you read about the Object Manager.

## How Does It Tell?

If an object has /lib/ in its path then it can be inherited, but not cloned. If an object has /obj/ but not /lib/ in its path, then it can be cloned but not inherited. If it has neither of those, there are other types of object it might be. See [Object Types](11_ObjectTypes.md) for details.

## Inheritance Statements

DGD has a pretty strict idea of which statements happen in what order. Inherit statements happen very early on (see Program Structure.)

An inherit statement uses the word "inherit." Optionally it can start with "private" or have a scope name. It has to say what object is being inherited.

Here's what that looks like:

```
inherit access API_ACCESS;
private inherit /lib/string;
private inherit tls /kernel/lib/api/tls;
```

## Constructors

By default, DGD calls create() on a newly-used object, often right after it's created. But that doesn't necessarily handle everything when it comes to inheritance.

If your object inherits from more that one parent, for instance, you may have more than one create() method that wants calling. You'll need to write that into your create() function.

Why not do it automatically? Partly it's hard to be sure that "call all the create methods first" is the right order. By having you call them manually, DGD makes sure you have the opportunity to do things both before and after an inherited class's create, and that you can decide what order to call create() in the various inherited parents.

If you inherit from three or four different parents, that's a great reason to give each of them a name in the inherit statement. That makes it easier to call each one's create in turn:

```
inherit access API_ACCESS;
inherit rsrc API_RSRC;

static void create(void) {
    access::create();
    rsrc::create();
    /* ... */
}
```

