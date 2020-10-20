# The Auto and Driver Objects

DGD has two particularly special objects.

One is the Driver object - this is the object that DGD will notify with events like network input or objects being recompiled. It receives function calls from the DGD interpreter, which DGD calls "applies."

The other is the Auto object. Auto is the object that every DGD object automatically inherits from. A function or a variable in Auto is inherited by every LPC object in your application.

DGD allows you to pick any objects as Driver and Auto. The Kernel Library (and/or Cloud Server) specifies its own Auto and Driver but allows you significant customisation by calling to handlers you can register.

## The Driver

The Cloud Server (or Kernel Library) driver is in kernel/sys/driver. It accepts a number of calls from DGD, and can accept calls from other sources as well.

As an example, inherit_program is called by DGD when one program attempts to inherit another. That allows the Cloud Server driver to prevent inheritance of parents without /lib/ in their path.

## Auto

There is a single library object that every object automatically inherits &mdash; except the driver. That object is called the "Auto" object. The Cloud Server's Auto object is at /kernel/lib/auto.

The Auto object can mask DGD's kfuns, which allows it to determine what other objects get access to any of those capabilities. This is important for a permissions system. That's one thing the Cloud Server does with its Auto object, for instance. It also allows you to have "standard library" functions that are available everywhere, in all objects. Be careful adding too many of them since literally every object will have any data fields or functions that you put into the Auto object.
