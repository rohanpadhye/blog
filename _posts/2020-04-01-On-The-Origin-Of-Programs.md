---
layout: post
title: "On the Origin of Programs by Means of Natural Reflection, or The Preservation of Data Races in the Struggle for CPU Time"
excerpt_separator: <!--more-->
---

Where do programs come from? This question has eluded experts since [the birth of computer science as a rigorous disclipine](https://www.ams.org/journals/bull/1966-72-06/S0002-9904-1966-11654-3/S0002-9904-1966-11654-3.pdf). This article provides a brief survey of the literature on what is often called the **origin problem**.

<!--more-->

## The Origin Problem

You are probably reading this article using a computer program. There are likely many other programs currently running on your computer. Depending on your operating system, you can find the list of such programs by launching the task manager, the activity monitor, or by screaming "*Hey Google, what's currently running?*". If you are like me, you've probably wondered: where do these programs come from? Were they always running, or was there ever a beginning? This is the *origin problem*. 

Now, stay with me here. I know that physics dictates that there *must* have been a beginning because somebody had to physically build the computer on which the program is running and of course because the universe only came into existence on January 1, 1970 at 00:00:00 UTC. However, since [physics](https://en.wikipedia.org/wiki/Ultimate_fate_of_the_universe) is never used to resolve the halting problem, it is also inappropriate to resolve the origin problem. 


## The Intelligent Programmer Proposition

The classical dogma that proliferates software engineering textbooks is that all programs must have a beginning because they are intentionally designed. Proponents of this school of thought argue that (a) programs are beautiful and elegant, and (b) programs have a purpose; therefore, programs must have been designed by an *intelligent programmer*. Not only is the argument flawed---indeed, this author has designed programs that are neither elegant nor have a purpose---but it also raises a host of other questions such as: *where does the programmer come from?* 

## Natural Reflection

The theory of natural reflection was originally developed to find an approximate solution to the *halting problem*, a dynamic variant of which can be stated as follows: given a running program and its input, *will it eventually halt*? Although the problem is well known to be undecidable in general---and therefore an exact solution does not exist---it is in some cases useful to [make an attempt anyway](https://www.burn.im/pubs/BurnimJalbertStergiouSen-ASE09.pdf). This theory provides one such direction.

The reasoning goes as follows. In many languages, programs can inspect and modify themselves at run-time, via a mechanism known as *reflection*. If the program modifies exactly that code which is currently executing, then of course it leads to a *data race*. Most respectable programming languages treat data races as undefined behavior, which means that the implementation is free to do as it pleases in such a situation. In practice, however, implementations are usually lenient and would like to give all interleavings an equal chance; therefore, they simply *fork* the program and continue executing each possible outcome of the race condition. Natural reflection therefore leads to a diverse population of programs in the computing resource pool. Programs that halt are eventually culled from the resource pool. Over time, the resource pool fills up with nonterminating programs. Given any arbitrary running program in the steady state, it is likely a nonterminating one. The approximate solution to the Halting Problem is therefore to [wait and then say **no**](https://support.mozilla.org/en-US/kb/warning-unresponsive-script).

However, natural reflection can also be used to get some insights into the origin problem when combined with a brilliant recent development from the field of loop theory.

## The Primordial Loop Hypothesis

<img src="https://blog.padhye.org/images/loop-white.png" height="100" />

Loops have been a favorite point of debate for proponents of intelligent programming. Loops, they argue, are so complex in structure that it is very unlikely for natural reflection of straight-line programs (such as the empty program, which can arise via *[asilicogenesis](https://en.wikipedia.org/wiki/Abiogenesis)*) to directly result in nontermination. An intermediate step of terminating programs with loops would likely be required, but there is no currently evidence for this to have ever occurred. They call this the "missing iteration" in the FOSS record.

However, loop theorists have recently made breakthrough advances that debunk this argument. Published in the latest edition of the field's leading journal *While*, the new hypothesis states that nonterminating programs could have arisen completely naturally and on their own in an instruction-rich environment such as x86. The *primordial loop* is a naturally occurring infinite loop with just a single x86 instruction: `jmp -2`, encoded as the two-byte sequence `0xEBFE`. The probability of any arbitrary naturally occuring program resulting in a perfect infinite loop is thus 1 in 65,536---not an exceedingly unlikely event in the grand scheme of things. From here, natural reflection takes over, giving us the wide variety of nonterminating programs that we see today. Some loop theorists have gone so far as to suggest that primordial loops could be the ancestors of *all* running programs that we find today.


## Flying Spaghetti-Code Monster

<img alt="Flying Spaghetti-Code Monster" src="https://blog.padhye.org/images/fscm.png" height="100" />

A fringe movement has recently created a stir about an alternative explanation for the missing iteration. Believers claim that a *flying spaghetti-code monster* (pictured above) randomly injects `goto` statements in straight-line code until programs start looping forever. The monster itself was accidentally created in a military facility that tried to weaponize the `goto` statement after someone misinterpreted [Dijkstra's seminal 1968 paper](https://homepages.cwi.nl/~storm/teaching/reader/Dijkstra68.pdf). This theory is entirely plausible but is currently awaiting peer review.

## Undecidability of the Origin Problem

Regardless of the philosophical debate, it turns out that the origin problem, when treated purely mathematically, is **undecidable** in general. A proof follows.

Let's assume that the origin problem is actually decidable. In that case, there exists an algorithm `had_beginning(P, s)`, which takes as input a program `P` and a running state `s`---such as the current program counter, and values of all varables / registers / memory locations---and returns `true` if the program has been executing from its entry point and `false` if the program has been running forever.

Now, given any program `P`, we can construct an augmented program `P'` as follows:
```
Line 1 | program P': 
Line 2 |    run P
Line 3 |    run P   // s' is an intermediate state inside this call
```

That is, the program `P'` simply runs `P` as a subprogram twice in a straight-line sequence.  Let `s'` be a running state inside the subprogram `P` when invoked at **Line 3**.

Now, if `had_beginning(P', s')` returns `true`, then it means that `P'` started execution from Line 1 and reached Line 3. This implies that subprogram `P` *successfully terminated* its execution on Line 2. 

On the other hand, if `had_beginning(P', s')` returns `false`, then it means that `P'` has *always been running*. But of course, a program that has been running forever needs an infinite loop. Since there are no loops surrounding the invocations of subprogram `P`, it must mean that *`P` itself has an infinite loop*. 

In this way, `had_beginning` actually allows us to determine whether any program `P` halts or loops forever. Since the halting problem is known to be undecidable, such a `had_beginning` cannot actually exist; therefore, the **origin problem is undecidable**. QED.
