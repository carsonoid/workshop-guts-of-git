---
weight: -100
bookCollapseSection: false
title: "Git Fundamentals"
---

# Fundamentals

Let's get started already! This page will walk you step by step through some
very basic git commands. While the commands themselves may not be anything new
or special to you, we will be diving into the guts of `git` and what each command
is actually doing inside the repo.

{{< page-break first="true">}}
## Creating a New Repository

It's time to start by making an empty Git repository. We do this by making
a directory, chaning into it, and using `git init`

{{< tabs "setup-repo" >}}
{{< tab "bash" >}}
```bash
mkdir ~/gitrepo
cd ~/gitrepo
git init
```
{{< /tab >}}
{{< tab "windows" >}}
 ```cmd
mkdir gitrepo
cd gitrepo
git init
```
{{< /tab >}}
{{< /tabs >}}

Ok, neat... so what did we actually do by when we ran `git init`? The truth is: not much!

That's right, all that actually happened was that `git` created a bunch of files and directories for us. It didn't need a server, it didn't need credentials, it didn't even need the network at all.

> Basically all that we did was get a `.git` directory created with some basic files in it.

You can see the contents pretty easily using something like the `tree` command

{{< tabs "tree-view1" >}}
{{< tab "bash" >}}
```bash
tree -F .git
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
tree /F .git
```
{{< /tab >}}
{{< /tabs >}}

```txt
.git
├── branches/
├── config
├── description
├── HEAD
├── hooks/
│   ├── applypatch-msg.sample*
│   ├── commit-msg.sample*
│   ├── fsmonitor-watchman.sample*
│   ├── post-update.sample*
│   ├── pre-applypatch.sample*
│   ├── pre-commit.sample*
│   ├── pre-merge-commit.sample*
│   ├── prepare-commit-msg.sample*
│   ├── pre-push.sample*
│   ├── pre-rebase.sample*
│   ├── pre-receive.sample*
│   ├── push-to-checkout.sample*
│   └── update.sample*
├── info/
│   └── exclude
├── objects/
│   ├── info/
│   └── pack/
└── refs/
    ├── heads/
    └── tags/
```

{{<                                                               page-break >}}
## Digging Around in `.git`

Now that we have an empty repo, let's poke around a bit

### The `HEAD` file

{{< tabs "view-head" >}}
{{< tab "bash" >}}
```bash
cat .git/HEAD
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
type .git\HEAD
```
{{< /tab >}}
{{< /tabs >}}

What do you see? For modern versions of `git` you actually will find very little in this file

```txt
ref: refs/heads/main
```

### The `config` file

{{< tabs "config-view" >}}
{{< tab "bash" >}}
```bash
cat .git/config
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
type .git\config
```
{{< /tab >}}
{{< /tabs >}}

Another fairly simple file. Look closely and you will notice it's a fairly basic format. And a new repo typically has
very little in this file:

```txt
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
```

{{<                                                               page-break >}}
### Scaffolded directories

You will notice a few pre-created sub-directories under `objects` and `refs`. We will come back to these.

There is also a `hooks` directory that we will not cover at all here.

```txt
.git
│   # ...
├── objects/
│   ├── info/
│   └── pack/
└── refs/
    ├── heads/
    └── tags/
```

{{<                                                               page-break >}}
## Start the repo

The next thing we are going to do is start the repo:

```bash
echo "# My Git Repository" > README.md
git add README.md
```

{{<                                                               page-break >}}
### A Cursory Check

You might be used to seeing something like this at this point:

```bash
git status
```

```txt
$ git status
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   README.md
```

{{<                                                               page-break >}}
### Check the files

What did that `git add` do? And how does `git status` know that there is a new file?

{{< tabs "tree-view2" >}}
{{< tab "bash" >}}
```bash
tree -F .git
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
tree /F .git
```
{{< /tab >}}
{{< /tabs >}}

```txt
.git
├── index
├── ... # omitted for space
├── objects/
│   ├── 88/
│   │   └── cbae6fb9b1ab364976238c1eb6871093ed3860
│   ├── info
│   └── pack
└── refs
    ├── heads
    └── tags
```

> Assuming you ran the exact files, you will always get this exact file put into `.git`

There are two things to note in this output. The `index` file and the new `object` file.



{{<                                                               page-break >}}
#### The object file

```txt
.git
├── objects/
│   ├── 88/
│   │   └── cbae6fb9b1ab364976238c1eb6871093ed3860
```

We can dig into this file ourselves by using the `cat-file` sub-command.

* The `-p` tells `cat-file` to guess the object type and pretty-print accordingly.

```bash
git cat-file -p 88cbae6fb9b1ab364976238c1eb6871093ed3860
```

In our case there is not much special here. What we see is just the exact content of the file we have staged:

```txt
# My Git Repository
```


{{< hint warning >}}
**This is still a big deal!**

This is one huge design aspect of git that you should internalize:

> Git stores every version of every file in it's object database
{{< /hint >}}

{{<                                                               page-break >}}
#### The index file

```txt
.git
├── index
```

This file is where `git` is now tracking the current state of your repo. This is
how it knows that you stagged a `README.md` to be commited. But just knowing that the repo is staged
isn't quite enough. While this file is really only ever read by `git status` you can also manually query
it for data:

```bash
git ls-files --stage
```

```txt
100644 88cbae6fb9b1ab364976238c1eb6871093ed3860 0	README.md
```

{{<                                                               page-break >}}
### Add More Content

{{< tabs "add-more-content" >}}
{{< tab "bash" >}}
```bash
mkdir assets
curl -Lo assets/gopher.png \
   https://go.dev/doc/gopher/doc.png

# if you don't have curl you can use wget
# wget -O tmp/gopher.png https://go.dev/doc/gopher/doc.png

git add assets
```
{{< /tab >}}
{{< tab "windows" >}}
```bash
mkdir assets
echo "Gopher" > assets\gopher.png
git add assets
```
{{< /tab >}}
{{< /tabs >}}

So this isn't that much different then what we did before. Except that we are
adding a non-text file to the repo. Again, we can check the result:

```bash
git status
```

```txt
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   README.md
	new file:   assets/gopher.png
```

{{<                                                               page-break >}}
#### Dig Deeper

{{< tabs "tree-view3" >}}
{{< tab "bash" >}}
```bash
tree -F .git
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
tree /F .git
```
{{< /tab >}}
{{< /tabs >}}

Now, we can see another new object file alongside our other one. Like the first
file, you should have this exact same object with the same hash in your `.git`.

That is because this file has the same content for everyone and all objects in git
are stored by content hash!

```txt
.git
├── ... # omitted for space
├── objects/
│   ├── 88/
│   │   └── cbae6fb9b1ab364976238c1eb6871093ed3860
│   ├── e1/
│   │   └── 5a3234d5d2c87e4e6afc226d971e8ab0c65d2b
│   ├── info
│   └── pack
└── refs/
    ├── heads/
    └── tags/
```

But what is inside it?

{{< tabs "view-head" >}}
{{< tab "bash" >}}
```bash
git cat-file -p e15a3234d5d2c87e4e6afc226d971e8ab0c65d2b |head -n1
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
git cat-file -p e15a3234d5d2c87e4e6afc226d971e8ab0c65d2b
```
{{< /tab >}}
{{< /tabs >}}

Again, the whole file! This is a binary file so our test command only shows one line
but you can see that the first line shows us that it is a `PNG` image

```txt
�PNG
```

{{<                                                               page-break >}}
## Commit

```bash
git commit -m "Initial Add"
git log
```

```txt
commit c71366be637024591f202876fbe5224b68f03199 (HEAD -> main)
Author: Carson Anderson <user@email>
Date:   Sun Oct 23 14:09:52 2022 -0600

    Initial add
```

You can see that the log now shows us the commit we made, along with relevent
information about the commit.


{{<                                                               page-break >}}
### Dig Deeper

So, what happened to our `.git`?

{{< tabs "tree-view4" >}}
{{< tab "bash" >}}
```bash
tree -F .git
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
tree /F .git
```
{{< /tab >}}
{{< /tabs >}}

```txt {hl_lines=["4-5","8-11"]}
.git
├── ... # omitted for space
├── objects/
│   ├── 10/
│   │   └── 76d1da17d464ae87160a3dd2519cd0f64f3cd5
│   ├── 88/
│   │   └── cbae6fb9b1ab364976238c1eb6871093ed3860
│   ├── a5/
│   │   └── c3a3648323f431095a32f4f5ea86885820b80a
│   ├── c7/
│   │   └── 1366be637024591f202876fbe5224b68f03199
│   ├── e1/
│   │   └── 5a3234d5d2c87e4e6afc226d971e8ab0c65d2b
│   ├── info/
│   └── pack/
└── refs/
    ├── heads/
    │   └── main
    └── tags/
```

Interestingly, that one command actually generated 3 new objects. Even though
we haven't actually added new content to the repo.

{{< hint warning >}}
**Your new objects will likely have different filenames**
Until now, we have all had the same objects. But your commit likely generated 3
objects with different content and thus different hashes
{{< /hint >}}


{{<                                                               page-break >}}
#### New refs

Along with adding the new objects we can also see that a few
file has been created in the `refs` directory


{{< tabs "tree-refs-view" >}}
{{< tab "bash" >}}
```bash
tree -F .git/refs
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
tree /f .git\refs
```
{{< /tab >}}
{{< /tabs >}}


```txt
.git/refs
├── heads/
│   └── main
└── tags/
```

What is in there? Just the hash of a git commit!

{{< tabs "view-heads-main" >}}
{{< tab "bash" >}}
```bash
cat .git/refs/heads/main
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
type .git\refs\heads\main
```
{{< /tab >}}
{{< /tabs >}}

```txt
c71366be637024591f202876fbe5224b68f03199
```

{{<                                                               page-break >}}
#### The commit object

One of the 3 new files is the commit object. Git takes our commit information, builds a file and
then hashes the content of that file and stores it as an object.

But wait, which of the 3 new objects is our commit object? Well, we can tell what it is one of two ways:

1. We can get the commit object hash by looking at the log

```bash
git log --pretty=oneline
```

```txt
c71366be637024591f202876fbe5224b68f03199 (HEAD -> main) Initial Add
```

2. Cat the ref

{{< tabs "view-heads-main2" >}}
{{< tab "bash" >}}
```bash
cat .git/refs/heads/main
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
type .git\refs\heads\main
```
{{< /tab >}}
{{< /tabs >}}

```txt
c71366be637024591f202876fbe5224b68f03199
```

3. Show the head commit

```bash
git show HEAD
```

{{<                                                               page-break >}}
##### Show the commit object

Now, no matter how we got that hash, we can now ask git to show us
the raw content of the object as before.

You can build this command yourself:

```bash
git cat-file -p <hashhere>
```

Or let the shell do it by cating our refs file.

{{< tabs "git-cat-file" >}}
{{< tab "bash" >}}
```bash
git cat-file -p $(cat .git/refs/heads/main)
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
for /F "usebackq delims=" %A in (`type .git\refs\heads\main`) do git cat-file -p %A
```
{{< /tab >}}
{{< /tabs >}}

```txt
tree a5c3a3648323f431095a32f4f5ea86885820b80a
author Carson Anderson <user@email> 1666555792 -0600
committer Carson Anderson <user@email> 1666555792 -0600

Initial add
```


{{<                                                               page-break >}}
#### The root tree object

Now, let's dig in on the tree object. We already know it's hash because it was
in the output of the previous command.

```bash
git cat-file -p <treehashhere>
```

```txt
100644 blob 88cbae6fb9b1ab364976238c1eb6871093ed3860	README.md
040000 tree 1076d1da17d464ae87160a3dd2519cd0f64f3cd5	assets
```


{{<                                                               page-break >}}
#### The nested tree object

Just like before, we can show the subtree:

```bash
git cat-file -p <nestedtreehash>
```

```txt
100644 blob e15a3234d5d2c87e4e6afc226d971e8ab0c65d2b	gopher.png
```

{{<                                                               page-break >}}
#### Object Files

Nearly everything in Git is an object

{{< figure src="git-objects.png" >}}

Even in our trivial repo we can see that we are already building a fairly large
`.git/objects` directory.

> This directory may contain as many files as you expect if you look at it in a "real" repo
> This is because git does garbage collection and actually packs multiple files into single "pack" files
> to help reduce the number of tiny files it creates.


{{<                                                               page-break >}}
#### Non-Object Files

So what isn't an object?

* Branches
* Tags

As we have already seen, all the files in `.git/refs` are not objects but instead are simply
pointers to commit objects.

{{<                                                               page-break >}}
## Another Commit

{{< tabs "another-commit" >}}
{{< tab "bash" >}}
```bash
touch api
echo "## Has an API" >> README.md
git add .
git commit -m "feat: add api"
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
type nul > api
echo "## Has an API" >> README.md
git add .
git commit -m "feat: add api"
```
{{< /tab >}}
{{< /tabs >}}



```bash
git log
```

```txt
commit 5b8b4cf7db77f140bd16de41fb541725c8dacb8a (HEAD -> main)
Author: Carson Anderson <user@email>
Date:   Sun Oct 23 14:23:48 2022 -0600

    feat: add api

commit c71366be637024591f202876fbe5224b68f03199
Author: Carson Anderson <user@email>
Date:   Sun Oct 23 14:09:52 2022 -0600

    Initial add
```

{{<                                                               page-break >}}
### Parent Pointers

We can now have git show us the latest commit content as before

{{< tabs "git-cat-file2" >}}
{{< tab "bash" >}}
```bash
git cat-file -p $(cat .git/refs/heads/main)
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
for /F "usebackq delims=" %A in (`type .git\refs\heads\main`) do git cat-file -p %A
```
{{< /tab >}}
{{< /tabs >}}

```txt {hl_lines=[2]}
tree d3d6344e952d73a8f8a33cab518ef1a1b13d44c7
parent c71366be637024591f202876fbe5224b68f03199
author Carson Anderson <user@email> 1666556628 -0600
committer Carson Anderson <user@email> 1666556628 -0600

feat: add api
```

Now we are starting to build a proper, if short, git commit tree

{{< mermaid >}}
flowchart RL
  8dacb8a --> 8f03199
{{< /mermaid >}}

Or:

{{< mermaid >}}
flowchart RL
  8f03199["feat: add api"] --> 8dacb8a["Initial add"]
{{< /mermaid >}}

{{<                                                               page-break >}}
## Tags

```bash
git tag v0.0.1
```

Now we can see that all we did was make a new tag file in `.git/refs/tags`

{{< tabs "tree-refs-view2" >}}
{{< tab "bash" >}}
```bash
tree -F .git/refs
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
tree /F .git\refs
```
{{< /tab >}}
{{< /tabs >}}

```txt {hl_lines=[5]}
.git/refs
├── heads/
│   └── main
└── tags/
    └── v0.0.1
```

The content is the exact same as our `refs/heads/main` ref

{{< tabs "tail-view" >}}
{{< tab "bash" >}}
```bash
tail .git/refs/*/*
```
{{< /tab >}}
{{< tab "windows" >}}
```bash
type .git\refs\heads\main
type .git\refs\tags\v0.0.1
```
{{< /tab >}}
{{< /tabs >}}

```txt
==> .git/refs/heads/main <==
5b8b4cf7db77f140bd16de41fb541725c8dacb8a

==> .git/refs/tags/v0.0.1 <==
5b8b4cf7db77f140bd16de41fb541725c8dacb8a
```

{{< details "Does this mean you could create a new branch or tag by hand?" >}}
## Sort of!

In it's simplest form, yes! Although as a rule, you shouldn't edit files in `.git` other than the config.

In this case you could quite easily
accidentlly create a branch or tag that points to a bad target. But it *is* possible:

You can try it:

```bash
echo 5b8b4cf7db77f140bd16de41fb541725c8dacb8a > .git/refs/tags/manual-tag
git tag
```

```txt
manual-tag
v0.0.1
```

```bash
echo 5b8b4cf7db77f140bd16de41fb541725c8dacb8a > .git/refs/heads/manual-branch
git branch -a
```

```txt
main
manual-tag
```
{{< /details >}}

{{<                                                               page-break >}}
### `git log` decorations

Now that we have to different files in `refs/heads` pointing at a commit. You can
see that `git log` uses that data to decorate the log.

```bash
git log
```

```txt {hl_lines=[1]}
commit 5b8b4cf7db77f140bd16de41fb541725c8dacb8a (HEAD -> main, tag: v0.0.1)
Author: Carson Anderson <user@email>
Date:   Sun Oct 23 14:23:48 2022 -0600

    feat: add api

commit c71366be637024591f202876fbe5224b68f03199
Author: Carson Anderson <user@email>
Date:   Sun Oct 23 14:09:52 2022 -0600

    Initial add

```

{{<                                                               page-break >}}
## Topic Branches

This is all well and good. But anyone who has used source code systems know
that the ability to collaborate or work on multiple things at once is
where these systems shine (or not!)

We already know that one defining attribute of `git` is that everything is stored
by content in `.git/objects` The other defining attribute is that creating a
branch it git is **easy** and **cheap**!

{{<                                                               page-break >}}
### Create A Branch

```bash
git checkout -b topic
```

{{< tabs "tree-ref-view4" >}}
{{< tab "bash" >}}
```bash
tree -F .git/refs
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
tree /F .git\refs
```
{{< /tab >}}
{{< /tabs >}}

```txt {hl_lines=[4]}
.git/refs/
├── heads/
│   ├── main
│   └── topic
└── tags/
    └── v0.0.1
```

{{<                                                               page-break >}}
### Commit to `topic`

{{< tabs "add-users" >}}
{{< tab "bash" >}}
```bash
touch users
echo "## Has Users" >> README.md
git add .
git commit -m "feat: add users"
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
type nul > users
echo "## Has Users" >> README.md
git add .
git commit -m "feat: add users"
```
{{< /tab >}}
{{< /tabs >}}

Now let's inspect it:

```bash
git log
```

Notice two things

1. The `head -> topic` decoration has moved with our new commit
2. The `tag: v0.0.1,` and `main` decorations are still attached to the previous commit

```txt {hl_lines=[1,7]}
commit 60f4c94a3c4f7af884bdd8d5726fbbca8cdcbdae (HEAD -> topic)
Author: Carson Anderson <user@email>
Date:   Sun Oct 23 14:42:37 2022 -0600

    feat: add users

commit 5b8b4cf7db77f140bd16de41fb541725c8dacb8a (tag: v0.0.1, main)
Author: Carson Anderson <user@email>
Date:   Sun Oct 23 14:23:48 2022 -0600

    feat: add api

commit c71366be637024591f202876fbe5224b68f03199
Author: Carson Anderson <user@email>
Date:   Sun Oct 23 14:09:52 2022 -0600

    Initial add
```

{{<                                                               page-break >}}
### Current Diagram

Now we are starting to see something more interesting. We can see that we have
started work on a new branch. We can of course walk back from the latest commit
in our branch all the way to the first commit to `main`

In a simple diagram flow it looks like this:

{{< mermaid >}}
flowchart RL
  8dacb8a["feat: add users"] --> 8f03199["feat: add api"] --> cdcbdae["Initial add"]
{{< /mermaid >}}

However, we are going to start making use of a more standardized git graph format.
In the new format, the current diagram like the one below. It's the same information
but is much easier to read.

{{< mermaid >}}
%%{init: { 'theme': 'neutral' } }%%

gitGraph
    commit id: "Initial add" tag: "v0.0.1"
    commit id: "feat: add api"
    branch topic
    commit id: "feat: add users"
{{< /mermaid >}}

{{< details "About the git diagrams" >}}
The official [mermaid gitGraphs](https://mermaid.js.org/syntax/gitgraph.html) shown here
are easier to read, but it could give you the impression that git can walk forward from
a commit to tell you all the commits after it. When in reality, this is not how the system works.


Commits have one or more parents. While you could do a lot of work to try and find all the commits that point to a specific parent, it is not a standard use-case for git and not easy to do
{{< /details >}}

{{<                                                               page-break >}}
## Diverging the `main` branch

Now let's go **back** to main and have it make some parallel changes

{{< tabs "add-docs" >}}
{{< tab "bash" >}}
```bash
git checkout main

touch docs.md
echo "## Has Docs" >> README.md
git add .
git commit -m "feat: add docs"
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
git checkout main

type nul > docs.md
echo "## Has Docs" >> README.md
git add .
git commit -m "feat: add docs"
```
{{< /tab >}}
{{< /tabs >}}

We can check the log again.

```bash
git log
```

```txt {hl_lines=[1]}
commit c2d71219e98d8bbd1240e3aa820b32659ad5045c (HEAD -> main)
Author: Carson Anderson <user@email>
Date:   Sun Oct 23 14:50:12 2022 -0600

    feat: add docs

commit 5b8b4cf7db77f140bd16de41fb541725c8dacb8a (tag: v0.0.1)
Author: Carson Anderson <user@email>
Date:   Sun Oct 23 14:23:48 2022 -0600

    feat: add api

commit c71366be637024591f202876fbe5224b68f03199
Author: Carson Anderson <user@email>
Date:   Sun Oct 23 14:09:52 2022 -0600

    Initial add
```

Notice anything missing?

The "feat: add users" commit we created in the `topic` branch is not shown in `main`

This does not mean our commit is gone, far from it! It just means that are on a branch where
our commit chain does not include the commit to `topic`

As far as our branch is concerned, the parent of `feat: add docs` is `feat: add api` and
it always will be.




{{<                                                               page-break >}}
## Unifying the branches

So now we understand fairly well how commits, tags, and branches work. But our repo now has two branches with two different ideas about the history and content of our repo

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

How do we bring these two heads back together?

The [Branching](../branching) section will cover this in detail but for now
let's use a simple `git merge`


{{<                                                               page-break >}}
## Branch Merging

This is where `git merge` comes into play. The branching section of the workshop
will cover this operation and other options in more detail. But for now lets
do it and see what happens!

```bash
git checkout main
git merge topic
```

{{<                                                               page-break >}}
## Branch Merging

```txt
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

😱 Oh no! The dreaded merge conflict! We will cover these and the details
around them in the branching section. But the short version is that our two
branches each have different ideas of what `README.md` should look like.

`git` is actually pretty good at reconciling this situation in most cases. But
in our case, each branch thinks line 3 should have different content and there is
no clear winner. So Git stops the merge and asks us to solve the problem.

For now we are going to manually set the content of `README.md`
and complete the merge.

> You can just save and close the editor prompt that comes up.

{{< tabs "manual-merge" >}}
{{< tab "bash" >}}
```bash
cat > README.md <<EOL
# My Git Repository
## Has an API
## Has Docs
## Has Users
EOL

git add .
git commit
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
(echo # My Git Repository
echo ## Has an API
echo ## Has Docs
echo ## Has Users) > readme.md

git add .
git commit
```
{{< /tab >}}
{{< /tabs >}}

This leaves us with a final git state like this:

{{< mermaid >}}
%%{init: { 'theme': 'neutral' } }%%

gitGraph
    commit id: "Initial add" tag: "v0.0.1"
    commit id: "feat: add api"
    branch topic
    commit id: "feat: add users"
    checkout main
    commit id: "feat: add docs"
    merge topic id: "Merge branch 'topic'"
{{< /mermaid >}}

Want to know about merging or branching? That will be covered in detail
in the [Branching](../branching) section.


{{<                                                               page-break >}}
### The log

Now we can look at our new log for `main`

```bash
git log
```

The output has one line that is specifically interesting. The `Merge:` line

```txt {hl_lines=["2"]}
commit 5fb51187efb652c7da6c3366588e4fa8f2ea9535 (HEAD -> main)
Merge: c2d7121 60f4c94
Author: Carson Anderson <email>
Date:   Sat Mar 11 11:35:33 2023 -0700

    Merge branch 'topic'
    
    # Conflicts:
    #       README.md

commit c2d71219e98d8bbd1240e3aa820b32659ad5045c (HEAD -> main)
Author: Carson Anderson <user@email>
Date:   Sun Oct 23 14:50:12 2022 -0600

    feat: add docs

commit 60f4c947db77f140bd16de41fb541725c8dacb8a (tag: v0.0.1)
Author: Carson Anderson <user@email>
Date:   Sun Oct 23 14:23:48 2022 -0600

    feat: add api

commit c71366be637024591f202876fbe5224b68f03199
Author: Carson Anderson <user@email>
Date:   Sun Oct 23 14:09:52 2022 -0600

    Initial add
```

This is important to understand. When you merge two branches in git you
aren't just adding the changes. Your are actually creating a commit
where you combine the two histories.

> We go into more detail later in the [Branching](../branching) section.


{{<                                                               page-break >}}
### The "merge commit"

Just like before, let's look at the raw contents of our latest commit

{{< tabs "git-cat-file3" >}}
{{< tab "bash" >}}
```bash
git cat-file -p $(cat .git/refs/heads/main)
```
{{< /tab >}}
{{< tab "windows" >}}
```cmd
for /F "usebackq delims=" %A in (`type .git\refs\heads\main`) do git cat-file -p %A
```
{{< /tab >}}
{{< /tabs >}}


Notice we now have **two** parent hashes in the commit. This
is where `git log` got those short hashes.

```txt {hl_lines=["2-3"]}
tree 9521bc081dd964a77a02f756bb52892d00379f07
parent 11e86be1b4ae2bee490d8c51bb6dcbb597964f35
parent b94c70770fc56ff85874eaa43165821403bb4729
author Carson Anderson <email> 1678559733 -0700
committer Carson Anderson <email> 1678559733 -0700

Merge branch 'topic'

# Conflicts:
#	README.md
```

{{<                                                               page-break >}}
## Wrapping Up

You should now have a pretty good grasp of the fundamentals of how git operates
at a low level. Including:

* How git stores files(blob objects) and directories(tree objects)
* How git stores commmits(commit objects)
* How git stores tags and branches (pointer files)
* The basic idea of what git `HEAD` is for
  * To point to a ref or commit

The next section will use all this information to dive deeper on how branching
works and how different kinds of branch management strategies.

<!-- {{<                                                               page-break >}}
## PLACEHOLDER


```bash
```

```txt {hl_lines=[]}
``` -->

{{< page-break last="true" nextRef="../branching" title="Next Section"                            >}}


