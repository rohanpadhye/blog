---
layout: post
title: Tips for Artifact Evaluation
published: false
---

A number of software research conferences incorporate an *artifact evaluation* (AE) process: authors of accepted papers can optionally submit their tools, code, data, and scripts for independent validation of the claims in their paper by an artifact evaluation committee (AEC). Personally, I'm a big fan of the AE process, as it promotes reproducible and reusable research.

But what makes a good artifact? On the surface, the AE process mirrors a paper submission process; for example, there is usually a Call for Artifacts (CFA), which provides some basic instructions, then the artifacts are submitted through a portal such as [HotCRP](http://hotcrp.com), and finally you get back reviews, possibly after one or more opportunities for rebuttal. However, the actual work required to produce and/or review an artifact is vastly different from the equivalent for papers.  Just like a CFP doesn't necessarily tell you what makes a good paper (or what will get your reviewers mad), the CFA does not say much about what an ideal artifact looks like (or what will make the AEC give up).

In this post, I would like to share some insights that I gained while participating in two artifact committees (PLDI 2018 and PLDI 2019) as well as while submitting two artifacts* of my own as first author. I am by no means an expert on this topic, but I hope that this post might help artifact authors and reviewers in avoiding some common pain points that I've encountered numerous times myself. I'll end this post with some unsolicited suggestions for AE Chairs.

This post is heavily biased towards PL/SE/Systems-ish artifacts that involve tools, scripts, benchmarks, and experiments. That is mostly because it is the type of artifact that I've worked with the most. 

\*One of these won an ACM SIGOSFT Distinguished Artifact Award.

# Tips for Artifact Authors

## Tip A: Send a friendly package

The first decision you would need to make is how to package your artifact. Should you send a large ZIP file with everything crammed in? Should you send a single Docker container or VM image? What should you put in these packages?

There are two sub-parts to this tip:

### Require the fewest dependencies

This one is obvious. Don't expect the AEC members to install 20 twenty different dependencies in order to run your artifact. Do not force the AEC to use a particular OS either. It is usually okay to have your package depend on technologies that are widely available for multiple platforms, such as Git, Java, Python, Docker, and/or VirtualBox -- though it is best that you need only one or two of these. A good practice is to package your entire artifact inside a VM or Docker container, so that you can manage all the dependencies yourself. 

### Do not expect the AEC to have lots of physical resources

This one is less obvious. It is generally not okay to send artifacts that are humongous (e.g. 100+GB in size) or that require inordinate amounts of compute resources (e.g. 16 core machine with 256GB RAM). The exact threshold for okay versus not okay probably depends on the type of conference and the technology is common at the time of submission, so it is a good idea to ask the chairs about this if you are worried. Most artifacts that I've worked with can be packaged in under 10GB and require less than 16GB of RAM on a single core machine.

If your artifact requires a bunch of data that is already publicly available --- for example, a benchmark suite consisting of open-source software --- then you could avoid packaging that in the artifact and instead provide scripts that will download the benchmarks at run-time. This doesn't reduce the overall disk requirement for the AEC, but could allow them to get your artifact up and running much quicker in order to report issues with basic functionality. Not packaging external resources into your artifact also lets you avoid getting into trouble with conflicting licensing requirements, should you want to make your artifacts publicly available.

If the nature of your artifact *requires* you to run on special hardware --- for example your paper is about a parallel algorithm or using a GPU to improve performance --- then it is often okay to provide the hardware to the AEC itself. One strategy that I've seen work in the past is to provide remote SSH access to a server that you host, where the home directory has all the scripts and data necessary to reproduce experiments listed in a paper. As authors, it is your job to ensure that the server(s) meet all the resource requirements. Ask your chairs if this is allowed -- you may have to provide a crypographic hash of the entire home directory at submission time to prove that you haven't modified its contents after the deadline. **Gotcha**: If you provide access to a *single* server, make sure that multiple AEC reviewers can try out your artifact concurrently. A good solution for this is to provide scripts which when run generate files only in a specific output directory and nowhere else --- that way, each reviewer can choose a unique directory name. Another important consideration here is that the AEC identities must usually remain anonymous to support blind reviewing -- you should take steps to prove to the AE chairs that you are not tracking the AEC's logins in any way.


## Tip B: Estimate human and compute time upfront

## Tip C: Support (almost) instant gratification

## Tip D: Explain side-effects before they occur

## Tip G: Support hotfixing and failure recovery

## Tip E: Cross-reference claims from the paper (and explain what's missing)

## Tip F: Use consistent terminology



# Tips for Artifact Evaluation Reviewers 

# Suggestions for Artifact Evaluation Chairs
