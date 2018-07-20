---
title: "Bumping into Things"
category: gamedev
layout: post
---

So as I continue to work on project Lily Tin, I've continued to wrestle with the
one thing that my game needs: collision detection. Almost every game needs it,
in fact. Mario needs to be kept out of walls, Link's sword needs to know if it's
slicing through Moblin flesh, and I need to know when it's OK to hit the ball
with my paddle.

Up until now I've been relying on other people's solutions for this problem.
`if (place_meeting(x, y))` immediately springs to mind. But because I've decided
to do most everything from scratch, things aren't that simple.

So far, building this game's architecture has been relatively straightforward.
The game involves actions being performed in real time, so let's shove a game
loop in `main`. That loop has certain events associated with it, so I'll pass
that over to a `Game` class. Breakout typically has a set of bricks that need to
be created and destroyed, along with one or multiple balls bouncing around, so
I'll just create a bunch of pointers to `GameObject` instances that each have
their own virtual behaviour so each object doesn't explicitly know what they all
do and... wait a minute.

Here inlies the biggest trap I've set for myself so far. The one thing keeping
this game from being done already: passing data between areas of a program is
*hard*. I've been tempted numerous times to make the `Game` class a singleton
and be done with it, but I'm not sure that would win me many favours with those
willing to look at me as a competant coder.

So what else can I do? Mope. That seemed popular with me for a while. Doesn't
exactly get me anywhere, but at least then I don't have to think about the
problem. Or at least, I'd like to say that were true, but I must have been
thinking about it during that time in order to end up with a brilliant idea:

Just copy Unity.

Unity's solution for collision detection is one of callbacks. When a collision
is detected, a message is sent to all the components of an object that says
"Hey! This object just bumped into you". The components of the receiving object
are then free to respond to the collision by accessing it as it's own class. I'm
not really bothering with any sort of entity component system here, so I just
decided to elevate it to the class level.

My `Game` class has a whole bunch of objects listed in a `std::vector`, and SFML
itself has it's own collision code built in, so I set to work. I haven't decided
whether having a sprite is essential to each `GameObject`, so I just gave them
each a bounding box as a member variable and let them set that equal to the
global bounds of the sprite every update.

Then I can just iterate over the vector twice and check if their bounding boxes
intersect and call a function that will eventually pass a bunch of useful data
over. What could possibly go wrong?

![Paddle Collision ad infinitum.]({{ "/assets/paddle-collision.png" | absolute_url }})

As it turns out, quite a lot. This is just one of many problems with this
'solution'. It took me a fair while to figure out what was going on here,
but I eventually figured it out.

**The paddle was colliding with itself.**

Let me break this down: in one update, the game is told to look for the first
pointer in the list and store it as `first`, then it was told to make another,
nested `for` loop that did the same thing and store it as `second`. In one fell
swoop, `first` and `second` become pointers to the same address in memory and
the whole thing falls to bits as the event is called forever.

And this isn't just for the paddle, either. Each and every object will eventually
be counted this way, and even if I managed to communicate to the compiler "If
the first and second game objects are identical, don't bother" that's still a
whole iteration I've wasted.

And this is before we get into combinatorial explosion and all of the pointer
chasing the CPU will have to do once objects get marked for death. It's at this
point I realised that my so-called "brilliant" idea wasn't so great after all.

If anyone's reading this, do keep an eye on your system architecture, won't you?
It'll come back to haunt you if you don't. Otherwise, this project might have to
go on hold for a while whilst I figure out how I can properly send collision
messages.