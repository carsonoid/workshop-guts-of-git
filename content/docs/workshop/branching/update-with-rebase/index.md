---
weight: 300
title: Update With Rebase
bookCollapseSection: false
---

# Update With Rebase

This section builds on the previous [Git Fundamentals]({{< ref "../../fundamentals" >}}) section.

If you have not done that section you should still be able to do all the excercies here since
each one starts with a script to get you to a known starting state.

{{<                                                               page-break >}}
## Problem: Updating to the latest Main

This will cover a very common situation and help you understand two critical
features of git and how they help you solve the problem.

> Problem: You are working on a repo in a topic(feature) branch and
> while you are doing so, the `main` branch for the repo gets updated with new
> code. You want to test your new feature alongside the new code.

Now you have two choices to solve this issue and get your branch up to date with
the latest `main`

The rest of this pages walks through pulling all the latest code into your branch with `git rebase`

{{<                                                               page-break >}}
## Setup

Let's start by setting up our test repo to a known state.

{{< details "Show Script" >}}

You can simply click in the block below to copy the snippet and paste it into
a terminal.

> WARNING: This will DELETE everything from `~/gitrepo` if either exists
> Make a backup if you want to keep work from that repo.

{{< tabs "setup-repo1" >}}
{{< tab "bash" >}}
```bash
cd ~

bash <<EOF
set -e

rm -rf ~/gitrepo
mkdir -p ~/gitrepo
cd ~/gitrepo
git init

echo "# My Git Repository" > README.md
git add README.md

mkdir assets
curl -Lo assets/gopher.png \
   https://go.dev/doc/gopher/doc.png
git add assets

git commit -m "Initial Add"

touch api
echo "## Has an API" >> README.md
git add .
git commit -m "feat: add api"

git tag v0.0.1

git checkout -b topic

touch docs
echo "## Has Docs" >> README.md
git add .
git commit -m "feat: add docs"

echo "acls" >> api
echo "## Has ACLs" >> README.md
git add .
git commit -m "feat: add acls"

git checkout main

touch users
echo "## Has Users" >> README.md
git add .
git commit -m "feat: add users"

echo "refactored" >> api
echo "## API Refactored" >> README.md
git add .
git commit -m "refactor: api"

git checkout topic

echo
echo "======= Setup success! ======="
echo
echo "------- topic log -------"

git log topic

echo
echo "------- main log -------"

git log main

EOF

cd ~/gitrepo
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
cd ..

rmdir /S /Q gitrepo
mkdir gitrepo
cd gitrepo
git init

echo "# My Git Repository" > README.md
git add README.md

mkdir assets
echo "Gopher" > assets\gopher.png
git add assets

git commit -m "Initial Add"

type nul > api
echo "## Has an API" >> README.md
git add .
git commit -m "feat: add api"

git tag v0.0.1

git checkout -b topic

type nul > docs
git add .
echo "## Has Docs" >> README.md
git commit -m "feat: add docs"

echo "acls" >> api
echo "## Has ACLs" >> README.md
git add .
git commit -m "feat: add acls"

git checkout main

type nul > users
echo "## Has Users" >> README.md
git add .
git commit -m "feat: add users"

echo "refactored" >> api
echo "## API Refactored" >> README.md
git add .
git commit -m "refactor: api"

git checkout topic

echo "======= Setup success! ======="

```
{{< /tab >}}
{{< /tabs >}}
{{< /details >}}

{{<                                                               page-break >}}
### The state of our branches

Our local branches now look like this:

{{< figure src="stack-diagrams/s6.png" >}}

The bottom two commits are greyed out because there is no contention between these two repos
once they hit the `feat: add api` commit.

> *We won't bother showing the tracking branch in this exercise, but it is still there


{{<                                                               page-break >}}
## What is going to happen

A rebase is a lot like a merge. The real difference is the order in which changes are applied.

Unlike `merge` our `rebase` is not going to just make one big commit with every change from the
target and apply it all at once. Instead we are going to re-apply every commit from our current
branch and put them on top of the target.

{{<                                                               page-break >}}
## Start the rebase

Starting a rebase follows the same pattern as `merge`. Checkout the repo you
want to update and use `git rebase TARGET` to apply the target to your
currently checked out branch:

```bash
git rebase main
```

{{< hint warning >}}
This command will have merge conflicts. We will be addressing the cause
of this and working through them one at a time.

Do not to try work through them or resolve them yet.
{{< /hint >}}

{{<                                                               page-break >}}
### Introducing `LIMBO`

In order to perform a rebase git is essentially going to take the new commits
from our current branch and pull them aside. In the diagram below you can see
them moved to a box called `LIMBO`

> This idea of `LIMBO` is 100% **not** a git thing, git does the actual rebase differently
> but we are going to draw and imagine it differently in this workshop

{{< figure src="stack-diagrams/s11.png" >}}

{{<                                                               page-break >}}
#### `LIMBO` Setup

Git walks back in the history of both branch and finds the first shared ancestor.

We are going to imagine that it then moves all the new commits on **our current branch**
into `LIMBO`

{{< figure src="stack-diagrams/s12.png" >}}

{{<                                                               page-break >}}
### Rebase prep

Now git sets our current branch to match exactly what our target branch looks like.

{{< figure src="stack-diagrams/s13.png" >}}

Notice that the two branches are now identical. We just have our new commits
that still need to be processed

{{<                                                               page-break >}}
### Apply the first commit from `LIMBO`

Git now applies all the changes from the first commit from `LIMBO` into our
new branch state.

{{< figure src="stack-diagrams/s14.png" >}}

Notice that the commit is shown in a different color. This is on purpose.

**Because the parent commit has changed, the git commit will be a new hash**

This effectively means that you are rewriting the git history to act like the old commit
into the repo never happened.

This can either be a good thing or a bad thing depending on what you want from
your git history.


{{<                                                               page-break >}}
#### Fix the first conflict

So, because our first commit to the readme that
conflicts with changes from `main` we will need to tell git what
content we want in these files for this first commit

To resolve:

* Edit the `README.md` file

Now we can add them and continue the rebase

```bash
git add .
git rebase --continue
```


{{< hint warning >}}
Expect **another** conflict! This is normal. Continue on to see why
{{< /hint >}}

{{<                                                               page-break >}}
### Apply the next commit from `LIMBO`

Git then applies the next commit from limbo on top of our current commit.

{{< figure src="stack-diagrams/s15.png" >}}

{{<                                                               page-break >}}
#### Fix the next conflicts

So, because our first commit involves changes to the api and readme that
both conflict with changes from `main` we will need to resolve those

But wait... didn't we just fix conflicts? Yes we did! But we only fixed the
conflicts between the state of the repo at start of merge and the first
commit that was applied.

This conflict is between the current state of the repo and the changes from
the current conflict being applied.

To resolve:

* Edit `api` file
* Edit the `README.md` file

Now we can add them and continue the rebase

```bash
git add .
git rebase --continue
```

{{<                                                               page-break >}}
### Rebase summary

Now our rebase is done and we can see the final version of both branches:

{{< figure src="stack-diagrams/s16.png" >}}

Notice something missing? There is no "merge commit" here! That is because we
rewrote history and effectively recorded our changes as though they had
happened directly on the history of `main`

{{<                                                               page-break >}}
#### Rebase log

```bash
git log --pretty=oneline
```

You can see that our two commits are simply applied on top of the
latest commits from `main`

```txt {hl_lines=["1-2"]}
4e926fa11e5948b26a2d7ed22c0c82d4a0fd2ec8 (HEAD -> topic) feat: add acls
708ce3d84b6b749cc19e27859f92467fb0ff0332 feat: add docs
a116397a8bc43c7f42fb8f3b192add0b5fbcacc9 (main) refactor: api
c7d22f2b3b1fe579d0220f0a0a62b83780037aa1 feat: add users
76a04f2486251799139c0c86b106b825830575f5 (tag: v0.0.1) feat: add api
45ab9223c6b9085a003e604615e1f59913683aef Initial Add
```

{{<                                                               page-break >}}
### Rebase diagrams

We can better understanding by visualizing the repo before and after


#### Before Rebase

You can see where topic and `main` diverge.

{{< mermaid >}}
%%{init: { 'theme': 'neutral' } }%%

gitGraph
    commit id: "Initial add" tag: "v0.0.1"
    commit id: "feat: add api"
    branch topic
    checkout main
    commit id: "feat: add users"
    commit id: "refactor: api"
    checkout topic
    commit id: "feat: add docs"
    commit id: "feat: add acls"
{{< /mermaid >}}

#### After Rebase

The history of `topic` is the same as though we just barely
created topic off the latest `main` and made our commits

{{< mermaid >}}
%%{init: { 'theme': 'neutral' } }%%

gitGraph
    commit id: "Initial add" tag: "v0.0.1"
    commit id: "feat: add api"
    commit id: "feat: add users"
    commit id: "refactor: api"
    branch topic
    commit id: "feat: add docs"
    commit id: "feat: add acls"
{{< /mermaid >}}

{{<                                                               page-break >}}
### Pulling the changes from `topic` to `main`

We will now pull our changes from `topic` back into the main branch. This can also
be done using `rebase` with some interesting impacts

```bash
# rebase topic to main
git checkout main
git rebase topic

# Log with the graph
git log --pretty=oneline --graph
```

```txt
* 4e926fa11e5948b26a2d7ed22c0c82d4a0fd2ec8 (HEAD -> topic, main) feat: add acls
* 708ce3d84b6b749cc19e27859f92467fb0ff0332 feat: add docs
* a116397a8bc43c7f42fb8f3b192add0b5fbcacc9 refactor: api
* c7d22f2b3b1fe579d0220f0a0a62b83780037aa1 feat: add users
* 76a04f2486251799139c0c86b106b825830575f5 (tag: v0.0.1) feat: add api
* 45ab9223c6b9085a003e604615e1f59913683aef Initial Add
```

So what did we get here? In our case we get clean linear commit history. When
you use `rebase` to pull in changes from topic branches you end up with
a cleaner and simpler git log.

{{<                                                               page-break >}}
#### The final graph for main

In our case, the graph for is now very simple. Notice that even though
the last two commits came from `topic` there is no indication of that
in the graph or really git at all!

{{< mermaid >}}
%%{init: { 'theme': 'neutral' } }%%

gitGraph
    commit id: "Initial add" tag: "v0.0.1"
    commit id: "feat: add api"
    commit id: "feat: add users"
    commit id: "refactor: api"
    commit id: "feat: add docs"
    commit id: "feat: add acls"
{{< /mermaid >}}

{{<                                                               page-break >}}
## Interactive `rebase`

So far `rebase` may seem to be more work than it is worth. A linear git
history is nice but may not be that exciting to some.

Next we are going to cover a super power of `rebase`: Interactive rebase

{{<                                                               page-break >}}
### Setup

Let's start by setting up our test repo to a known state.

{{< details "Show Script" >}}

You can simply click in the block below to copy the snippet and paste it into
a terminal.

> WARNING: This will DELETE everything from `~/gitrepo` if either exists
> Make a backup if you want to keep work from that repo.

{{< tabs "setup-repo2" >}}
{{< tab "bash" >}}
```bash
cd ~

bash <<EOF
set -e

rm -rf ~/gitrepo
mkdir -p ~/gitrepo
cd ~/gitrepo
git init

echo "# My Git Repository" > README.md
git add README.md

mkdir assets
curl -Lo assets/gopher.png \
   https://go.dev/doc/gopher/doc.png
git add assets

git commit -m "Initial Add"

touch api
echo "## Has an API" >> README.md
git add .
git commit -m "feat: add api"

git tag v0.0.1

git checkout -b topic

touch docs
echo "## Has Docs" >> README.md
git add .
git commit -m "feat: add docs system"

echo "docuument feature 1" >> docs
git add .
git commit -m "doc: feaature 1"

echo "acls" >> api
git add .
git commit -m "feat: add acls"

echo "document feature 1" >> docs
git add .
git commit -m "fix: doc typo"

echo "fixed" >> api
git add .
git commit -m "fix: acls"

echo "fixed1" >> api
git add .
git commit -m "please work"

echo "fixed2" >> api
git add .
git commit -m "asdf"

echo "fixed3" >> api
git add .
git commit -m "language X is DUMB! :("

echo
echo "======= Setup success! ======="
EOF

cd ~/gitrepo
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
cd ..

rmdir /S /Q gitrepo
mkdir gitrepo
cd gitrepo
git init

echo "# My Git Repository" > README.md
git add README.md

mkdir assets
echo "Gopher" > assets\gopher.png
git add assets

git commit -m "Initial Add"

type nul > api
echo "## Has an API" >> README.md
git add .
git commit -m "feat: add api"

git tag v0.0.1

git checkout -b topic

type nul > docs
echo "## Has Docs" >> README.md
git add .
git commit -m "feat: add docs system"

echo "docuument feature 1" >> docs
git add .
git commit -m "doc: feaature 1"

echo "acls" >> api
git add .
git commit -m "feat: add acls"

echo "document feature 1" >> docs
git add .
git commit -m "fix: doc typo"

echo "fixed" >> api
git add .
git commit -m "fix: acls"

echo "fixed1" >> api
git add .
git commit -m "please work"

echo "fixed2" >> api
git add .
git commit -m "asdf"

echo "fixed3" >> api
git add .
git commit -m "language X is DUMB! :("

echo "======= Setup success! ======="
```
{{< /tab >}}
{{< /tabs >}}
{{< /details >}}

{{<                                                               page-break >}}
#### View branch log

```bash
git log --pretty=oneline
```

```txt
9e01a83484bc7428cfec752597162128c2acb27b (HEAD -> topic) language X is DUMB! :(
d78cb408c690b63f491e757015b33cc98cb1366e asdf
51dd0c3b6aede0df60274e4a25ab44614b9eddb0 please work
c11e49190eaadae8c2359dee2a297f7af1ffed66 fix: acls
936965a84e3312786b19e8bf7e9b656a07654e5e fix: doc typo
fc0ecdbf66460c758904eb9043a5dd905cfbec0f feat: add acls
9135c0f138ca3fe61f92b063c0c34f524f947919 doc: feaature 1
325f6e3202bddfa071ee8ab8963b9132a5b5a183 feat: add docs system
2dfbd06f94cba98f58a3a626efba10c2198ac86d (tag: v0.0.1, main) feat: add api
66fa49f6d4c275b2e9b4d3cd2a43c545035394d7 Initial Add
```

There are some commits here where someone clearly got frustrated! Are we really sure
that we want to preserve every one of these commits in our git history forever?

* Using `merge` will, by design, always result in every one of these commits in the
log forever.
* Using `rebase` without any args will also keep every commit

{{<                                                               page-break >}}
### Cleaning it up

We want to keep things clean! And `rebase -i` can help! We just need to run it
like normal and tell it which branch we want to rebase against.

```bash
git rebase -i main
```

That will result in Git pulling up our default editor with some interesting content:

```txt
pick d77c7d7 feat: add doccs
pick 3a8449c doc: feature 1
pick 94039a9 feat: add acls
pick c5aded7 fix: docs typo
pick e528e93 fix: acls
pick 6f9ea51 please work
pick f7792bd asdf
pick be0ab5c language X is DUMB! :(

# Rebase ae65529..be0ab5c onto ae65529 (8 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
```

{{< hint warning >}}
**The first time you see this output it the order can be confusing**

If you are using to seeing a `git log` you would expect the commits to start
with the most recent and move to the oldest.

This output shows the same commits as `git log` but in **reverse order**, meaning
that the oldest commits are first and the newest are last.

To help keep you from having to switch between the two types we will use the
`--reverse` flag for `git log` commands from here on out. That will keep
both outputs the same.
{{< /hint >}}


{{<                                                               page-break >}}
#### "acl" commit cleanup

We won't cover every option in detail here, but essentially this is
git giving us a chance to tell exactly how to rewrite our history!

To change what is going to happen, we simply have to change the content of the file. So,
for example we can change the 3 acl related commits to start with `f` instead
of `pick`

```txt {hl_lines=["5-8"]}
pick d77c7d7 feat: add doccs
pick 3a8449c doc: feature 1
pick 94039a9 feat: add acls
pick c5aded7 fix: docs typo
pick e528e93 fix: acls
f 6f9ea51 please work
f f7792bd asdf
f be0ab5c language X is DUMB! :(

# Rebase ae65529..be0ab5c onto ae65529 (8 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
```


Now just save and close the file to tell git to continue.

We can then use `git log`  (with `--reverse`) to see what happened:

```bash
git log --pretty=oneline --reverse
```

```txt {hl_lines=[1]}
66fa49f6d4c275b2e9b4d3cd2a43c545035394d7 Initial Add
2dfbd06f94cba98f58a3a626efba10c2198ac86d (tag: v0.0.1, main) feat: add api
325f6e3202bddfa071ee8ab8963b9132a5b5a183 feat: add docs system
9135c0f138ca3fe61f92b063c0c34f524f947919 doc: feaature 1
fc0ecdbf66460c758904eb9043a5dd905cfbec0f feat: add acls
936965a84e3312786b19e8bf7e9b656a07654e5e fix: doc typo
c92c7938d757625e6463e03a7e932250b9e38752 (HEAD -> topic) fix: acls
```

You will see that we just combined 4 commits into one! And we told git to
"fixup" meaning it only kept the file changes from those 3 ugly commits and
threw away the messages

{{<                                                               page-break >}}
#### More rebase cleanup

But wait! We still aren't clean. There is a documentation fix between
our first acl commit and our initial acl feature add.

Let's use another feature: re-ordering commits

```bash
git rebase -i main
```

Now it should look like this:

```txt
pick d77c7d7 feat: add doccs
pick 3a8449c doc: feature 1
pick 94039a9 feat: add acls
pick c5aded7 fix: doc typo
pick 4f69143 fix: acls

# Rebase ae65529..4f69143 onto ae65529 (5 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
```

Go ahead and *move* the "fix: doc typo" commit up one line.

```txt {hl_lines=[3]}
pick d77c7d7 feat: add doccs
pick 3a8449c doc: feature 1
pick c5aded7 fix: doc typo
pick 94039a9 feat: add acls
pick 4f69143 fix: acls

# Rebase ae65529..4f69143 onto ae65529 (5 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
```

Then you can save and close the file to finalize these changes. Then check
the log again.


```bash
git log --pretty=oneline --reverse
```

```txt
66fa49f6d4c275b2e9b4d3cd2a43c545035394d7 Initial Add
2dfbd06f94cba98f58a3a626efba10c2198ac86d (tag: v0.0.1, main) feat: add api
325f6e3202bddfa071ee8ab8963b9132a5b5a183 feat: add docs system
9135c0f138ca3fe61f92b063c0c34f524f947919 doc: feaature 1
1717a2bc17dce292b18d364f13625cdc2074a3c6 fix: doc typo
b1f4e150edc4cac6604083a2d5d8937720024906 feat: add acls
b9702f63d1a1d7039e2c798c0a94440f19038132 (HEAD -> topic) fix: acls
```

You can see that the two docs related comments are now one after the other!

{{<                                                               page-break >}}
#### Last rebase cleanup

Ok things are looking better in our history. But let's go further! It's clear that
there really are only 3 **unique and important** states we want to represent
with in our branch

1. Adding the docs system
2. Documenting feature 1
3. Adding acls

Our commits should cleanly represent those states. So let's rebase
one more time to get things where we want


```bash
git rebase -i main
```

```txt
pick 747f7b2 feat: add docs system
pick bc94f44 doc: feaature 1
pick 28080d4 fix: docs typo
pick 3c0816a feat: add acls
pick 8f5c34c fix: acls

# Rebase ae65529..8f5c34c onto ae65529 (5 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
```

Now our last update is going to be this:

1. Set the last acl commit to `fixup`
2. Set the `fix: docs type` commit to `fixup`
3. Set the `doc: feaature 1` commit to `reword` so we fix the typo in the message


Example:

```txt
pick 747f7b2 feat: add docs system
r bc94f44 doc: feaature 1
f 28080d4 fix: docs typo
pick 3c0816a feat: add acls
f 8f5c34c fix: acls

# Rebase ae65529..8f5c34c onto ae65529 (5 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
```

Now you can save and close the file. You will get a prompt to
update the commit message for the commit marked as `reword`. Fix the
typo and then save and close that file to complete the rebase

Then we can view our final clean log

```bash
git log --pretty=oneline --reverse
```

```txt
66fa49f6d4c275b2e9b4d3cd2a43c545035394d7 Initial Add
2dfbd06f94cba98f58a3a626efba10c2198ac86d (tag: v0.0.1, main) feat: add api
325f6e3202bddfa071ee8ab8963b9132a5b5a183 feat: add docs system
a752ea0228391672e834cfef1fa0c34c11f5c899 doc: feature 1
e9054cfc916bd62a6974ab65fd2d7ddd3021d6cc (HEAD -> topic) feat: add acls
```

Notice that we now have exactly 3 commits since `main` this is a history
that we can be proud of!

{{<                                                               page-break >}}
#### Exercise: all in one cleanup

We did our cleanup in 3 phases so we could illustrate specific features.

But it would be completely possible, and normal, to just do all of those cleanups
in one step. You may want to consider re-running the setup script and
trying to do all the cleanup in one `git rebase -i` to see for yourself.

{{<                                                               page-break >}}
## TIP: Rebase during a `git pull`

You can use the `--rebase` flag for `git pull` to tell it to fetch the latest
code and rebase your new changes on top. Without this, the default mode for `pull`
is to fetch and merge.

```bash
git pull --rebase
```

<!--

{{<                                                               page-break >}}
## PLACEHOLDER


```bash
```

```txt {hl_lines=[]}
```

-->

{{<                                                               page-break >}}
## Wrapping Up

That's it for rebases and the bulk of the workshop. Why not move onto the git tips
section to learn a few bonus things?

{{< page-break last="true" nextRef="../../../tips" title="See Tips" >}}
