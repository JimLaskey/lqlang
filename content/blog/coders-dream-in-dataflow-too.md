+++
date = '2026-03-10T06:52:25-03:00'
draft = false
title = 'Coders Dream in Dataflow Too'
+++

It happened in the shower, or on a walk, or staring at a whiteboard with a dried-out marker in your hand. The solution arrived not as words, not as syntax, but as a *shape* — data flowing from here to there, branches splitting, paths converging, the whole thing hanging in your mind like a mobile. You can see it. You can trace any path through it.

Then you open your editor and start dismantling it.

Line by line, you flatten the shape into text. You invent variable names for connections that didn't need names. You impose an order on things that had no order. You take something you *saw* and turn it into something you *read*. By the time you're done, the shape is gone. What's left is a set of instructions that some other poor soul will have to mentally reconstruct back into the shape you started with.

Every programmer does this. **Every single time.**

## It's Not Just You

Turns out, cognitive science has receipts on this one.

Researchers studying how programmers comprehend code found something telling: when they compared people working in visual dataflow environments versus traditional text languages, the dataflow group immediately formed data-flow-based mental models of programs. The text group? They had to grind through control flow first — the sequencing, the branching, the nesting — and only *then* could they reconstruct the dataflow underneath. Both groups ended up at the same place. The text programmers just took the scenic route.

A 2023 study out of ICSE asked the obvious question: if developers already think about code spatially, what happens when you let them arrange it spatially? Lay code out on a canvas instead of stacking it in a scrolling file. Translate logical relationships into physical proximity. Developers *want* that. Their mental model already works that way. The IDE just never caught up.

And here's where it connects to [Robots Dream in Dataflow](/blog/robots-dream-in-dataflow): a NeurIPS 2024 paper showed that when large language models are prompted to *visualize* their reasoning — to generate spatial representations instead of just churning through text — their performance jumps dramatically. The AI, like the human, thinks better when it can think in space.

Both sides of the screen are dreaming in dataflow. Neither side wanted the text.

## The Whiteboard Test

Watch any team of developers plan a system. They don't open an editor. They grab a whiteboard.

They draw boxes and arrows. Data flowing from one service to another, fanning out, converging, transforming. They argue about the *shape* of the architecture, not the syntax. Nobody stands at a whiteboard and writes `async function processRequest()` — they draw what it *does*.

Then they sit down and throw the picture away.

The whiteboard was the design. The code is the translation. And like all translations, it's lossy. Parallelism that was obvious in the drawing disappears in the text. Relationships that were spatial become semantic — buried in import statements and naming conventions that only make sense if you already understand the architecture.

We have an entire discipline — software architecture — whose primary tool is the diagram. UML. ER models. Flowcharts. State machines. Sequence diagrams. We've invented a dozen visual languages for *thinking* about programs. Then we throw them all away and write text.

## The Double Translation

In [Robots Dream in Dataflow](/blog/robots-dream-in-dataflow), I argued that AI builds a graph of relationships internally, then flattens it into sequential text because that's the only format we've given it.

Now look at the mirror image. The programmer builds a graph in their head, then flattens it into text because that's the only format the machine accepts.

Graph → text → graph → text, around and around. The programmer compresses their mental model into code. The compiler parses it back into an AST — a graph. The processor runs it in parallel across cores and pipelines. The beginning was a graph. The end was a graph. Everything in between was a detour through text that nobody asked for.

The AI and the human are sitting across from each other, both thinking in dataflow, communicating through a format that neither of them wanted. Like a translator at a summit where both parties already speak the same language.

Maybe it's time to cut out the middleman.
