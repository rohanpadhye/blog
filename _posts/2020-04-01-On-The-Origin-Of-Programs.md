---
layout: post
draft: true
title: "On the Origin of Programs by Means of Natural Reflection, or The Preservation of Data Races in the Struggle for CPU Time"
excerpt_separator: <!--more-->
---

Where do programs come from? This question has eluded experts since the birth of computer science as an independent discipline. This article provides a brief survey of the literature on what is often called **The Origin Problem**.

<!--more-->

## The Origin Problem

Let us first define the problem with an example. You are probably reading this article on a computer. In fact, you are probably using some program to read this article. There are likely many other programs currently running on your computer. Depending on your operating system, you can find the list of such programs by launching the task manager, the activity monitor, or by screaming "Hey Google, what's currently running?". If you are like me, you've probably wondered: where do these programs come from? Were they always running, or was there ever a *beginning*? This is the *Origin Problem*. It is formally stated as follows: "Given an arbitrary running program and its current state, has it always been running?"

Now, stay with me here. I know that physics dictates that there *must* have been a beginning because somebody had to physically build the computer on which the program is running and of course because the universe only came into existence on January 1, 1970 at 00:00:00 UTC. However, the computer scientists among you will appreciate that since [physics](https://en.wikipedia.org/wiki/Ultimate_fate_of_the_universe) is never used to resolve the [Halting Problem](https://en.wikipedia.org/wiki/Halting_problem), it is also inappropriate to resolve the Origin Problem. Can we reason about this problem using pure computer science?

Indeed, it turns out that the Origin Problem is general **undecidable**. A proof is included in the [appendix](#appendix), where the Halting Problem is reduced to the Origin Problem. However, for practical applications, we do not necessarily need an oracle to decide if any *arbitrary* program had a beginning. We are often concerned with determining where the programs currently running on one's own computer came from.


## The Intelligent Programmer Proposition

The classical dogma that proliferates software engineering textbooks is that programs are intentionally designed. Proponents of this school of thought argue that (a) programs are beautiful and elegant, and (b) programs have a purpose; therefore, programs must have been designed by an *intelligent programmer*. Not only is the argument flawed---indeed, this author has witnessed programs that are neither elegant nor have a purpose---but it also raises a host of other questions such as: *where does the programmer come from?* 

## Natural Reflection

The theory of natural reflection was originally developed to find an approximate solution to the *Halting Problem*, a dynamic variant of which can be stated as follows: given a running program and its input, *will it eventually halt*? Although the problem is well known to be undecidable in general---and therefore an exact solution does not exist---it is in some cases useful to [solve the probem partially](https://www.burn.im/pubs/BurnimJalbertStergiouSen-ASE09.pdf) or to simply [make an educated guess](https://support.mozilla.org/en-US/kb/warning-unresponsive-script), which this theory helps in doing.

The reasoning goes as follows. Programs can inspect and modify themselves at run-time, via a mechanism known as *reflection*. Running programs can create copies of themselves via operations such as [fork](http://man7.org/linux/man-pages/man2/fork.2.html). Programs that halt are eventually culled from the resource pool. Over time, the resource pool fills up with non-terminating programs. The approximate solution to the Halting Problem is therefore to wait and then say **no**.

However, natural reflection can also be used to get some insights into the Origin Problem when combined with a brilliant recent development from the field of loop theory.

## The Primordial Loop Hypothesis

Loops have been a favorite point of debate for proponents of intelligent programming. Loops, they argue, are so complex in structure that it is very unlikely for natural reflection of straight-line programs (such as the empty program, which can arise via *asilicogenesis*) to directly result in nontermination. An intermediate step of terminating programs with loops would be required, but there is no currently evidence for this to have ever occurred. They call this the "missing iteration" in the [FOSS record](https://en.wikipedia.org/wiki/Free_and_open-source_software).

<img src="https://blog.padhye.org/images/loop-white.png" height="100" style="float: right" />

However, loop theorists have recently made breakthrough advances that debunk this argument. Published in the latest edition of the field's leading journal *While*, the new hypothesis states that non-terminating programs could have arisen completely naturally and on their own in an instruction-rich environment such as x86. The *primordial loop* is a naturally occurring infinite loop with just a single x86 instruction: `jmp -2`, encoded as the two-byte sequence `0xEBFE`. The probability of any arbitrary naturally occuring program resulting in a perfect infinite loop is thus 1 in 65,536---not an exceedingly unlikely event in the grand scheme of things. From here, natural reflection takes over, giving us the wide variety of non-terminating programs that we see today. Some loop theorists have gone as far as to suggest that primordial loops could be the ancestors of *all* running programs that we find today.



## Flying Spaghetti-Code Monster

<img alt="Flying Spaghetti-Code Monster" src="https://blog.padhye.org/images/fscm.png" height="100" />

A fringe movement has recently created a stir about an alternative explanation for the missing iteration. Believers claim that a *flying spaghetti-code monster* (pictured above) randomly injects `goto` statements in straight-line code until programs start looping forever. The monster itself was accidentally created in a cold-war-era military facility that tried to weaponize the `goto` statement after someone misinterpreted [Edward Dijkstra's seminal 1968 paper](https://homepages.cwi.nl/~storm/teaching/reader/Dijkstra68.pdf). This theory is entirely plausible but is currently awaiting peer review.




## Appendix



