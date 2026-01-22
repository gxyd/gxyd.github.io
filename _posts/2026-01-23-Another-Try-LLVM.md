---

layout: post
title: "LLVM: Another Try"
author: "Gaurav Dhingra"
excerpt: "Contributing to LLVM"
keywords: llvm, compilers, 2026

---

Over the last few years I've often tried to make a contribution to [LLVM-project](https://github.com/llvm/llvm-project) but failed to land any contribution in, mostly because I engaged myself in some contract work last year, and because of which I couldn't devote myself to focusing on it.

I feel I've come full-circle back LLVM contribution. I think I should be paving the way forward to keep myself away from all the distractions (AI taking over software development, whether what I'm taking up will lead to paid work or not etc.) and try as much as I can on two simple goals (atleast for the next 2 months or so):

- learn C++
- Contribution to LLVM project


> ## Learn C++ 

Overall the last 1 year, I think I've degraded as a software engineer, and something I'm sure I work on it. I intend to focus on learning a of C++ concepts from [C++ reference](https://en.cppreference.com/) and [learn cpp](https://learncpp.com/), there are many concepts which I have never been able to think clearly about, the idea is to majorly use the above C++ reference to learn C++, and taking help from an AI-agent as less as possible. I'm one of those software engineer who thinks that using AI substantially isn't good to move forward as a software engineer, and that's been my stance from the last 4 years (I'm not sure if it's just pure denial of it's capabilities though).

> ## Contribution to LLVM project

I think one of the biggest hurdle one faces when starting to work on LLVM project is figuring out how to build LLVM, and since my laptop isn't very powerful (Macbook Air M4 - 256 GB), I need to be careful with using it's resources, I've been maintaining this [LLVM debug build](https://gist.github.com/gxyd/ea46bf72bdee94d8f0b253b2e1b6be68) listing the way I built LLVM for difference issues, and it help's me make sure I document my way of building LLVM (with various configuration options), so I can re-use specific feature if needed.

Currently, I'm looking at an issue https://github.com/llvm/llvm-project/issues/121952 from the LLVM project, which I'm able to reproduce locally and I'm trying to follow the comment left by one of the maintainer of the project. I've already understood many things that I wasn't aware of before approaching this issue (e.g. Fuzzer), and many C++ features that I haven't used in a while and re-visiting those helps.

I'll try my best to keep this blog of mine continuously updated, and which will probably help me in writing better blog posts as well. See ya mate.
