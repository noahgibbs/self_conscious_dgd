# Setting Up an LPC Playground

In this chapter we'll make an LPC playground based on the Kernel Library. You'll learn the very basics of LPC syntax and be able to begin playing with important DGD concepts.

## Getting Started: Making a DGD Application

I'll assume you've already got a functioning DGD installation. I'll also assume you have DGD and the Kernel Library cloned. See "Installing DGD" if you don't.

So let's look at how to run some DGD code.

Make a new directory for your new application. I'll call mine lpc_playground.

    mkdir -p lpc_playground/root lpc_playground/state
    cd lpc_playground
    ln -s ../../kernellib/src/kernel root/kernel   # Instead of symlinking, you can copy this
    ln -s ../../kernellib/src/include root/include  # Instead of symlinking, you can copy this
    cp ../kernellib/kernel.dgd ./start.dgd
    mkdir -p root/usr/System

Also, you'll want to edit start.dgd to change "directory" to "root":

    vi start.dgd

There's obviously a lot more to the DGD configuration file. In a later chapter we'll explore the details.

Once you've done these things, you'll have a bootable app that does nothing. Let's make it do something simple, just to be sure it works.

You'll need to add a file called "usr/System/initd.c" using your text editor of choice.

Initially, put this into the file:

```
void create(void) {
  write_file("/log.txt", "DGD started up successfully!\n");
}
```

Now run your new micro-app:

    dgd start.dgd

Hit control-c to stop it again, and check for a new file: root/log.txt. Inside it should say "DGD started up successfully."

You've now written your first little "hello, world" bit of DGD code. Luckily a lot of the above was just boilerplate and things will get easier from here.

If you find things you wrote running together, watch that "\\n" above carefully. It's a newline, to start a new line. If you're used to a language that uses "puts" or "println" to end a line for you, it's the same idea. But the "\\n" starts a new line instead. You can use it in multiple places if you want more than one line.

## Connecting

If you already shut down your DGD app, run it again. If you're using Linux or Mac, type the following in a new console:

    telnet localhost 6047

(If you're using Windows, instead run PuTTY and connect to localhost on port 6047.)

You should see a login console. Give the name "admin" and set up a new password of your choice. You can type "quit" to disconnect. There are a few commends like "cd" and "pwd" and "ls" that make this feel a bit like a terminal. You can play around a bit if you like. There's no useful help here, so it's going to be hard to get much done without reading more, or coding more in LPC.

But keep in mind that when DGD is running, it's waiting for new incoming connections. That's a lot of what DGD is actually ***for***.

Go ahead and type "quit" to log out.

## Variables and Expressions

Like most programming languages, DGD has variables that hold values, and expressions that calculate those values. DGD also has a simple type system. If you want to do a tiny bit of calculation, you can use them roughly like you're used to.

In initd.c:

```
void create(void) {
  string str_value;
  str_value = "foo" + "bar";
  write_file("/log.txt", "DGD made a string: " + str_value + "\n");
}
```

There are a few interesting things to notice here. First, you'll need to restart DGD to make this work. DGD does ***not*** automatically reload this file for you. There's more to the whole "persistence" thing than just changing the file and waiting.

Second: if you look in log.txt after you run this, the first line is still there and now there's a new one. So write_file will append by default, not replace. That's good to know.

Also, we didn't just say `string str_value = "foo" + "bar"`. And in fact, that wouldn't have been allowed. DGD uses syntax that is a lot like the C programming language, which wasn't big on that sort of thing. There's a lot of declaring your types and variables and things ahead of time.

## A Miniature REPL

If you'd like to start playing with LPC code, DGD has a (very) simple REPL. For non-dynamic-language folks, that means there's a way to type code and immediately see the result of it.

Remember how you connected earlier? Do that again, still as "admin".

Once you're at the command line, type:

    code "foo" + "bar"

You should see something like this:

```
# code "foo" + "bar"
$0 = "foobar"
#
```

`code` is the command in the default Kernel Library to evaluate a piece of code and see what it does. It can be dangerous to run random code, of course. But if you're the admin user, DGD assumes you know what you're doing. Luckily, DGD makes it difficult for your code to do anything outside DGD's own process and its own area of files. So at most you could mess up DGD's own data, and there's not much of that yet.

Now let's learn a bit more:

```
# code 17 + 25.3
/usr/admin/_code, 10: bad argument types for + (int, float)
Error: Failed to compile "/usr/admin/_code".
```

Huh! Looks like DGD won't add an integer to a floating-point number. That's good to know. We could fix that problem in a few different ways:

```
# code 17.0 + 25.3
$2 = 42.3
# code (float)17 + 25.3
$3 = 42.3
```

That second one uses the word "float" in parentheses. That construct is called a "typecast" and you may already be familiar with it, depending what languages you're used to. It means "try to turn whatever follows this into a float." You might say, "what if you try to turn a float into an integer? What would happen to the extra?"

And I'd say, "you have a REPL now. You can go and find out."

One unexpected-to-most example is what happens if you round something like 3.7 to an int. Go try it out.

Type "quit" when you're done to log out.

## Longer Code

A REPL for one-liners can be fun. But it's not great for writing longer functions. How can you write longer functions?

You can build all sorts of things for that purpose. But right now you want something simple and immediate that works easily.

You'd also like to be able to do console output without writing anything specific to do that. Here's what we'll do for these tutorial chapters. It's a bad idea for a real production app, but that's fine.

We'll make an object under /usr/System and compile it. That will run its "create()" function.

For instance, go ahead and create an object called root/usr/System/obj/example.c. Here's what we'll put in it:

```
void create(varargs int clone)
{
    this_user()->message("Hello, me!\n");
}
```

Now you'll need to change into that directory. Type `cd /usr/System/obj`.

Now that you're in the right directory, you can compile this object and run the code:

```
# cd /usr/System/obj
/usr/System/obj
# compile example.c
$20 = </usr/System/obj/example>
# clone /usr/System/obj/example
Hello, me!
$21 = </usr/System/obj/example#33>
```

You don't have to cd into the directory every time. But unfortunately, you ***will*** need to compile the object every time you change the code. And you'll have to clone it every time to run it. There are other ways to do this, but they're about equally awkward.

Aren't there better ways? Definitely. But they all involve writing more code. We'll talk more about that later.

For now we'll do it this way.

## Future Notes

This tutorial section should be a lot more extensive. We should set up some kind of more reasonable code reloading. We should use more of DGD's features.

That's a lot of the value of this section. But it also needs solid reference chapters to back it up. So I'll write those, then come back here and do this right.
