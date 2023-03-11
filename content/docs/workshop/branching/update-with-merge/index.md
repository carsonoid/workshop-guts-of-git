---
weight: 200
title: Update With Merge
bookCollapseSection: false
---

# Update With Merge

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

The rest of this pages walks through pulling all the latest code into your branch with `git merge`

{{<                                                               page-break >}}
## Setup

Let's start by setting up our test repo to a known state.

{{< details "Show Script" >}}

You can simply click in the block below to copy the snippet and paste it into
a terminal.

> WARNING: This will DELETE everything from `~/gitrepo` if either exists
> Make a backup if you want to keep work from that repo.

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
git add .
echo "## Has Docs" >> README.md
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
EOF

cd ~/gitrepo
```
{{< /details >}}

{{<                                                               page-break >}}
### Check the two logs

The log for `topic`

```bash
git log topic --pretty=oneline
```

```txt {hl_lines=["1-2"]}
e4a3fc6118be3193724b4d44dd1c3e2361ceefb1 (HEAD -> topic) feat: add acls
e79af8b7d0f95d7ec80637051734c8bae5b22c45 feat: add docs
221f2c37b9de909e5e25b53031f752f8f1f0ebb4 (tag: v0.0.1) feat: add api
f74cd0034f3cba2fd6b26864e1e153ebae340b41 Initial Add
```

THe log for `main`

```bash
git log main --pretty=oneline
```

```txt {hl_lines=["1-2"]}
61545bbb1473e52bdab38ff78c1af57d47a5f9de (main) refactor: api
7ccefa1ff40dc32a49d1a9372b3265dd2c9a4ca1 feat: add users
221f2c37b9de909e5e25b53031f752f8f1f0ebb4 (tag: v0.0.1) feat: add api
f74cd0034f3cba2fd6b26864e1e153ebae340b41 Initial Add
```

{{<                                                               page-break >}}
## The state of our branches

Our local branches now look like this:

{{< figure src="stack-diagrams/s6.png" >}}

The bottom two commits are greyed out because there is no contention between these two repos
once they hit the `feat: add api` commit.

> *We won't bother showing the tracking branch in this exercise, but it is still there

{{<                                                               page-break >}}
## What is going to happen

The entire idea of `git merge` is that you are trying to pull in the latest
code from your merge head to your base branch.

It does this by essentially building a single new commit that contains
the the entire latest state of the target.

Before we run the command, let's understand what it's going to do by
breaking down exactly what is going to happen

{{<                                                               page-break >}}
### Upstream content

We can expand the commits in `main` to see what files they
have updated or added.

{{< figure src="stack-diagrams/s7.png" >}}


{{<                                                               page-break >}}
### Visualizing the merge commit

While not 100% accurate, a good way to think about `git merge` is to imagine
git building a single new commit that contains the sum of all the changes
you are going to pull in.

{{< figure src="stack-diagrams/s8.png" >}}

But it really only makes one commit with the final state. So it is better imagined like this:

{{< figure src="stack-diagrams/s9.png" >}}

{{<                                                               page-break >}}
#### Applying the merge commit

Now that Git has made this commit. It can take the state of the repo as described
in that commit and **merge** it with the state of the repo as described in
the latest commit in `topic`

{{< figure src="stack-diagrams/s10.png" >}}

{{< hint warning >}}
Two major implications come with what will happen

1. All of our original commits will remain untouched.
2. Any merge conflicts will only need to be resolved once!
{{< /hint >}}

{{<                                                               page-break >}}
### Do the merge

```bash
git merge main
```

```txt
Auto-merging api
CONFLICT (content): Merge conflict in api
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

😱 Conflicts!

No one likes seeing a conflict on merge but sometimes they happen.

In our case, the fixes are simple. We can edit the `README.md` and the `api` files
to manually resolve the conflicts. Then once we are done we can mark them
as solved and commit them.

```bash
git add .
git commit
```

```txt
[topic 22abe1a] Merge branch 'main' into topic
```

{{<                                                               page-break >}}
#### Post-merge log

If we check out git log, we can see that it contains all the commits
from both branches. And it also contains our new `merge` commit.

```bash
git log --pretty=oneline
```

```txt
22abe1a5104e747d5361646e59c1d4b65896b449 (HEAD -> topic) Merge branch 'main' into topic
f5e5392a7004366c9a1f48799497bdbf7e883ff9 feat: add acls
42c8711920bf0017762446c33242953d968e4c8f (main) refactor: api
baa9e2f0cc6eb1e93edf1d3c5fba36b6ad5236a6 feat: add docs
0064f0d618f1f6e4d286fb27e081aa7d88246e27 feat: add users
98c289adc2a366186c3d3db1e8f987ef592243b7 (tag: v0.0.1) feat: add api
ad28d84b252411157d857c41931fbf9e8add1851 Initial Add
```

{{< figure src="stack-diagrams/s10.png" >}}


{{<                                                               page-break >}}
##### git log ordering

You may have noticed that our stack diagram and the `git log` have different orders.
That is not an accident.

Our stacks look like this:

{{< figure src="stack-diagrams/s10.png" >}}

In reality, we have to use a more complete git diagram to view the state of the repo
at this time

{{< mermaid >}}
%%{init: { 'theme': 'neutral' } }%%

gitGraph
    commit id: "Initial add" tag: "v0.0.1"
    commit id: "feat: add api"
    branch topic
    commit id: "feat: add docs"
    commit id: "feat: add acls"
    checkout main
    commit id: "feat: add users"
    commit id: "refactor: api"
    checkout topic
    merge main id: "Merge branch 'main' into topic"
{{< /mermaid >}}

The same diagram could be shown like this:

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
    merge main id: "Merge branch 'main' into topic"
{{< /mermaid >}}

This is again a case where `git` log can be confusing. Any tool that wants to take
the tree that is your git history and make into a serialized format
will need to decide how to order commits made on parallel tracks.
 By default, `git log` sorts commits by timestamp.


If you want, you can also visualize more complex branch histories using git itself:

```bash
git log --pretty=oneline --graph
```

```txt
*   22abe1a5104e747d5361646e59c1d4b65896b449 (HEAD -> topic) Merge branch 'main' into topic
|\
| * 42c8711920bf0017762446c33242953d968e4c8f (main) refactor: api
| * 0064f0d618f1f6e4d286fb27e081aa7d88246e27 feat: add users
* | f5e5392a7004366c9a1f48799497bdbf7e883ff9 feat: add acls
* | baa9e2f0cc6eb1e93edf1d3c5fba36b6ad5236a6 feat: add docs
|/
* 98c289adc2a366186c3d3db1e8f987ef592243b7 (tag: v0.0.1) feat: add api
* ad28d84b252411157d857c41931fbf9e8add1851 Initial Add
```

{{<                                                               page-break >}}
#### Pulling the changes from `topic` to `main`

So now that we have the latest code in our branch, what about using `merge`
to get our changes pulled back into `main`?

We can do that by simply checkout out `main` and merging `topic`

```bash
git checkout main
git merge topic
```

```txt
Updating 42c8711..22abe1a
Fast-forward
 README.md | 5 +++++
 api       | 4 ++++
 docs      | 0
 3 files changed, 9 insertions(+)
 create mode 100644 docs
```

Then check the log:

```txt
*   22abe1a5104e747d5361646e59c1d4b65896b449 (HEAD -> main, topic) Merge branch 'main' into topic
|\
| * 42c8711920bf0017762446c33242953d968e4c8f (main) refactor: api
| * 0064f0d618f1f6e4d286fb27e081aa7d88246e27 feat: add users
* | f5e5392a7004366c9a1f48799497bdbf7e883ff9 feat: add acls
* | baa9e2f0cc6eb1e93edf1d3c5fba36b6ad5236a6 feat: add docs
|/
* 98c289adc2a366186c3d3db1e8f987ef592243b7 (tag: v0.0.1) feat: add api
* ad28d84b252411157d857c41931fbf9e8add1851 Initial Add
```

In this case you can see one feature of `merge` it did *not* make another merge
commit to bring our changes from topic back into main. This is because they both have the
exact same content already. So `merge` just updated the pointer to `main` to point
to the same commit as `topic` does.

If you *want* to have a merge commit anytime you can use the `--no-ff` flag to force git
to always make a merge commit.

What we end up now is a git history as shown below.

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
    merge main id: "Merge branch 'main' into topic"
    checkout main
    merge topic
{{< /mermaid >}}

Notice there is no extra commit, we are just effectively saying that `main`
starts at the same commit as `topic` does.

---

Even if you add more commits to `topic`
it will have no effect, main will always start at the commit as it was when
the merge happened

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
    merge main id: "Merge branch 'main' into topic"
    checkout main
    merge topic
    checkout topic
    commit
{{< /mermaid >}}

Even if you add more commits to `topic`, which would be unusual but not impossible,
it will have no effect, main will always start at the commit as it was when
the merge happened

---

And if a new branch is made of `main` it will start at the commit
`main` points to:

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
    merge main id: "Merge branch 'main' into topic"
    checkout main
    merge topic
    checkout topic
    commit
    checkout main
    branch topic2
    commit
{{< /mermaid >}}


{{<                                                               page-break >}}
#### Summary

So now that we have done a merge we can talk about what is good and bad about
these merges.

{{<                                                               page-break >}}
##### Pro: All of the original commits are untouched

We have tracked every single commited state of our repo
which means we could easily inspect the repo before and after our
merge commit.

{{<                                                               page-break >}}
##### Con: All of the original commits are untouched

Every commit is there, all the time. Not only do we have all these extra
`Merge branch 'x' into y` commits, but any bad commits we created will stick around forever.

So a git diagram for merge-heavy branches can often look like this:

{{< mermaid >}}
%%{init: { 'theme': 'neutral' } }%%

gitGraph
    commit id: "Initial add" tag: "v0.0.1"
    commit id: "feat: add api"
    branch feat1
    checkout feat1
    commit id: "feat: add users"
    checkout main
    commit id: "feat: add docs"
    checkout feat1
    merge main id: "Merge branch 'main' into feat1"
    commit id: "fix typo"
    commit id: "please work"
    checkout main
    commit id: "feat: add ACLs"
    checkout feat1
    merge main id: "Merge branch 'main' into  feat1"
    checkout main
    merge feat1 id: "Merge branch 'feat1' into main"
{{< /mermaid >}}

Thanks to all of our `git merge` commands we get to enshrine every bad
commit and merge of `main` -> `feat1` to get the latest code into our git
history forever!

{{<                                                               page-break >}}
##### Pro: Conflicts only have to be solved once

This is a big one: No matter how you do it, when you `git merge`
you only ever have to solve merge conflicts once. That is
because git only ever tries to merge two states together.

This means that even though we have to solve conflicst, we only ever
do it once per execution of the command. The same is not true of other methods
like `rebase`

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

There is one other option for solving this issue. Using `git rebase` which is covered
in the next section

{{< page-break last="true" nextRef="../update-with-rebase" title="Next Section" >}}
