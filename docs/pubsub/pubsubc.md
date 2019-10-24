# The design of Robust PUB - SUB agents with C

Implementation of Wireless communication in aerospace industry to reduce the wiring is everyone's idea and many papers present what should be used for communication but I found very few papers on how and what patterns of communications must the avionics intra-communications should follow.

So I start respresenting few models, some designed according to my idealogy and some patterns which are inspired by Distributed Systems.

## The PUB-SUB model (publisher-subscriber pattern)

The pattern that I am demonstrating is not just a typical `publisher-subscriber` pattern but programs built with `reliability`, `scalability` and `flexibility` in mind.

## Features of publisher

> (1) Publisher publishes messages or updates to its subscribers.

> (2) Publisher has the ability to publish `n` number of messages with different topics.

> (3) A single publisher can publish it's updates to few thousands or few hundred thousand subscribers.

> (4) If disconnected, the publisher automatically reconnects and starts publishing the updates to its subscribers.

> (5) A broker can be implemented between `publisher` and `subscriber` to perform time critical operations such as invoking `RPC's`, `control systems` etc.

Consider the following block diagram which demonstrates the flow.

![publisher image](/images/pub1.svg)

