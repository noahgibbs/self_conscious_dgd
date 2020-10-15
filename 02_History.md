# Where Did DGD Come From?

The original LPMud and its LPC language date back to 1989. The very first MUD was MUD1, back in 1978.

Dworkin's Game Driver (DGD) is a comparative newcomer, dating back to 1993. It was a reimplementation from scratch of LPMud. Like several LPMud derivatives, it then diverged rapidly and became mostly incompatible. There was always a difficulty in writing text games: do you add a lot of powerful functionality that isn't present in other similar games and give yourself an advantage? Do you try to improve your base language layer to be more powerful and useful? Or do you try to retain compatibility?

DGD's particular innovation was to treat LPC as a generic language more than a game language. Game-specific ideas like characters, locations and inventories got removed from the standard library, while more familiar general-purpose programming-language ideas like hooks, callbacks and encryption got added.

## MUDs and Commercial Use

There have been a few traditional-style LPMuds built on DGD over the years, most prominently one called Xyllomer. Since they never used a DGD-specific layer like the Kernel Library or Cloud Server, they were unable to use some of DGD's most powerful features, like full recompile-in-place persistence.

There have also been a few experimental DGD-specific game libraries such as my own (now long-defunct) Phantasmal MUDLib. None of them ever got used for a game with more users than you have fingers as far as I know.

Basically, DGD had interesting and powerful features that made it theoretically appealing to developers, but very little documentation and no easy on-ramps.

DGD spent awhile being "free for noncommercial use, licenseable for commercial use." It went quite poorly. For awhile you could buy a commercial license for your MUD for DGD for US $100. Given the power and complexity of DGD as a tool, that's ludicrously cheap.

Nobody bought one. Ever.

DGD's author (Felix Croes, a.k.a. Dworkin) then gave an exclusive commercial license for DGD to an external company. They sold a few licenses &mdash; I personally know of four. Years later they went out of business and Dworkin gave up the sublicense in disgust. At this point there is an extremely DGD-like "other" product, sharing nearly all of DGD's source code, called Hydra, released under AGPL. Hydra is basically a renamed and expanded DGD that does ***not*** have any commercial license encumbrances with some random other company. It's fully backward-compatible. Everything you learn here about DGD applies to Hydra as well.

## SkotOS.net

That's not to say that nobody ***used*** their commercial license. SkotOS.net was a company that decided: let's make money off commercial text games. We'll make MUDs with very high production values and it'll make a ton of money.

The ton of money never materialised. I'm sure you're shocked. But the remains of SkotOS is still out there, and these days is open source as well under the name "ChatTheatre." It remains one of the most advanced text games ever made, a title which has few serious contenders.

Dworkin eventually decided to handle licensing more like how other languages do. Since 2010, DGD and Hydra use the AGPL. Does that mean you have to distribute the full code to your DGD app is somebody connects to the server? Not according to Dworkin. He says that a DGD interpreter running your source code doesn't count as "linking."

## Weird Stuff

DGD has also been used for iChat, which became Yahoo! Chatrooms. There was one Bitcoin exchange built in DGD, once. Geir Harald Hanssen [wrote his thesis about DGD](https://geir-hansen.com/distributedworld.pdf).
