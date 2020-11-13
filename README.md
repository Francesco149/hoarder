collection of scripts and tools to hoard various kinds of data

current features

* automatically fetch your starred repos on github and archive clean local clone of them
* take existing local repos and archive a clean copy of them
* keep archived repos up to date and clean
* all hoard directories are split into subdirectories based on the first letter of the name.
  this can help speeding up file lookup
* keep track of posts on ragezone and commit them as bbcode to a local git repository, tracking
  any changes
* automatically download links from ragezone posts

# requirements

* a posix shell (bash for badown-continue and the ragezone downloader)
* git 2.3 or newer
* ruby
* `gem install --user octokit netrc`
* curl
* wget
* [badown](https://github.com/stck-lzm/badown)

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

## `hoard-update-repos`
clones and updates all tracked git repos

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
check all tracked ragezone posts and commit any changes, then download any new links that can be
handled by `hoard-download`

## `ragezone-parse-url post`
returns the post id for a ragezone url. fails if the url format is not recognized

## `ragezone-download post`
download all recognized urls in a post. files are saved to
`/path/to/hoarder/ragezone-files/post_id`

see `ragezone-parse-url` for the post argument

* post: either a link to the specific post, such as
  `http://forum.ragezone.com/f420/some-post-666-post123/#post123`
  or the post id (123 in this example)

## `hoard-github [options] [command [options]]`
interface for the github api. see `hoard-github --help`

## `hoard-download url`
download from various file sharing services. currently supports stackstorage, zippyshare, mega,
mediafire

## `stackstorage-download url`
download something from stackstorage. resumes partial downloads and doesn't redownload the same file

## `badown-continue url`
wrapper for [badown](https://github.com/stck-lzm/badown) that resumes downloads like
`stackstorage-download`

## `hoard-source-config`
sources hoarder config, exports all the vars, applies default values, exports various util funcs
that depend on the config
