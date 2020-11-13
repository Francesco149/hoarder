legacy scripts that aren't used anymore

## `hoard-move-repos <path>`
checks every subdirectory (not recursively) and if it's a git repo it creates a clean copy in
"/path/to/hoard/git". if the git repo is already considered clean (all branches exist upstream and
no unstaged changes) it will be moved directly

## `git-is-clean <path>`
returns true only if the repository is absolutely clean (including no gitignored files present)
note that this only checks that it's locally clean. it could still have diverged from upstream

* path: the path to the repository. defaults to current dir if not specified

## `git-fetch-all <path>`
does a full multithreaded fetch on the repo. does not prune local tags and refs.
this is non-interactive. any password prompts will fail instantly

* path: the path to the repository. defaults to current dir if not specified

## `git-is-upstream <upstream-branch> <ref>`
checks that ref exists in upstream-branch

* upstream-branch: the name of the upstream branch, such as origin/master
* ref: defaults to HEAD

## `git-each-upstream-branch <command>`
runs `<command> <upstream-branch> <branch>` for each branch, where upstream-branch is the name of
the upstream branch that was automatically detected and branch is the name of the corresponding
local branch

fails when upstream branch cannot be determined or when command fails

* command: the shell command to run

## `git-recurse <command>`
runs `<command>` on the git repo and all of its submodules recursively

## `git-checkout-and-reset <upstream-branch> <branch>`
does a `git-super-clean`, checks out branch and hard resets it to upstream-branch


## `hoard-prefix-dir <path>`
adjust path so the last element is prefixed by a directory named according to the first
letter of the last element

for example, `~/hoard/git/Example` becomes `~/hoard/git/E/Example`

## `github-clone-starred-repos`
clones all github repos starred by `HOARDER_GITHUB_STARRED_USERS`. keeps track of the starred repos
that have already been seen to not abuse the github api too hard

lists of repos are kept at /path/to/hoard/github-repos-username
