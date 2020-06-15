collection of scripts and tools to hoard various kinds of data

current features

* automatically fetch your starred repos on github and archive clean local clone of them
* take existing local repos and archive a clean copy of them
* keep archived repos up to date and clean
* all hoard directories are split into subdirectories based on the first letter of the name.
  this can help speeding up file lookup
* keep track of posts on ragezone and commit them as bbcode to a local git repository, tracking
  any changes

# requirements

* a posix shell
* git 2.3 or newer
* ruby
* `gem install --user octokit netrc`

optional but recommended: set up a `~/.netrc` with your github token so you don't get rate limited
as much. make sure it's not world readable (`chmod 600`)

```
machine api.github.com
  login YourGithubUsername
  password xxxx
```

# config

you can customize various settings by creating a `config.sh` file at `~/.config/hoarder/config.sh`
and exporting any of the following variables

* `HOARDER_DIR` base directory where all of the hoarded stuff is stored. defaults to `~/hoard`
* `HOARDER_GIT_JOBS` number of parallel jobs to use with git when applicable.
  defaults to `nproc --all`
* `HOARDER_GITHUB_STARRED_USERS` users to clone starred repositories for, as a space separated list

# scripts

it's up to you to use these scripts in cronjob, daemons or however you want to set it up

## `hoard-move-repos <path>`
checks every subdirectory (not recursively) and if it's a git repo it creates a clean copy in
"/path/to/hoard/git". if the git repo is already considered clean (all branches exist upstream and
no unstaged changes) it will be moved directly

## `hoard-clone-starred-repos`
clones all github repos starred by `HOARDER_GITHUB_STARRED_USERS`. keeps track of the starred repos
that have already been seen to not abuse the github api too hard

lists of repos are kept at /path/to/hoard/github-repos-username

## `hoard-github [options] [command [options]]`
interface for the github api. see `hoard-github --help`

## `ragezone-get-post post`
gets bbcode for a post on ragezone. NOTE: output is wrapped in some weird xml node

see `ragezone-parse-url` for the post argument

## `ragezone-track-post post description`
## `ragezone-untrack-post post`
tracks the given post. tracked posts will be committed to a git repository in
`/path/to/hoarder/ragezone` as bbcode and kept updated by `ragezone-update`

calling track again for the same post will just update the description

see `ragezone-parse-url` for the post argument

## `ragezone-update`
check all tracked ragezone posts and commit any changes

## `ragezone-parse-url post`
returns the post id for a ragezone url. fails if the url format is not recognized

* post: either a link to the specific post, such as
  `http://forum.ragezone.com/f420/some-post-666-post123/#post123`
  or the post id (123 in this example)

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

## `hoard-source-config`
sources hoarder config, exports all the vars, applies default values, exports various util funcs
that depend on the config
