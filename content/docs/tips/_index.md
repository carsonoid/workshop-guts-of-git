---
weight: 0
title: Bonus Git Tips
bookCollapseSection: false
---

# Git Tips

This page summarizes a few quick tips that can help make your git experience better.

## Command Aliases

Sick of typing common git commands? You can use a git `alias` to
save some time.

Here are a few examples:

```bash
# get the status with "git s"
git config --global alias.s 'status'

# rebase on the latest main with "git pr"
git config --global alias.pr 'pull --rebase origin/main'
```

You can of course add any aliases that you want. These new features will then
be availabile from any repo and are independent of the shell you use.

## Git Prompts

Another thing that can be really useful is to add extra information about
the state of your git repo directly into your prompt!

Luckily you don't have to build this yourself, you can use some existing
tools to get a quick value-add.

### For Bash Users

https://github.com/magicmonty/bash-git-prompt

### For Zsh Users

https://github.com/olivierverdier/zsh-git-prompt

### What you get

The exact format will change depending on your shell and how you
configure the prompt add on. But here are some examples to
help you see the value added:

```sh
# Clean state
carson@weave /home/carson/tmp/myrepo [main {origin/main}|✔]

# New Files
carson@weave /home/carson/tmp/myrepo [main {origin/main}|..1]

# Unpushed Commits
carson@weave /home/carson/tmp/myrepo [main {origin/main}↑·1|✔]

# Diverged
carson@weave /home/carson/tmp/myrepo [main {origin/main}↓·3↑·1|✔]
```

## Coventional Commits

As you start to really understand git and hopefully embrace the use
of `rebase -i` to keep pristine git histories it's often useful to standardize
a bit more on your commit messages so you can get even more out of them.


https://www.conventionalcommits.org/en/v1.0.0/


> The Conventional Commits specification is a lightweight convention on top of commit messages. It provides an easy set of rules for creating an explicit commit history; which makes it easier to write automated tools on top of.
