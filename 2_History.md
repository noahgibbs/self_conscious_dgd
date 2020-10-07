# Where Did DGD Come From?

The original LPMud and its LPC language date back to 1989. The very first MUD was MUD1, back in 1978.

Dworkin's Game Driver (DGD) is a comparative newcomer, dating back to 1993. It was a reimplementation from scratch of LPMud. Like several LPMud derivatives, it then diverged rapidly and became mostly incompatible. There was always a difficulty in writing text game drivers: do you add a lot of powerful functionality that isn't present in other similar games and give yourself an advantage? Do you try to improve your base language layer to be more powerful and useful? Or do you try to retain compatibility?

DGD's particular innovation was to treat LPC as a generic language more than a game language. Game-specific ideas like characters, locations and inventories got removed, while more familiar programming-language ideas like hooks, callbacks and arrays got added.

DGD spent awhile being "free for noncommercial use, licenseable for commercial use." It went quite poorly, frankly. DGD's author (Felix Croes, a.k.a. Dworkin) gave an exclusive commercial license for DGD to an external company. They sold a few licenses, but it also went poorly. Years later they went out of business and Dworkin gave up the sublicense in disgust. At this point there is an extremely DGD-like "other" product, sharing nearly all of DGD's source code, called Hydra, released under AGPL. 

To put that another way, Dworkin eventually decided to do this more like how other languages do, and there was an ugly interim where it wasn't.

These days DGD is AGPL. Does that mean you have to distribute the full code to your DGD app is somebody connects to the server? Not according to Dworkin. He says that a DGD interpreter running your source code doesn't count as "linking."
