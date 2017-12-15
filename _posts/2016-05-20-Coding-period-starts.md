---

layout: post
title: "Community Bonding period ends, Coding period starts"
author: "Gaurav Dhingra"
excerpt: "Coding period starts"
keywords: gsoc, sympy, 2016

---


Community Bonding period ends, Coding period starts
The Community bonding period comes to an end now. First of all considering the issues described in the last post:

* Aaron created a new channel for our GSoC project discussion, [sympy/GroupTheory](https://gitter.im/sympy/GroupTheory).
* I have also added rss-feed in my blog. As for time of meeting, me and Kalevi often have discussion on the gitter channel, but since of quite a bit differene in timings between me and Aaron (I tend to sleep early at 11 PM IST). We three haven't been able to together have a meeting. Though Aaron suggested "Kalevi is the primary mentor, so if you have to meet without me that is fine". I also think that's not too big of an issue now, but his opinion has always helped, since he has best knowledge of sympy core.

In the past few weeks I wasn't too much productive, since I had a touch of fever, anyways I am okay now. We have now completed the implementation of the `FreeGroup` class in [PR #10350](https://github.com/sympy/sympy/pull/10350). I started working on the PR back in January but I halted, since of my semester classes. `FreeGroup` is quite similar to the `PolyRing` implemented in `sympy.polys.rings.py`. We first started with the list of tuples as the data structure for the `FreeGroupElm`, where each tuple being `(index, exp)`, but since of the mutable nature of lists, Kalevi suggested to go with tuple of tuples. Also as tuples are probably more efficient as there is no 'housekeeping' overhead. Also changed the element from `(index, exp)` --> `(symbol, exp)`.

Implementing `FreeGroupElm` deals elegantly in such a way that it can't be independently created in a public interface. The reason being: every `FreeGroupElm` is in itself created only by the `dtype` method of `FreeGroup` class. The assignment is as follows:

                        `obj.dtype = type("FreeGroupElm", (FreeGroupElm,), {"group": obj})`

Its sort of an advanced usage of `type` function as a [metaclass](http://www.stackoverflow.com/questions/100003/what-is-a-metaclass-in-python).

Currently the printing code of `latex` and `pprint` for `FreeGroupElm` is a little hacky. I need to work on that as well.

> ## Plan for Next few weeks

Though according to my [proposal timeline](https://github.com/sympy/sympy/wiki/GSoC-2016-Application-Gaurav-Dhingra:-Group-Theory#Proposed_Timeline), we described to go with implementation of other algebraic structures i.e `Magma`, `SemiGroup`, `Monoid`. But we will next move onto **Coset Enumeration**. It is going to be a big task. That is harder and more important than other algebraic structures. Timline states it to be 5 week task, thats almost half the GSoC coding period. Well how do we go about that? I think of studying the mathematics in parallel with the implementation.

We have created a PR for implementation of Finitely Presented Group [#11140](https://github.com/sympy/sympy/pull/11140). Not much code has been added here. Paper on Coset Enumeration using [Implementation and Analysis of Todd Coxeter Algorithm](http://staff.itee.uq.edu.au/havas/1973cdhw.pdf) (by John J. Cannon, George Havas), and other paper being the original paper by Todd and Coxeter, "A practical method for enumerating cosets of a finite abstract group" are the ones I am reading. As for the implementation of Todd Coxeter, we will be following the methods described in the book "Handbook of Computational Group Theory" by Derek F. Holt.

