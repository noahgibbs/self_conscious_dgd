# Introduction

You may have heard of Doug Engelbart, and the Mother of All Demos. Just in case you haven't: there was a single demo at Xerox PARC where he introduced the idea of a mouse for the computer, hyperlinks, instant messages, videoconferencing, real-time document collaboration, even basic stuff like undo and copy-and-paste... All for the first time. All in one go. In 1968. This was long before any of this stuff would become standard. It was well before the first personal computer.

You can see how some of those features work well together. Cut-and-paste would be hard to use without a mouse. Instant messages, videoconferencing and real-time document collaboration actually came out of an idea like hyperlinks: you could have stuff on one computer displayed on another.

Why does it matter that some of these go together? Because they come from the same sensibilities. The same person would be likely to figure some of these same things out. Engelbart was thinking in text UI and networks. If you had somebody who was thinking about user interfaces instead then you might have gotten, oh, let's say the windowed desktop interface, MVC application architecture, eye tracking, some kind of scheme for what icons should look like and how to use subtle animations all through an interface for legibility.

There have been a lot of fun demos over the years, and most of them didn't come to much. What's so weird about Engelbart's demo is that it all got popular. So we can all see the real thing and say, "wow, they figured all this out in 1968!"

What would computing be like if one of those ***other*** early demos had come true? What if we had gotten electronic pens like a Wacom tablet earlier instead of the mouse? The pen interface is pretty lousy for highlight-the-text cut and paste. And we'd probably have a lot more drawing-based interfaces.

What would computing look like in a world where a different set of ideas won?

And on that note, let's talk about DGD.

I enjoy dynamic, grandiose book titles. If you wrote a book called "C++ for the Upstart Philosopher-King" I'd read it, you know? Or "Ruby That Makes You a Super-Genius." Neither of those are actual good book concepts. But it's not like "Learn You a Haskell for Great Good" is all about the title. A title is meant to get your attention.

And then I realised: here I am writing a book about an ancient dialect of LPC, the language of LPMuds. LPMuds are this ancient text-based MMO from before anybody had fast CPUs or a decent network. Few people know what an LPMud ***even was***. To those few they're kind of a nostalgic joke. And so if I pick a really fancy title, it's gonna be embarrassing all around.

So: welcome to Self-Conscious DGD.

DGD is a weird dynamic language. It's visionary enough that it went from "ahead of its time" to "what the hell is that?" with no intervening "popular" stage. DGD is like the Engelbart demo but most of its vision never happened.

Imagine a different reality, where programming languages worked completely differently. Imagine some of the early choices were made in a different direction, and this completely different reality grew up around them.

DGD is a tiny glimpse into that reality. It has some parallels to our own world &mdash; history doesn't repeat, but it rhymes. But if you ask, "will this get me a job?" the answer is "only in an alternate reality." It's a fun alternate reality, though.

Mostly I'm gonna play this book straight. Let's talk about how you'd actually write stuff in DGD. After all, DGD does some things that are ***still*** ahead of its time. I mean, assuming anybody ever figures out how to steal them for a more popular language. We didn't know if it would ever happen with Lisp Macros and it did, right? And Ruby has a few of DGD's better dynamic features. Somebody will steal the rest.

If you learn DGD before that, you can be one of those smug, annoying old programmers who says "oh yeah, I knew about that feature back when all the ways to use it were awful. Let me show you how it's done."

You know Javascript prototype inheritance? LPC was doing it before the rest of the world. Dynamic code reloading? DGD did it before Rails made it common. It had an extensive type-based directory structure before Java and Rails stole that trick. DGD still has a few tricks yet to be stolen. Atomic functions? Somebody will steal them. Caller- and object-name-based security? It'll happen. Full twenty-year-plus persistence? DGD's not the only contender, but nobody's ever made it popular.

You can be the smug grognard for ***those*** features... If you learn now.

## Relatives

DGD's vision can't be be entirely unique. It uses older ideas, and new languages come along to copy old features every year. What are its relatives?

Its unusual execution model has a lot in common with Node.js, and a little in common with in-browser JavaScript. Mostly it's an evented system with tight limits on how long each event handler can take. In that sense it's also a lot like hardware interrupt handling from back when DGD was written.

Its sandboxing is a bit JavaScript-like. Its self-sandboxing through rewriting and recompilation is a bit like Ruby and a bit like CommonLISP macros.

Its limited data-type sizes and careful resource-counting isn't much like most general-purpose languages. If you've used SalesForce's Apex environment, it's a bit like that. Counting resources at a very granular level is uncommon, and not many languages do it like DGD.

Its atomic functions are more like database transactions than anything else. So in that sense it's a bit like SQL. Though again, nobody really does it the same way. Similarly, using a transaction-like method for parallelism through optimistic speculative execution is a thing processors do frequently, and languages nearly never do at all. So in that sense it's a bit like assembly language or machine code.

DGD's Kernel Library has an extremely rigid application structure a bit reminiscent of Ruby on Rails or other modern web applications -- all of which it predates by quite a long time. I don't think they copied DGD. I think it's so common to structure your applications in a vaguely similar way that it just makes sense to bake those assumptions into your framework or language. C-descended languages are, frankly, weird in how willing they are to let you put things in the "wrong" place but still use them. You could also argue that Java namespacing is similar, though I personally disagree.

The Kernel Library's security by checking the call stack and calling objects, then literally using their name, was entirely new to me. It seems like a simple enough idea that *somebody* else must do it, but I have no idea who. Do you know? If so, let me know!
