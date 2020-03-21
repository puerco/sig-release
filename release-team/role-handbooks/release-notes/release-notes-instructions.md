# Kubernetes Release Notes

The Kubernetes Release Notes team generates/updates the release notes between Kubernetes release cycles.
The Kubernetes Release Notes team is responsible for the following:
1. Updating the Draft Release Notes markdown file kubernetes/sig-release/releases/\<release ver e.g. release-1.18>/release-notes-draft.md
2. Generating the JSON file for the Release Notes Website in kubernetes-sigs/release-notes/src/assets/<release
version>.json (e.g. release-notes-1.18.0-rc.1.json)
3. Update the `assets` list with the release version in kubernetes-sigs/release-notes/src/environments/assets.ts
4. Providing the Release Notes status at Release and Burndown meetings:
  - Green: Complete steps 1-3, submit Pull Requests
  - Yellow: Pull Requests are not done in time
  - Red: Blocker from the tooling or another blocker


# Pre-requisites
Before proceeding, complete the following:

1. Sign the CNCF CLA
2. Become a GitHub Kubernetes Organization member
3. Install Golang
4. Obtain GitHub API Token with the repo scope
https://github.com/settings/tokens
- [X] repo
    - [X] repo: status
    - [X] repo_deployment
    - [X] public_repo
    - [X] repo:invite


# Build the krel tool

The Release Notes team uses the command line tool `krel` to generate release notes.
The `krel` tool with the subcommand `release-notes` generates the Draft Release Notes in markdown (MD) for the
kubernetes/sig-release repo with the option `--create-draft-pr`.
`krel` with the subcommand `release-notes` generates the Patch Release Notes in JSON for the release notes website which
resides in the kubernetes-sigs/release-notes repo with the `--create-website-pr` option.

1. Clone the kubernete/release repo to your local system.
https://www.github.com/kubernetes/release

```
user@laptop:~$ git clone git@github.com:kubernetes/release.git

Cloning into 'release'...
remote: Enumerating objects: 35, done.
remote: Counting objects: 100% (35/35), done.
remote: Compressing objects: 100% (35/35), done.
remote: Total 10304 (delta 10), reused 11 (delta 0), pack-reused 10269
Receiving objects: 100% (10304/10304), 7.76 MiB | 883.00 KiB/s, done.
Resolving deltas: 100% (5942/5942), done.

user@laptop:~$
```

2. Change directory in your local to the release repo

```
user@laptop:~$ cd release

user@laptop:~/release$
```

3. Build the `krel` tool from the release repo

Use `go install` to install `krel` from the cloned repo:

```
user@laptop:~/release$ go install ./cmd/krel

go: downloading github.com/pkg/errors v0.9.1
go: downloading github.com/yuin/goldmark v1.1.25
go: downloading github.com/spf13/cobra v0.0.6
go: downloading sigs.k8s.io/yaml v1.2.0
go: downloading golang.org/x/net v0.0.0-20191004110552-13f9640d40b9
go: downloading github.com/google/go-github/v29 v29.0.3
go: extracting github.com/pkg/errors v0.9.1
go: extracting github.com/yuin/goldmark v1.1.25
...
go: finding k8s.io/utils v0.0.0-20200229041039-0a110f9eb7ab
go: finding gopkg.in/yaml.v2 v2.2.8

user@laptop:~/release$
````

- Use `$GOPATH/bin/krel` to use the `krel` tool
- For the Draft Release Notes in markdown (MD), you will use `krel` and the subcommand `release-notes` and the option `--create-draft-pr`
- For the Patch Release Notes in JSON, you will use `krel` and the subcommand `release-notes` and the option `--create-website-pr`

4. Examine the `krel release-notes` help:

```
user@laptop:~/release$ $GOPATH/bin/krel release-notes --help

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
      --sigrelease-fork-path string   output a copy of the release notes to this directory (default "/var/folders/35/m9zql4dj7t3_bq6qsq567vqc0000gn/T/k8s-sigrelease")
  -t, --tag string                    version tag for the notes

Global Flags:
      --cleanup            cleanup flag
      --log-level string   the logging verbosity, either 'panic', 'fatal', 'error', 'warn', 'warning', 'info', 'debug' or 'trace' (default "info")
      --nomock             nomock flag
      --repo string        the local path to the repository to be used (default "/var/folders/35/m9zql4dj7t3_bq6qsq567vqc0000gn/T/k8s")

user@laptop:~/release$
```
- Note the `--repo string`, the parent directory is the local path krel will use to clone the kubernetes/kubernetes repo
and the kubernetes/sig-release repo to.
In this example `krel release-notes` will clone kubernetes/kubernetes to
`/var/folders/35/m9zql4dj7t3_bq6qsq567vqc0000gn/T/k8s` and clone kubernetes/sig-release to
`/var/folders/35/m9zql4dj7t3_bq6qsq567vqc0000gn/T/k8s-sigrelease`


# Generate the Draft Release Notes for the kubernetes/sig-release repo

The `krel release-notes` is used to generate or modify the release-notes-draft.md file in the
kubernetes/sig-release/releases/\<release version> e.g. kubernetes/sig-release/releases/release-1.18/release-notes-draft.md

`krel release-notes` will create the branch, generate or modify (if existing) the release-notes-draft.md file, make the
commit, and push to the new branch in your fork of kubernetes/sig-release.

1. If you ran the `krel release-notes` tool before and re-use the local k/sig-release repo. The tool will error.
It may produce an error similar to:
`FATA failed to create release notes draft PR: cloning k/sig-release: unable to pull from remote: command /usr/bin/git
pull --rebase did not succeed: There is no tracking information for the current branch. Please specify which branch you
want to rebase against.`

Delete the local k/sig-release. You will find the directory by running `krel release-notes --help`.
You system may have a different location. The following example is from a Mac.

```
user@laptop:~$ $GOPATH/bin/krel release-notes --help

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
      --sigrelease-fork-path string   output a copy of the release notes to this directory (default "/var/folders/35/m9zql4dj7t3_bq6qsq567vqc0000gn/T/k8s-sigrelease")
  -t, --tag string                    version tag for the notes

Global Flags:
      --cleanup            cleanup flag
      --log-level string   the logging verbosity, either 'panic', 'fatal', 'error', 'warn', 'warning', 'info', 'debug' or 'trace' (default "info")
      --nomock             nomock flag
      --repo string        the local path to the repository to be used (default "/var/folders/35/m9zql4dj7t3_bq6qsq567vqc0000gn/T/k8s")

user@laptop:~$
```
- Note the `--repo string` path `/var/folders/35/m9zql4dj7t3_bq6qsq567vqc0000gn/T/k8s`. `krel` clones the
kubernetes/kubernetes repo in this directory and uses this repo to generate the release notes.
- `krel` also clones the `kubernetes/sig-release` repo also in the `k8s-sigrelease` directory under the same parent
directory and under  e.g.
/var/folders/35/m9zql4dj7t3_bq6qsq567vqc0000gn/T/k8s-sigreleasse
> The parent directory of the local kubernetes/kubernetes repo and kubernetes/sig-release repo may be different, in
> Linux the directory is under `/tmp`
> Always run `krel release-notes --help` to lookup the directory in your local system

If you have generated the Draft and Patch Release Notes with `krel` previously, delete the `krel` cloned `kubernetes/sig-release` repo.
Go to the parent of the `--repo` directory and delete the k8s-sigrelease directory:

```
user@laptop:~$ cd /var/folders/35/m9zql4dj7t3_bq6qsq567vqc0000gn/T/

user@laptop:~/T$ ls | grep k8s

k8s
k8s-sigrelease

user@laptop:~/T$ rm -rf k8s-sigrelease
```

If the `krel` cloned `kubernetes/kubernetes` repo in the k8s directory is outdated, delete the directory also:

```
user@laptop:~/T$ rm -rf k8s

user@laptop:~$ cd ~
```

2. Fork the kubernetes/sig-release to your GitHub account.
Go to `https://github.com/kubernetes/sig-release` and fork the repo.

3. Clone your fork to your local system:

```
user@laptop:~$ git clone git@github.com:kubernetes/sig-release.git

Cloning into 'sig-release'...
remote: Enumerating objects: 28, done.
remote: Counting objects: 100% (28/28), done.
remote: Compressing objects: 100% (25/25), done.
remote: Total 6614 (delta 8), reused 12 (delta 3), pack-reused 6586
Receiving objects: 100% (6614/6614), 8.37 MiB | 8.67 MiB/s, done.
Resolving deltas: 100% (3880/3880), done.

user@laptop:~$
```

4. Change directory in your local to the cloned sig-release repo

```
user@laptop:~$ cd sig-release

user@laptop:~/sig-release$
```

5. To stay current with the upstream, add an upstream remote to the kubernetes/sig-release repo:

```
user@laptop:~/sig-release$ git remote add upstream git@github.com:kubernetes/sig-release.git

user@laptop:~/sig-release$
```

6. Confirm the upstream remote

```
user@laptop:~/sig-release$ git remote -v

origin	git@github.com:reylejano-rxm/sig-release.git (fetch)
origin	git@github.com:reylejano-rxm/sig-release.git (push)
upstream	git@github.com:kubernetes/sig-release.git (fetch)
upstream	git@github.com:kubernetes/sig-release.git (push)

user@laptop:~/sig-release$
```

7. Export your GitHub token:

```
user@laptop:~/sig-release$ export GITHUB_TOKEN=<git hub token>
```

8. Update your local sig-release repo from upstream:

```
user@laptop:~/sig-release$ git pull upstream master

From github.com:kubernetes/sig-release
 * branch            master     -> FETCH_HEAD
Already up to date.

user@laptop:~/sig-release$
```

9. Generate the release notes draft and PR with `krel release-notes` with the `--create-draft-pr` option. You must the
following options:
- `--draft-org` which is your GitHub account
- `--tag` which is the Kubernetes release version e.g. v1.18.0-rc.1

>N.B. `krel release-notes --create-draft-pr` will do the following:
> - create a branch in your cloned fork of `kubernetes/sig-release`
> - generate the release-notes-draft.md in kubernetes/sig-release/releases/\<release ver e.g.
> release-1.18>/release-notes-draft.md
> - commit the changes
> - push the commit and new branch to your fork

```
user@laptop:~/sig-release$ $GOPATH/bin/krel release-notes \
--create-draft-pr \
--draft-org=reylejano-rxm \
--tag v1.18.0-rc.1

INFO cloning/updating repository kubernetes/kubernetes
INFO using found start SHA: 49dfa8690488c6658b3e334dd3737c7eb465c639
INFO using found end SHA: dbbed7806681109f541264ab37284f9a51c87fcc
INFO fetching all commits. This might take a while...
INFO starting to process commit 1 of 47 (2.13%): adf4ccab61d4fecd4af3ba12d5e4f1fb3ae58c96
INFO starting to process commit 2 of 47 (4.26%): 2d981b36c9875277098397b2938e9a83ce02795a
INFO starting to process commit 3 of 47 (6.38%): 84dc7046797aad80f258b6740a98e79199c8bb4d
...
INFO Verifying release-notes-draft-v1.18.0-rc.1 branch exists on the remote
INFO Pushing release notes draft to reylejano-rxm/sig-release
remote:
remote: Create a pull request for 'release-notes-draft-v1.18.0-rc.1' on GitHub by visiting:
remote:      https://github.com/reylejano-rxm/sig-release/pull/new/release-notes-draft-v1.18.0-rc.1
remote:
To github.com:reylejano-rxm/sig-release
 * [new branch]      release-notes-draft-v1.18.0-rc.1 -> release-notes-draft-v1.18.0-rc.1

 user@laptop:~/sig-release$
```

10. If you see that `krel release-notes` has given instructions on how to `Create a pull request` with a GitHub link then
`krel release-notes` has created a new branch in your fork e.g. release-notes-draft-v1.18.0-rc.1, committed the Release Notes draft in
markdown e.g. kubernetes/sig-release/releases/1.18/release-notes-draft.md, and pushed the commit to the new branch in
your fork.

11. Pull the new branch created by `krel release-notes` and pushed to your fork:
```
user@laptop:~/sig-release$ git pull origin release-notes-draft-v1.18.0-rc.1

remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 5 (delta 4), reused 5 (delta 4), pack-reused 0
Unpacking objects: 100% (5/5), done.
From github.com:reylejano-rxm/sig-release
 * branch            release-notes-draft-v1.18.0-rc.1 -> FETCH_HEAD
 * [new branch]      release-notes-draft-v1.18.0-rc.1 -> origin/release-notes-draft-v1.18.0-rc.1
Merge made by the 'recursive' strategy.
 releases/release-1.18/release-notes-draft.md | 43 +++++++++++++++++++++++++++++++++++++++++++
 1 file changed, 43 insertions(+)

user@laptop:~/sig-release$
```

Checkout the new branch e.g. release-notes-draft-v1.18.0-rc.1:

```
user@laptop:~/sig-release$ git checkout release-notes-draft-v1.18.0-rc.1

Branch 'release-notes-draft-v1.18.0-rc.1' set up to track remote branch 'release-notes-draft-v1.18.0-rc.1' from 'origin'.
Switched to a new branch 'release-notes-draft-v1.18.0-rc.1'

user@laptop:~/sig-release$
```

12. Amend the commit with your personal information before creating the Draft PR:

```
user@laptop:~/sig-release$ git commit --amend --author="Rey Lejano <rey.lejano@rx-m.com>" --signoff

user@laptop:~/sig-release$
```

13. Force push the amended commit with `git push -f userfork branchname`:

```
user@laptop:~/sig-release$ git push -f git@github.com:reylejano-rxm/sig-release.git release-notes-draft-v1.18.0-rc.1

user@laptop:~/sig-release$ cd ~

user@laptop:~$
```

14. Create the Pull Request
You can go to your GitHub fork and click on create PR or create PR by visiting:
https://github.com/<Your GitHub Account>/sig-release/pull/new/release-notes-draft-v1.18.0-rc.1
e.g.
https://github.com/reylejano-rxm/sig-release/pull/new/release-notes-draft-v1.18.0-rc.1

Edit the Title, add the command used in the Description, and cc appropriate people like the team lead and team members:
Title:
```
Release Notes Draft for k/k v1.18.0-rc.1
```
Description:
```
What type of PR is this?
/kind documentation

What this PR does / why we need it:
Generated draft of release notes with krel for k/k v.18.0-rc.1

Command used:
krel release-notes --create-draft-pr --draft-org=<Your GitHub Account> --tag v1.18.0-rc.1

The commit was amended with my personal info with
git commit --amend --author="Rey Lejano rey.lejano@rx-m.com" --signoff

Which issue(s) this PR fixes:

Special notes for your reviewer:

cc/ @saschagrunert @evillgenius75 @puerco
```

15. Pay attention to Prow's comments on the Pull Request as you may need to assign an Assignees.

16. After your Pull Request has merged, delete your fork.


# Generate the Patch Release Notes (JSON) for the Release Notes Website and Modify assets.ts In kuburnetes-sigs/release-notes

1. Fork the Release Notes Repo
https://github.com/kubernetes-sigs/release-notes

2. Clone the repo

```
user@laptop:~$ git clone git@github.com:reylejano-rxm/release-notes.git

Cloning into 'release-notes'...
remote: Enumerating objects: 183, done.
remote: Counting objects: 100% (183/183), done.
remote: Compressing objects: 100% (114/114), done.
remote: Total 1611 (delta 132), reused 107 (delta 69), pack-reused 1428
Receiving objects: 100% (1611/1611), 1.77 MiB | 5.74 MiB/s, done.
Resolving deltas: 100% (1064/1064), done.

user@laptop:~$
```

3. Change directory to the release-notes local repo:

```
user@laptop:~$ cd release-notes

user@laptop:~/release-notes$
```

4. To stay current with the upstream, add the upstream remote:
```
user@laptop:~/release-notes$ git remote add upstream git@github.com:kubernetes-sigs/release-notes.git

user@laptop:~/release-notes$
```

5. Confirm the upstream remote:

```
user@laptop:~/release-notes$ git remote -v

origin	git@github.com:reylejano-rxm/release-notes.git (fetch)
origin	git@github.com:reylejano-rxm/release-notes.git (push)
upstream	git@github.com:kubernetes-sigs/release-notes.git (fetch)
upstream	git@github.com:kubernetes-sigs/release-notes.git (push)

user@laptop:~/release-notes$
```

6. Pull from upstream
```
user@laptop:~/release-notes$ git pull upstream master

From github.com:kubernetes-sigs/release-notes
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> upstream/master
Already up to date.

user@laptop:~/release-notes$
```

7. Create a new branch for the release notes patch:
```
user@laptop:~/release-notes$ git checkout -b 'v1.18.0-rc.1_release_notes_patch'

Switched to a new branch 'v1.18.0-rc.1_release_notes_patch'

user@laptop:~/release-notes$
```

8. Generate Release Notes Patch in JSON with the `krel release-notes` with the `--create-website-pr` option.
You must supply the following options:
- `--tag` which is the Kubernetes release version e.g. v1.18.0-rc.1
- `--website-org` which is your GitHub account
- `--website-repo=release-notes`

>N.B. `krel release-notes --create-website-pr` will do the following:
> - create a branch in your cloned fork of `kubernetes-sigs/release-notes`
> - generate the JSON file for the Release Notes Website in kubernetes-sigs/release-notes/src/assets/<release
> version>.json (e.g. release-notes-1.18.0-rc.1.json)
> - update the `assets` list with the release version in kubernetes-sigs/release-notes/src/environments/assets.ts
> - commit the changes
> - push the commit and new branch to your fork

```
user@laptop:~/release-notes$ $GOPATH/bin/krel release-notes \
  --create-website-pr \
  --tag v1.18.0-rc.1\
  --website-org=reylejano-rxm \
  --website-repo=release-notes

INFO cloning/updating repository kubernetes/kubernetes
INFO using found start SHA: b7dcc4ac29812042769148679de1fbf54580ac7e
INFO using found end SHA: f2d7577e31829664899f1b8e3d3a73de8c5f4029
INFO fetching all commits. This might take a while...
INFO starting to process commit 1 of 243 (0.41%): 5cc572f798a840030a20b80761214d675a4a9386
INFO starting to process commit 2 of 243 (0.82%): 6ec3ea855d22b30d8b8cef5657bb6ec78f4dfd7b
...
IINFO starting to process commit 241 of 243 (99.18%): 7c12251c7a6e88929db97c28199febdf467c384e
INFO starting to process commit 242 of 243 (99.59%): 8e9d76f1b95be03e213f99509d052c7dcd58f492
INFO starting to process commit 243 of 243 (100.00%): ddc18eae67cc7973679c1c46bfb2a217148edda0
ERRO getting the release note from commit 3631887a284e550a23aa16ebe6095e4b9427b673 (PR #87215): no matches found when parsing note text from commit string
INFO got the commits, performing rendering
INFO release notes written to file                 format=json path=src/assets/release-notes-1.18.0-rc.1.json

user@laptop:~/release-notes$
```

9. Verify the generated patch release notes is saved in `src/assets`
e.g. `src/assets/release-notes-1.18.0-rc.1.json`


10. Verify the `assets` list in `src/environments/assets.ts` was updated with the new version to the [0] index position of the list e.g. `assets/release-notes`:

e.g.
```
export const assets = [
  'assets/release-notes-1.18.0-rc.1.json',
  'assets/release-notes-1.18.0-beta.2.json',
```

11. Create a Pull Request.
Go to your release-notes fork and branch for the patch to create the Pull Request:

Edit the Title, add the commands used in the Description, and cc appropriate people like the team lead and team members:

Title:
```
Patch relnotes.k8s.io with release v1.18.0-rc.1
```

Description:
```
krel release-notes \
--create-website-pr \
--tag v1.18.0-rc.1\
--website-org=reylejano-rxm \
--website-repo=release-notes

cc/ @saschagrunert @evillgenius75 @puerco @JamesLaverack
```

12. Pay attention to the Prow comments to assign the appropriate Assignee(s).

13. Delete your fork when the Pull Request has merged.

