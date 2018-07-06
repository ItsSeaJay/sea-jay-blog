---
layout: post
title: "Announcing Project Lily Tin"
date: 2018-07-06 15:45:27 +0000
categories: gamedev
tags: lily-tin c++ glfw sdl2 cmake
---

Over the past couple of months, I've become increasingly aware of how little
I've done with the C++ programming language outside of my university. I've made
a simple tool here or there, but never anything impressive or super
relevant to my career.

## The Pitch
To fix this, I've decided to make a small game to better demonstrate my
knowledge. It's going to be in 2D, because I've never written a full game in
this language before, and I feel that I work best when I don't have to worry
about the z axis.

I'm also making this open source too, because these days, I don't see any
reasons to keep code to myself.

If you want an idea of how it might play, think of breakout mixed with boxing.
I've been playing quite a lot of WarioWare, Inc.: Mega Microgames recently and
I've been inspired by the simplicity of the score-based minigames you get for
completing stages, and how each manage to do a lot with limited controls.

![WarioWare Screenshot]({{ "/assets/warioware-inc-mega-microgames-jump-forever.png" | absolute_url }})

*The 'Jump Forever' minigame only has one button. Well, to begin with anyway.*
*Apologies for the blurry screenshot.*

I'm imagining that instead of a paddle you move back and forth, you instead have
a pair of boxing gloves that need to be charged first. The ball is subject to
simple gravity, so you have to get into a rhythm of anticipating and hitting
the ball at the right time. Why you can't just punch the bricks yourself is
something I'm still thinking about.

I'm giving it the code name of 'Lily Tin' for now, but I'm going to try my
hardest to find a better title for it before release.

## Technology
In order to make a game, you need a couple of things. You need a way of letting
the player tell the game what to do, you need a way of providing feedback to the
player once they've told it what they'd like it to do, and you as the programmer
need a way to tell the game what feedback to give that player to let them know
what their input did. In other words, a feedback loop.

Most people use a game engine like Unity or Unreal to take care of these things
for them, but for this project, I've decided to go for broke and write my own
engine from scratch. Whilst most people I've talked to have claimed that this is
a terrible idea, given the explosive complexity of modern, general purpose
solutions, I felt that the opportunity to learn something outweighed the sheer 
amount of time needed to create the thing in the first place.

Right now, I'm not worrying to much about what other games I can make with it.
It's going to be bespoke. It only needs to run this one game on one platform and
not explode in the process. That's it.

--------------------------------------------------------------------------------

OK, so I've decided that I'm going it alone. Great! But that doesn't solve
all of the problems I mentioned before. Using C++ for its vast list of features
and exceptional performance is a good start, but that doesn't make my feedback
problem go away. Unlike more modern languages like Java and C#, C++ doesn't have
much - if anything - in the way of displaying graphics or pumping audio into your
ears. In fact, it almost predates the need for those things entirely.
So what options do I have?

### 1. Forget graphics; do it in text
While C++ does have a wide range of tools for console output, and it would
make things much easier, I can't name a single game I've played offline
from this decade that actually did it this way. Plus, the game I have in mind
really needs something more elaborate than that.

### 2. Actually doing it from scratch
So when I said that I wanted to do this from scratch, that wasn't entirely
accurate. While it is possible for me to display graphics and audio entirely on
my own, doing so brings its own set of problems. The main one of these is that
*no-one can agree on how to put stuff on the screen*.

This is to be expected from personal computers, of course. After all, each of
the three big operating systems I hope to support (Windows, Mac, and Linux) were
all made by different people at different times. Besides; I want to make a game,
not another abstraction layer. Which brings me to my next best choice:

### 3. Just use a library
This might seem obvious. Why didn't I mention it before? If throwing stuff at
the screen is such a common problem, shouldn't someone else have found a solution
already? Well of course they have. It'd be pretty weird for me to talk about
games like this if they didn't.

Trouble is, there are a lot of libraries out there; each of them with their own
strengths and weaknesses:

#### GLFW
It's almost impossible to talk about this one without first mentioning
[GLUT, or the OpenGL Utility Toolkit](https://www.opengl.org/resources/libraries/glut/).
Back in the ninties and noughties, if you needed to learn graphics,
specifically using OpenGL, GLUT and its derivatives were your go-to solution.

GLUT provided a lever into the OpenGL API using familiar C functions, as well
as windows and basic input. Many of it's basic functions are still in use
today as they bleed into the specification itself.

It was easy to use, if a bit unweildy, and I've heard legends of people
making games in it before, but overall it was unsuited to any 'serious'
development. It's no surprise then that it was cancelled in 2005 and hasn't been
touched since.

Enter [GLFW, or the OpenGL](http://www.glfw.org/)...
actually, I have no idea what it stands for. I can't find it anywhere!
I'm going to say 'framework'.
GLFW feels like a replacement for GLUT made by people who know what they're
doing. Whilst it's certainly interesting and provides a great deal of control,
you still have to do almost everything not related to the operating system
yourself. I'd only really use this if I needed to do something specific where
the size of the library was more important than it is now.

#### SDL2
[![SDL2 Logo](https://www.libsdl.org/media/SDL_logo.png)](https://www.libsdl.org/)

This is the one that you'll probably seen the most of, thanks to its use in
Valve's Source engine. It occupies a similar space to GLFW, but is much larger
and has support for the DirectX API for better performance on Windows.

This would be a perfectly fine choice if it wasn't for one fatal flaw: it's
written in C. Now while C is a great language in it's own right, it's missing
two things critical to how C++ operates: classes and namespaces. These two
things fundamentally change how you interact with the program, and tend to clash
when put together. It's not impossible to make them work, but doing so requires
a level of architecture that I feel is unnecessary for this project. Plus,
I'd have to write my own audio code, which would be a massive hassle for
something this small.

#### SFML
[![SFML Logo](https://www.sfml-dev.org/download/goodies/sfml-logo-small.png)](https://www.sfml-dev.org/)

This is the one that I ultimately went with, and the main reasons why is that
it's based on C++, not C. All of it's functionality is properly namespaced, and
as such is much easier to integrate it in my project. Plus, it has it's own
module for audio, so I don't have to bother with the nitty gritty of beeps and
boops.

Fantastic! It seems like I'm on to a winner. A comprehensive library written
in the same language that's easy to implement and has a focus on 2D rendering.
All's well that ends well, right? Not exactly.

Whilst the library is relatively easy to install, it has a large amount of
dependancies and a wide girth in general. In this case, I feel like that's a
worthwhile payoff so long as I make good use of all of its features, in each of
the modules but it is worth considering for future projects.

--------------------------------------------------------------------------------

So I've decided on a language, I can give feedback to the player and I can
process input. What else is there? Well, once I've written our code, I need
some way of compiling it into an executable, as well as some way of linking the
libraries to that executable.

I'm a Windows developer mainly, so my first instinct is to boot up Visual Studio
and muddle my way through the menus. This is a working solution, but it's not
the best way of doing things, or even the most efficient.

The other day, I was trying to compile a game for an aquaintance of mine. As
above, they made it in Visual Studio and distributed it that way. I tried
debugging it and it wouldn't start. Why?

Honestly, I'm not entirely sure, but I'm pretty sure it was to do with the
version. Visual Studio abstracts a lot of things, which is good because it
makes it easier to use, but also bad because of how delicate things can get.
Especially when it comes to updates. If one thing goes out of place, your
game will just refuse to start unless you spend hours toiling in the Properties
menu.

Keep in mind that my game's open source. We're in the big wide world here with
living, breathing people that have opinions and preferences. There's a big
chance that whoever works on my game next won't use Visual Studio, or even have
a Windows computer at all. This is where meta-build systems like CMake come in.

## CMake
C++ is a compiled language. This means in order for a computer to meaningfully
use your code, you have to convert it once for each machine you want to target,
on real (or sometimes virtual) computers.

It's not like Python where your code is interpreted each time it's run. It's
tied to your hardware. Even worse is when you write C++ on one computer using
that computer's tools and hand it off to someone else. You have no idea if it'll
work or not, and most of the time it doesn't.

CMake fixes this by having you create a single, interpreted script that tells
your computer how your project should be built regardless of what kind of
computer you're using, called `CMakeLists.txt`. You write your code and headers
as normal, but instead of linking them to a Visual Studio solution, for example,
you tell CMake to run the script and do it for you.

Using this method, you can create a stupid amount of different build chains for
a ludicrous number of compilers and platforms, but the ones I'm most interested
in are Makefiles and Visual Studio solutions (`.sln` files).

The downside of this of course is that you've got to learn an entirely new
programming language, though I've spent enough time with CMake at this point
to have a reasonable idea of how it works. As for configuring the latest version
of SFML with it, that was a tricky process due to the lack of documentation
available for 2.5.0 at time of writing.

I have managed to do it, but I think I want it to have a post all of it's own.
Stay tuned for that.