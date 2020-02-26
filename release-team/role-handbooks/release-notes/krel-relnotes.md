# Release Notes tooling in krel

As of early 2020 work is in progress to integrate the old `release-notes` tool into [`krel`](https://github.com/kubernetes/release/tree/master/cmd/krel). At the time of this writing, krel is already capable of writing the release notes in json and markdown formats. Work is already in place to automate the draft PR generation which must be filed after each release cut.

## krel release-notes Subcommand Options

These are the current release notes options in krel

```
krel release-notes

The 'release-notes' subcommand of krel has been developed to:

1. Generate the release notes for the provided tag for commits on the master
   branch. We always use the master branch because a release branch
   gets fast-forwarded until we hit the first release candidate (rc). This is
   also the reason why we select the first 'v1.xx.0-rc.1' as start tag for
   the notes generation.

2. Put the generated notes into a release notes draft markdown document and
   create a GitHub pull request targeting to update the file:
   https://github.com/kubernetes/sig-release/blob/master/releases/release-1.xx/release-notes-draft.md

3. Put the generated notes into a JSON file and create a GitHub pull request
   to update the website https://relnotes.k8s.io.

To use the tool, please set the GITHUB_TOKEN environment variable which needs write
permissions to your fork of k/sig-release and k-sigs/release-notes.

Usage:
  krel release-notes [flags]

Flags:
      --create-draft-pr               create the Release Notes Draft PR. --draft-org and --draft-repo must be set along with this option
      --draft-org string              a Github organization owner of the fork of k/sig-release where the Release Notes Draft PR will be created
      --draft-repo string             the name of the fork of k/sig-release, the Release Notes Draft PR will be created from this repository (default "sig-release")
      --format string                 The format for notes output (options: markdown, json) (default "markdown")
  -h, --help                          help for release-notes
  -o, --output-dir string             output a copy of the release notes to this directory (default ".")
      --sigrelease-fork-path string   output a copy of the release notes to this directory (default "/tmp/k8s-sigrelease")
  -t, --tag string                    version tag for the notes

Global Flags:
      --cleanup            cleanup flag
      --log-level string   the logging verbosity, either 'panic', 'fatal', 'error', 'warn', 'warning', 'info', 'debug' or 'trace' (default "info")
      --nomock             nomock flag
      --repo string        the local path to the repository to be used (default "/tmp/k8s")

```
## Get a GitHub token!

The release notes gatherer puts a heavy load on the GitHub API. You must [get a GitHub token](https://github.com/settings/tokens) before running krel. Your token will need to have the following scopes enabled:

- [x] repo

[TBD as more scopes will be needed to create the PR]


## Generating the release notes draft and PR with krel

krel is currently capable of generating the release notes draft and cloning, branching and committing the draft to your fork of [k/sig-release](https://github.com/kubernetes/sig-release). As this is still work in progress, there are still some pitfalls to avert.

To generate the release notes draft, you must run `krel release-notes` at minimum with the `--create-draft-pr` flag and your GitHub user (or org) defined with `--draft-org`. You should also specify the current kubernetes/kubernetes `--tag` just to make sure you are __really__ working on the release you want.

This command will do a full clone of k/k, process the commit messages and compile them into the release-notes draft (note that this process can take a while). If it succeeds, it will then clone k/sig-release, add your fork as a remote, create a branch, write the draft and push it back up to your fork.

As PR creation is not (yet) ready, you need to create it manually. But please see pitfall #1 before filing your PR!

This is an example command to generate the release notes for version 1.18.0-beta.0. Note that before running you must export your token in an environment variable:

```bash
export GIT_TOKEN=YOUR_TOEKN_HERE
krel release-notes \
     --create-draft-pr
     --draft-org=YOURORG
     --tag v1.18.0-beta.0
```

## Pitfalls and Current Shortcomings
We currently are working on the following issues that must be taken into account when generating the release notes.

#### 1. You need to amend your commit before creating the Draft PR
Currently, krel has a [hardcoded git identity](https://github.com/kubernetes/release/blob/c99628f49d8583cdb7a4a60ec608309165c01f0b/pkg/git/git.go#L726) which will go in your commit when you run it. Before creating the PR, amend the commit with your personal information and push it to you fork:

```bash
git commit --amend --author="First Last Name <email-address>" --signoff
git push -f userfork BRANCHNAME
```

Note that `userfork` is the fixed name of the remote and that you need to specify the real branch name (`release-notes-draft-v1.18.0-beta.0` in the example above). You must use the email address authorized by the CNCF.

If you forget to do this, your PR will fail the CLA test.

#### 2. There is a problem re-using a k/sig-release repository
If you try to reuse a local repository of k/sig-release, the process will probably fail. This includes re-running krel if it failed for some reason.

We are [currently investigating this issue](https://github.com/kubernetes/release/pull/1126) but to avoid now, delete the local copy of `k/sig-release` where krel is working before running it (on Linux it defaults to `/tmp/k8s-sigrelease`).



