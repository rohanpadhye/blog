---
layout: post
title: Artifact Evaluation: Tips for Authors
excerpt_separator: <!--more-->
---

A number of software research conferences such as [ICSE](https://2019.icse-conferences.org/track/icse-2019-Artifact-Evaluation), [ISSTA](https://conf.researchr.org/track/issta-2019/issta-2019-Artifact-Evaluation-#About), [PLDI](https://pldi19.sigplan.org/track/pldi-2019-PLDI-Research-Artifacts), [POPL](https://popl19.sigplan.org/track/POPL-2019-Artifact-Evaluation), [OOPSLA](https://2019.splashcon.org/track/splash-2019-Artifacts#Call-for-Artifacts), [SOSP](https://sysartifacts.github.io/), and [USENIX Security](https://www.usenix.org/conference/usenixsecurity20/artifact-evaluation-information) incorporate an *artifact evaluation* (AE) process: authors of (conditionally) accepted papers can optionally submit their tools, code, data, and scripts for independent validation of the claims in their paper by an artifact evaluation committee (AEC). Papers with accepted artifacts get stamped with [one or more badges](https://www.acm.org/publications/policies/artifact-review-badging). Personally, I'm a big fan of the AE process, as it promotes reproducible and reusable research.

<!--On the surface, the AE process mirrors a paper submission process. For example, there is usually a Call for Artifacts (CFA), which provides some basic instructions, then the artifacts are submitted through a portal such as [HotCRP](http://hotcrp.com), and finally you get back reviews, possibly after one or more opportunities for rebuttal. However, the actual work that goes into producing and/or reviewing an artifact is vastly different from doing so for papers.  Just like a CFP doesn't necessarily tell you what makes a good paper, the CFA does not say much about what an ideal artifact looks like.--> 

But what makes a good artifact? Although there exist [many](https://www.cs.cmu.edu/~mleone/how-to.html)  [great](https://www.cs.cmu.edu/~jrs/sins.html) [resources](https://www.microsoft.com/en-us/research/academic-program/write-great-research-paper/) for [writing good papers](https://homes.cs.washington.edu/~mernst/advice/write-technical-paper.html), as well as for [writing good rebuttals](https://www.st.cs.uni-saarland.de/zeller/onresearch/rebuttal-patterns.php3), I haven't found anything similar for producing good artifacts for AE. This is my attempt at filling the gap.

<!--more-->

In this post, I would like to share some insights that I gained while participating in two artifact evaluation committees ([PLDI 2018](https://pldi18.sigplan.org/committee/pldi-2018-artifact-evaluation-committee) and [PLDI 2019](https://pldi19.sigplan.org/committee/pldi-2019-pldi-research-artifacts-artifact-evaluation-committee)) as well as while submitting two artifacts* of my own ([ISSTA 2019](https://conf.researchr.org/details/issta-2019/issta-2019-Technical-Papers/14/Semantic-Fuzzing-with-Zest) and [OOPSLA 2019](https://2019.splashcon.org/details/splash-2019-oopsla/57/FuzzFactory-Domain-Specific-Fuzzing-with-Waypoints)). I am by no means an expert on this topic. However, I've identified some common pain points that make the AE process annoying for both authors and reviewers. Some of the insights in this post stem from my own past mistakes. I hope that this post helps future authors (and perhaps even reviewers and chairs) in ensuring that AE goes smoothly.

This post is heavily biased towards PL/SE/Systems-ish artifacts that involve tools, scripts, benchmarks, and experiments, since I've had most experience with such type of artifacts.

\* One of these won a [Distinguished Artifact Award](https://twitter.com/moarbugs/status/1151701669781966848)!

* TOC
{:toc}

## Tip 1: Submit a friendly package

The first decision you would need to make is how to package your artifact. Should you send a large ZIP file with everything crammed in? Should you send a single Docker container or VM image? What should you put in these packages? There are two sub-parts to this tip:

### Require the fewest dependencies

This one is obvious. Don't expect the AEC to install dozens of different dependencies in order to run your artifact. Do not force the reviewers to use a particular OS either. It is usually okay to have your package depend on technologies that are widely available for multiple platforms, such as Git, Java, Python, Docker, and/or VirtualBox---though you should try to require at most one or two of these. A good practice is to package your entire artifact inside a VM or Docker container, so that you can manage fine-grained dependencies yourself. 

### Do not expect the AEC to have lots of physical resources

This one is less obvious. It is probably not the best idea to submit humongous artifacts (e.g. over 100GB in size) or that require vast amounts of compute resources (e.g. 32 cores with over 256GB RAM). The exact threshold for okay versus not okay probably depends on the type of conference and the tech that is common at the time of submission. If you think you are on the borderline, it is a good idea to ask the chairs what is considered acceptable. Most artifacts that I've worked with can be packaged in under 10GB, require less than 16GB of RAM, and can be run on a single core machine (even if the authors used a different setup themselves).

If your artifact requires a bunch of data that is already publicly available online---for example, a benchmark suite consisting of open-source software---then you could avoid packaging such data in the artifact and instead provide scripts that will download the benchmarks at run-time. This doesn't reduce the overall storage requirement for the AEC, but reduces the bloat in the initial submission. It allows the AEC to get your artifact up and running much quicker in order to report issues with basic functionality. Not packaging external resources into your artifact also lets you avoid getting into trouble with conflicting licensing requirements, should you want to make your artifacts publicly available eventually.

If the nature of your artifact *requires* you to run on special hardware---for example your paper is about massive parallelism or uses GPUs or needs SGX---then it is sometimes okay to provide your own hardware to the AEC. One strategy that I've seen work in the past is to provide remote SSH access to a server that you host, where the home directory has all the scripts and data necessary to reproduce experiments listed in a paper. As authors, it is your job to ensure that the server(s) meet all the resource requirements. Ask your chairs if this is allowed; you may have to provide a crypographic hash of the entire home directory at submission time to prove that you haven't modified its contents after the deadline. **Gotcha**: If you provide access to a *single* server, make sure that multiple AEC reviewers can interact with your artifact concurrently. A good solution for this is to provide scripts which when run generate files only in a specific output directory and nowhere else; that way, each reviewer can choose a unique directory name. Another important consideration here is that the AEC identities must usually remain anonymous to support blind reviewing; you should take steps to prove to the AE chairs that you are not tracking the AEC's logins in any way.


## Tip 2: Estimate human + compute time and declare it upfront

One of the **most important** things to keep in mind when submitting an artifact is the AEC's time. Reviewing an artifact takes considerably longer than reviewing a paper, and it is very hard work. Respect the AEC's time and do everything in your power to prevent the reviewers from having to stare at your artifact for hours wondering if they are doing the right thing.

A simple way to address this issue is to clarify the amount of *human-time* and *compute-time* required for *every, single, step* in your artifact's README. In fact, I recommend providing an outline of each step with an estimate of human / compute time before going into the details. Here is an example artifact README:

~~~ markdown
Artifact FooBar
# Overview
* Getting Started (10 human-minutes + 5 compute-minutes)
* Build Stuff (2 human-minutes + 1 compute-hour)
* Run Experiments (5 human-minutes + 3 compute-hours) 
* Validate Results (30 human-minutes + 5 compute-minutes)
* How to reuse beyond paper (20 human-minutes)

# Getting Started (10 human-minutes + 5 compute-minutes)
* Follow the instructions at XYZ to run our VM/container (10 minutes read).
* Run `./install.sh` and go grab a coffee (5 minutes to install). 
  - You will see no output while the script runs. 
  - The script accesses the internet to download external dependencies such as Qux and Baz.
  - Once complete, it will have created a directory called `stuff`.
  - If this command fails, you can delete the `stuff` directory and try again.
...

# Build Stuff (2 human-minutes + 1 compute-hour)
* Run `./build.sh stuff` and get some lunch -- this should take ~1 hour to complete. 
  - You should see a progress bar while the command runs.
  - Once complete, it will have created a `BUILD` directory. 
  - If the `BUILD` directory already exists, it will be overwritten.
...

~~~

The example above also shows some elements from our next tip...

## Tip 3: Explain side-effects before they occur

In the artifact's README, whenever you ask the reviewer to perform an action, such as executing a command or script, clarify what side-effects that command will have. For example, does it create or modify any files? Does it create any new directories? Are the filenames dependent on the command-line arguments that you provide? Will the command try to access the internet? Will running the command lead to large amounts of additional disk space being used? What output should you expect if everything goes fine?

Such information helps AE reviewers sanity check. It also prevents errors in one step cascading to subsequent steps because the reviewer did not realize that some step failed. Cascading errors makes for very hard debugging, especially when the AE process allows only a fixed number of rebuttal opportunities. Try to help reviewers identify failing steps quickly. 

Also, do not penalize reviewers for running the same command multiple times. Ideally, every step in the README should be [idempotent](https://en.wikipedia.org/wiki/Idempotence).

## Tip 4: Enable quick turnaround (via approximation if needed)

Many papers report results of experiments that can take very long to compute. For example, one of the artifacts that I submitted myself required 2 CPU-years to run the whole suite of experiments. Naturally, you cannot and *should not* expect the AEC reviewers to spend so many compute resources. 

A good way around this is to provide *alternative* experiment configurations, which can run with much fewer resources, even if they provide only approximate results. I usually aim for less than 24 hours of compute on a single-core CPU. For example, you could provide the following in your artifact README:

~~~ markdown
# Run Experiments (~3 compute-hours)
* Run `./exp.sh 6 30m 1` to run our tool on only *6 benchmarks* for *30 minutes each* with only *1 repetition*. 
  - This command takes only **3 hours** to run in total, and produces results that approximate the results shown in the paper.
  - Since there is only 1 repetition, there will be no error bars in the final plots.
  - Results will be saved in a directory called `results`.

* Run `./exp.sh 20 24h 10` to replicate the full experiments in the paper
  - This command takes **200 days** to run 10 reps of all 20 benchmarks for 24 hours each. 
  - Feel free to tweak the args to produce results with intermediate quality, depending on the time that you have.
  - Results will be saved in a directory called `results`.
~~~

When providing means to produce approximate results, some AEC reviewers may not be satisfied that they can validate all the claims in your paper, because it necessarily requires weeks or months to reproduce the tables or plots that you have in the paper. In such cases, you could provide the results of the long-running experiments in your aritfact package as a sort of *pre-baked* data-set. Make sure that the the output of *fresh-baked* approximate experiments (as shown above) is in exactly the same format as your *pre-baked* data set. That way, you could increase the reviewer's confidence that *had they performed the full set of experiments, they would have seen results similar to that shown in the paper*.

## Tip 5: Support hotfixing and failure recovery

No matter how hard you try or how many of your collegues you recruit to test your artifact, you can expect something to go wrong for at least one of the reviewers. Usually, the reviewer is missing some dependency, has limited resources (e.g. memory), or is running an OS or other platform that requires a slightly different configuration which you did not anticipate.

Fret not, as most AE processes provide a mechanism for reviewers to communicate issues relating to basic functionality of the artifact with authors, and receive remote tech support. In most cases, authors can identify the issue and quickly patch the artifact on their local machine.

Now, how do you send this fix over to the AEC? One way would be to repackage the entire artifact again and ask the AEC to re-download this giant 10GB file and load up another VM, etc. A much better solution would be to support *hotfixing*, i.e., patching the artifact while it is live and running on the reviewer's machine.

There are many ways to do this and I don't want to go into details for each method here. For Docker, you could post your container images to [Docker Hub](https://hub.docker.com/) and have the reviewers simply `docker pull`. Or you could embed a Git repository of your scripts/tools in the package that you send and simply have the reviewers perform a `git pull`. Either way, the important thing is to support the hotfixing mechanism *before you submit the first version*. This is often a step that authors forget to do in their initial submission.

That said, hotfixing is not useful if your artifact cannot deal with failure recovery. A good artifact is one which can recover from crashes in any step of the README. For example, let's say the command `./install.sh` downloads items into the `stuff` directory, and this command crashes due to a bug in your aritfact. You've now identified a fix, pushed changes, and asked the reviewers to pull the latest version of the artifact, bug-free. A simple strategy would simply be to ask the reviewers to delete the `stuff` directory and run the command again. In short, try to avoid any steps in your artifact causing global, irreversible changes, which cannot be hotfixed. 

## Tip 6: Cross-reference claims from the paper (and explain what's missing)

One of the main purposes of artifact evaluation is to enable the AEC to independently validate claims made in the paper. For this purpose, do not just ask the AEC to run a bunch of scripts and say "QED". It is important to list down items from the paper and cross-reference them with data from the artifact. For example, your README could say:

~~~ markdown
# Validate Results (30 human-minutes + 5 compute-minutes)

The output of the experiments will validate the following claims:
- Table 1: `results/tab1.csv` reproduces Table 1 on Page 5.
- Figure 2: `results/plot2.pdf` reproduces the plot in Figure 2 on Page 8.
- Page 7, para 3: "We outperform the baseline by 2x". See `results/comparison.csv`, where the second column (our performance) should have a value that is twice as much as the third column (baseline).

Our artifact does not validate the following claims:
- On Page 8, we say X, but this cannot be validated without access to specialized hardware/people, so we leave it out of scope of artifact evaluation.

...
~~~

## Tip 7: Produce results in a standard human-readable format

If you have plots in the paper, then it is generally a good idea to auto-generate plots from the results of experiments, instead of asking the AEC to stare at log files and compare numbers from these logs with figures in the paper. If you do the latter, then adjust the estimation for "human-minutes" accordingly, as it takes longer for humans to read text than to read figures. Conversely, when reproducing tables from the paper, it is easier for a reviewer to read CSVs or ASCII-formatted tables rather than to read LaTeX-formatted tables in source form. Avoid auto-generating LaTeX, even if you did this for your paper. As a general rule of thumb, prefer standard human-readable formats (e.g. CSV or MarkDown) rather than custom human-readable formats (e.g. arbitrary log files) or formats intended for machines (e.g. JSON or XML).

## Tip 8: Use consistent terminology

It is not uncommon for authors to rename tools, techniques, theorems, and other named or acronymized entities just a day or two before paper submission. This often leads to aritfacts and papers disagreeing on standard terminology, since the core of the artifacts are often developed before the paper is finalized and submitted. When submitting artifacts for AE, remember to refactor the artifact to use the same terminology used in the paper. Sometimes, this is not possible -- for example, if your artifact contains pre-baked results of experiments that were run before you decided on a terminology. In such cases, include some explanation of old vs new terminology in your artifact README before you discuss how to run scripts or read provided files.

## Tip 9: Make your artifact reusable

The point of AE is not just to get a badge on your paper. Well, it sort of is, but there should also be a larger goal: to make your research reusable for others*. This means that the research artifacts you produce should be easily reusable on datasets/inputs/workloads that are NOT part of the evaluation presented in your paper. To that end, I recommend including a section on "how to reuse beyond the paper" in your artifact README. This section need only describe the steps to run a single instance of your research tool or system for a small use case. Including such a section also forces you to ensure that your artifact has an understandable user interface, be it something as simple as sensible command-line arguments and readable success/error messages. [Some](https://conf.researchr.org/track/issta-2019/issta-2019-Artifact-Evaluation-#Call-for-Submissions) [conferences](https://popl20.sigplan.org/track/POPL-2020-Artifact-Evaluation) provide explicit badges for reusability. Even if not, completing this section will give the AEC more confidence that your artifact is not something that has been overfitted for the evaluation in the submitted paper.

\* This may not be applicable to some types of artifacts such as mechanized proofs or results of empirical studies.

## Tip 10: Join an Artifact Evaluation Committee

This tip is more strategic than tactical: it may not help you if your AE deadline is coming up soon. However, if you are a graduate student or early-stage researcher, try to get on the AEC of a conference in your area*. It's a lot of work, but a great way to get exposure to artifacts produced by other researchers. Not only will it help you in submitting your own artifacts for evaluation in the future, but it could also inspire you to refine the way you do research.  

\* Many AECs are formed via open invitation for applications -- typically you submit a short bio about yourself via a Google Form or similar [[Exhibit A](https://twitter.com/poplconf/status/1135895797520900097), [Exhibit B](https://twitter.com/vj_chidambaram/status/1151977134593908736)]. If you don't find one of these, look for conferences scheduled for about 3-6 months in the future that do not have an AEC finalized and email the AE chairs to find out how they will form the committee (chairs: apologies for the spam that I'm going to send your way). My guess is that they will be happy to talk to motivated grad students / researchers who are interested in such roles, especially if you have prior publications or AE experience.

---

If you have experience with artifact evaluation yourself and have more tips to add, some insights regarding other types of artifacts (e.g. survey data, user studies, or mechanized proofs), or if you vehemently disagree with anything in this post, please let me know.
