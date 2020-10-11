# The Auto and Driver Objects

DGD has two particularly special objects.

One is the Driver object - this is the object that DGD will notify with events like network input or objects being recompiled. It receives function calls from the DGD interpreter, which DGD calls "applies."

The other is the Auto object. Auto is the object that every DGD object automatically inherits from. A function or a variable in Auto is inherited by every LPC object in your application.

DGD allows you to pick any objects as Driver and Auto. The Kernel Library specifies its own Auto and Driver, but allows you significant customisation.

## The Driver

