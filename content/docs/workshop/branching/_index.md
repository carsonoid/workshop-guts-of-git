---
weight: 200
title: Git Branching
bookCollapseSection: true
---

# Git Branching

This section builds on the previous [Git Fundamentals]({{< ref "../fundamentals" >}}) section.

If you have not done that section you should still be able to do all the excercies here since
each one starts with a script to get you to a known starting state.

{{<                                                               page-break >}}
## Tip: Preventing Data Loss

A vast majority of git commands are non-destructive. Most things you do can be undone. But
when dealing with complex branch operations or other unfamiliar commands you may want to
take extra precautions to prevent data loss

With that in mind, below you will find my #1 tip for protecting yourself when you
are doing things that are scary in git:

{{< tabs "setup-repo" >}}
{{< tab "bash" >}}
```bash
cp -rp ./my-repo ./my-repo.bak
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
copy my-repo my-repo.bak
```
{{< /tab >}}
{{< /tabs >}}

Ok, so it's just a copy. Not very fancy or effecient but it works. As you become more familiar with git you might not do this as often. But when in doubt, take a few seconds nad make a backup. Then no matter what you do you can recover.

{{<                                                               page-break >}}
## Tip: Kinds of Git Diagrams

So far we have seen and used 2 kinds of git diagrams. But the exercises in this
section are going to use a 3rd.

### Traditional Diagrams

The fundamentals section ended with a repo that looks something like this:

{{< mermaid >}}
%%{init: { 'theme': 'neutral' } }%%

gitGraph
    commit id: "Initial add" tag: "v0.0.1"
    commit id: "feat: add api"
    branch topic
    commit id: "feat: add users"
    checkout main
    commit id: "feat: add docs"
{{< /mermaid >}}


These diagrams are great! And they are really the only accurate way to represent
the data as it exists in git.

In fact, you will find a lot of these in the git book. Here is another example from the git book:

{{< figure src="basic-merging-2.png" attr="https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging" >}}

We could make this same graph using our mermaid gitGraph

{{< mermaid >}}
%%{init: { 'theme': 'neutral', 'gitGraph': {'showBranches': true, 'showCommitLabel':true,'mainBranchName': 'master'} } }%%

gitGraph
    commit id: "C0"
    commit id: "C1"
    commit id: "C2"
    branch iss53
    commit id: "C3"
    checkout master
    commit id: "C4"
    checkout iss53
    commit id: "C5"
    checkout master
    merge iss53 id: "C6"
{{< /mermaid >}}

But I think we can do a bit better when it comes to visualizing the state you need to understand when dealing with some git branching workflows. So we are going to introduce...

{{<                                                               page-break >}}
### Stack Diagrams

As we work through the branching section of the workshop we are going to
visualize our operations using a more simplified version of these diagrams.

Essentially git branches will simply be represented as stacks of commits. You can see that this is just a slightly different way to describe the same data as before:

{{< columns >}}

{{< figure src="stack-diagrams/s1.png" >}}

<--->

{{< figure src="basic-merging-2.png" attrlink="https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging" attr="<sub><sub>Source: Pro Git</sub></sub>" >}}

{{< /columns >}}

{{< hint danger >}}
**Stack Diagrams Cannot Always Accurate Express Git State**

We will be using them here because
 * They make visualizing certain processes easier
 * They more closely match what you see in `git log` or during an interactive rebase

**BUT** data is lost in some cases. If you look at the comparison above, you will see that
they you lose some data on merge. Stack diagrams, like `git log` have to decide how to sort commits across trees and there is no exact answer. But they are a useful tool for understanding
some of the concepts we will cover here.
{{< /hint >}}

## Wrapping Up

This section covered just a few of the basic concepts like

* git remotes
* multiple branches
* stack diagrams

Next we will build on this to help you understand how to keep branches
up to date and get branch changes back to the `main` branch

{{< page-break last="true" nextRef="./update-with-merge" title="Start Exercise" >}}
