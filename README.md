These files make up my bash profile. There's a `.bashrc`, a `.bash_profile` and a bunch of helpers.

Features:
 - color prompt.
 - terminal title bar follows prompt.
 - git status.

## Git Prompt

It works by running `git -c color.status=off status --short --branch` which outputs more-easily machine-readable output, such as:

### No repository.

    fatal: Not a git repository (or any of the parent directories): .git

### Branch header

The first line of the output is the branch details, prefixed by two '#' characters:

`git init`: If you've only just initialised a repository, you get:

    ## Initial commit on master

Once you've committed something, it changes to:

    ## master

TODO: branch.
TODO: ahead.
TODO: behind.
TODO: ahead and behind.

### File status

After the branch header, the output is one line per file, showing each file with a two character prefix, as `XY filename`, where 'X' refers to the index status, and 'Y' refers to the working status. Renames/moves are shown as 'XY bar -> baz'.

In the `tests` folder, there's a script which creates a new git repo and working directory, and populates it with all of the states that I could think of. When run, the output looks like this:

    ## master
    D  deleted
     D missing
     M modified
    M  modified_added
    R  old -> new
    A  new_added
    ?? untracked

TODO: Of course, the real question here is: "What do I actually want to see in the prompt?"

## What do I actually want to see in the prompt?

- The branch name.
- Whether I've got anything to do. That is:
  - Do I have modified/untracked files that need staging?
  - Do I have staged files that need pushing? That is: am I ahead?
  - Do I have files that need merging? That is: am I behind?

Do I _really_ need to know the difference between these states?

The answer: in order for a piece of information to be useful to me, it must be actionable. That is: there must be something I can do about it. In the cases above, there are different actions that I might take:
- Nothing.
- Add files to, or remove files from the index.
- Commit, or revert the index.
- Push
- Pull

So, I guess that's pretty much all I really want to know.

Aside: posh-git uses the following:

By default, the status summary has the following format:

[{HEAD-name} +A ~B -C !D | +E ~F -G !H]
{HEAD-name} is the current branch, or the SHA of a detached HEAD

Cyan means the branch matches its remote
Green means the branch is ahead of its remote (green light to push)
Red means the branch is behind its remote
Yellow means the branch is both ahead of and behind its remote

ABCD represent the index; EFGH represent the working directory
+ = Added files
~ = Modified files
- = Removed files
! = Conflicted files
As in git status, index status is dark green and working directory status is dark red

### Ahead/Behind

How do I get a git repo to be ahead of / behind / both a remote? First I need a remote.

    $ git daemon --verbose --export-all --base-path=. --base-path-relaxed --reuseaddr .

