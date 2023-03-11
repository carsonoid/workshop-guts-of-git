---
weight: 100
title: Understanding Remotes
bookCollapseSection: false
---

## Understanding `remotes`

So, before we go about fixing our out of date branch, let's talk a little about
another fundamental git concept: `remotes`

A `remote` in git is sort of like a pointer, but rather than point to a specific
file or commit. It points git itself to a place where a git repo is stored.

This is typically a remote server. Something serving git over `https` or `ssh`
is fairly common.

For example, since this workshop comes from a repo hosted on GitHub it has two remotes
that are available:

```
https://github.com/carsonoid/workshop-guts-of-git.git
```

```
git@github.com:carsonoid/workshop-guts-of-git.git
```

Both of these are valid `remotes` for git.

{{<                                                               page-break >}}
### A note about local `remotes`

This may seem like an oxymoron, but yes, you can use a local git repository as a remote!

This is important to know because we will be utilizing this feature heavily when working through various git branching exercises.

{{<                                                               page-break >}}
### Creating the baseline repo

While you can use the repo from [Fundamentals](../fundamentals), here is a script to help you fully recreate the `~/gitrepo` our commands expect.

By using the baseline you will also have a local repo that exactly matches the diagrams in
the rest of this section.

> WARNING: This will DELETE everything from `~/gitrepo` and `~myclone` if either exists
> Make a backup if you want to keep work from that repo.

{{< details "Show Script" >}}

You can simply click in the block below to copy the snippet and paste it into
a terminal.

```bash
cd ~

bash <<EOF
set -e

rm -rf ~/gitrepo ~/myclone
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

echo
echo "======= Setup success! ======="
echo
echo "------- git log -------"

git log
EOF
```
{{< /details >}}


{{<                                                               page-break >}}
### Cloning from a local `remote`

To use a local repo as a remote, you can simply `git clone` it.

```bash
git clone ~/gitrepo ~/myclone
cd ~/myclone
```

With that done, we can now examine and operate in `~/myclone` and do all the
same things that we would do if we used a full git server.

{{<                                                               page-break >}}
### What `~/myclone` contains

From here on out you we will be visualizing the state of our git repo using stacks.
Each stack represents a `head` of a git repo and it's starting commit (at the top) and
all the commits that came before. It really isn't much more than a `git log` made using boxes.

{{< figure src="stack-diagrams/s2.png" >}}

{{<                                                               page-break >}}
#### Explained

{{< figure src="stack-diagrams/s2.png" >}}

You can see that we are concerned with 3 heads right now:

###### Our local `main` head.

The repo starting from the commit pointed to by the contents of `refs/heads/main`

###### The head at `origin/main`

The repo starting from the commit pointed to by the contents of `refs/remotes/origin/main`

{{< details "NOTE: Remote Refs and .git" >}}
**You will likely not actually have a file at `.git/remotes/origin/main`**

This is because git stores most remote heads in a compacted file.

Try using `ls-remote` instead

```bash
git ls-remote -q --heads
```

```txt
63241bfa5a195149e8b3dd3ec934ef4f97f2fa52	refs/heads/main
abd14ba38fd78839c2a6f24b50ee99f2f3934e7a	refs/heads/topic
```
{{< /details >}}

###### The head at stored in our remote

The repo starting from the commit pointed to by the contents of `refs/heads/main`
as it currently exists in the remote.

{{<                                                               page-break >}}
### Update the remote

Let's simulate somone updating `main` on the remote.

```bash
cd ~/gitrepo
git checkout main
touch "users"
git add users
git commit -m "feat: add users"

cd ~/myclone
```

The snippet above emulates another user pushing new code to `main`. It's the
local repo equivilent of someone else pushing new code to a remote server like GitHub.


{{<                                                               page-break >}}
#### Meanwhile in `~/myclone`

You will notice in our diagram that while we just added a new commit to the remote
absolutely nothing has changed in our local repo. Both `main` and `origin/main` are unaware of the new commit.

{{< figure src="stack-diagrams/s3.png" >}}

{{<                                                               page-break >}}
#### Fetch latest

But this is easy to fix! We can use `git fetch` to update our local repo based on the latest data
in the remote

```bash
git fetch
```

Now you can see that our `origin/main` head now points to the the new commit.

{{< figure src="stack-diagrams/s4.png" >}}

{{<                                                               page-break >}}
#### Merge latest

So, how do we ensure that our `main` ref starts at the same place as `origin/main`?

Easy! We use the same command we always use to combine to refs into one: `git merge`

```bash
git merge origin/main
```

```txt
Updating c5b4843..24f3e65
Fast-forward
 users | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 users
```


Now you can see that our `origin/main` head now points to the the new commit.

{{< figure src="stack-diagrams/s5.png" >}}


{{<                                                               page-break >}}
#### What about `git pull`?

It may seem sort of odd to go to all this trouble. And even more odd to use `git merge`
to update our `main` to match the latest main from our remote repo.

But the truth is that this is **exactly** what `git pull` does.

> You do not have to run the commands below, they will have no effect since our
> `main` is already up to date

```bash
git pull origin/main
```

Or, since our `main` is already set up to track `origin/main` we can skip the second
argument in our command. Git will assume we want to use the tracking head by default.

```bash
git pull
```

{{< hint warning >}}
You could try this for yourself by following the exercise up to the `fetch` step and using
`git pull` instead.

You will end up with the same git log regardless of method
{{< /hint >}}

{{< page-break last="true" nextRef="../update-with-merge" title="Next Section" >}}
