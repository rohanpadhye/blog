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

One of the **most important** things to keep in mind when submitting an artifact is the AEC's time. Reviewing an artifact takes considerably longer than reviewing a paper, and it is very hard work. Please, please, PLEASE respect the AEC's time and do everything in your power to prevent the reviewers from having to stare at your artifact and wonder what's going on.

A simple way to address this issue is to clarify the amount of *human-time* and *compute-time* required for *every, single, step* in your README. In fact, I recommend providing an outline of each step with an estimate of human / compute time before going into the details. Here is an example:

```
Artifact FooBar
# Overview
* Getting Started (10 human-minutes + 5 compute-minutes)
* Build Stuff (2 human-minutes + 1 compute-hour)
* Run Experiments (5 human-minutes + 24 compute-hours) 
* Validate Results (30 human-minutes + 5 compute-minutes)

# Getting Started
Run `./install.sh` and go grab a coffee (5 minutes to install). 
You will see no output while the script runs. 
The script accesses the internet to download external dependencies such as Qux and Baz.
Once complete, it will have created a directory called `stuff`.
If this command fails, you can delete the `stuff` directory and try again.
...

# Build Stuff 
Run `./build.sh stuff` and get some lunch -- this should take approx. one hour to complete. 
You should see a progress bar while the command runs.
Once complete, it will have created a `BUILD` directory. If this directory already exists, it will be overwritten.
...

```

The above example also showed some elements from the next tip...

## Tip D: Explain side-effects before they occur

Whenever you ask the reader to perform an action, such as executing a command or script, please clarify what side-effects that command will have. For example, does it create or modify any files? Does it create any new directories? Are the filenames dependent on the command-line arguments that you provide? Will the command try to access the internet? Will running the command lead to large amounts of additional disk space being used? What output should you expect if everything goes fine?

## Tip C: Support (almost) instant gratification

Many papers report results of experiments that can take inordinately long to compute. For example, one of the artifacts that I submitted myself required 2 CPU-years to run the whole experiment. Naturally, you cannot and *should not* expect the AEC reviewers to spend so many compute resources.

A good way around this is to provide *alternative* experiment configurations, which can run with much fewer resources, even if they provide only approximate results. For example, you could provide the following in your artifact README:

```
# Experiments (3 compute-hours)
Run `./exp.sh 6 30m 1` to run our tool on only *6 benchmarks* for *30 minutes each* with only *1 repetition*. 
This command takes only **3 hours** to run in total, and produces results that approximate the results shown in the paper.

Run `./exp.sh 20 24h 10` to replicate the full experiments in the paper, which take 200 days to run. 
Feel free to tweak the args to produce results with intermediate quality, depending on the time that you have.
```

When providing means to produce approximate results, some AEC reviewers may not be satisfied that they can validate all the claims in your paper --- because it necessarily requires weeks or months to reproduce the tables or plots that you have in the paper. In such cases, you could provide the results of the long-running experiments in your aritfact package as a sort of *pre-baked* data-set. Make sure that the the output of *fresh-baked* approximate experiments (as shown above) is in exactly the same format as your *pre-baked* data set. That way, you could increase the reviewer's confidence that *had they performed the full set of experiments, they would have seen results similar to that shown in the paper*.


## Tip G: Support hotfixing and failure recovery

No matter how hard you try or how many of your friends and collegues you get to try out your artifact, you can expect that something goes wrong for at least one of the reviewers. Usually, the reviewer is missing some dependency, has limited resources (e.g. memory), or is running an OS or other platform that requires a slightly different configuration which you did not anticipate.

Fret not, as most AE processes provide a mechanism for reviewers to communicate issues with basic functionality of the artifact, and receive tech support from the authors. In most cases, authors can identify the issue and quickly fix the artifact on their local machine.

Now, how do you send this fix over to the AEC? One way would be to repackage the entire artifact again and ask the AEC to re-download this giant 10GB file and load up another VM, etc. A much better solution would be to support *hotfixing*, i.e., patching the artifact while it is live and running on the reviewer's machine.

There are many ways to do this and I don't want to go into detail here. For Docker, you could post your container images to [Docker Hub](https://hub.docker.com/) and have the reviewers simply `docker pull`. In other cases, you could embed a Git repository of your scripts/tools in the package that you send and simply have the reviewers perform a `git pull`. Either way, the important thing is to support the hotfixing mechanism *before you submit the first version*. This is often a step that authors forget to do in their initial submission.

That said, hotfixing is not useful if your artifact cannot deal with failure recovery. A good artifact is one which can recover from crashes in any step of the README. For example, let's say the command `./exp.sh results` runs your experiments and produces results in the `results` directory, and this command crashes due to a bug in your aritfact. You've now identified a fix, pushed changes, and asked the reviewers to pull the latest version of the artifact, bug-free. A simple strategy would simply be to ask the reviewers to delete the `results` directory and run the command again. In short, try to avoid any steps in your artifact causing global, irreversible changes, which cannot be hotfixed.

## Tip E: Cross-reference claims from the paper (and explain what's missing)

One of the main purposes of artifact evaluation is to enable the AEC to independently validate claims made in the paper. For this purpose, please do not just ask the AEC to run a bunch of scripts and say "QED". It is important to list down items from the paper and cross-reference them with data from the artifact. For example, your README could say:

```
# Validate Results (30 human-minutes + 5 compute-minutes)
Run `./scripts/analyze_results.sh` for

The output of the experiments will validate the following claims:
- Table 1: `results/tab.csv` replicates Table 1 on Page 5.
- Figure 2: `results/plot.pdf` replicates the plot in Figure 2 on Page 8.
- Page 7, para 3: "We outperform the baseline by 2x". See `results/comparison.csv`, where the second column (our performance) should have a value that is twice as much as the third column (baseline).

Our artifact does not validate the following claims:
- On Page 8, we say X, but this cannot be validated without access to specialized hardware/people, so we leave it out of scope of artifact evaluation.

...
```

## Tip F: Produce results in a standard human-readable format

If you have plots in the paper, then it is generally a good idea to auto-generate plots from the results of experiments, instead of asking the AEC to stare at log files and compare numbers with figures in the paper. If you do the latter, then please adjust the estimation for "human-minutes" accordingly, as it takes longer for humans to read text than to read figures. Conversely, when reproducing tables from the paper, it is easier for a reviewer to read CSVs or nice ASCII-formatted tables rather than to read LaTeX-formatted tables. Please avoid auto-generating LaTeX, even if you did this for your paper. As a general rule of thumb, prefer standard human-readable formats (e.g. CSV or MarkDown) rather than custom human-readable formats (e.g. arbitrary log files) or formats intended for machines (e.g. JSON or XML).

## Tip F: Use consistent terminology

It is not uncommon for authors to rename tools, techniques, theorems, and other named entities just a day or two before paper submission. This often leads to aritfacts and papers disagreeing on standard terminology. When submitting artifacts for AE, remember to refactor the artifact to use the same terminology used in the paper. Sometimes, this is not possible -- for example, if your artifact contains pre-baked results of experiments that were run before you decided on a terminology; in such cases, include some explanation of old/new terminology in your artifact README before you discuss how to run scripts or read provided files.

# Tips for Artifact Evaluation Reviewers 

# Suggestions for Artifact Evaluation Chairs
