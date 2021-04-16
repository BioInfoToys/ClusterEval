# Development conventions and process

## Workflows for collaborators

### Preparing to collaborate

* Fork the repo on GitHub.
* Clone a local copy of your fork.
* Link your local copy to the upstream original.

### Collaboration workflow - small feature

* Communicate your intended code change in a relevant issue or discussion on the upstream repo.
* If either `main` or `dev` is out of date, pull from upstream to your local repo copy, and push to your fork.
* Checkout the `dev` branch.
* Verify the version in the DESCRIPTION file is formatted like #.#.#.# (four segments).
* Create and switch to a new local feature branch to code on.
* Iterate development on the local feature branch, discuss as needed in the upstream repo.
* When feature is complete (with tests and documentation), complete the pull-request checklists

## Pull-request checklist

This checklist is a mix of checks that should be done and tasks that are required to make a pull requests. You should consider everything here before submitting a pull request. How much consideration depends on the importance of the feature, the size of the changes made to implement it, the problems caused if you break what you were working on, and how shiny you want your reputation.

### Prepare to publish

[] All work is done:

   * All planned work is done, rescheduled, or re-planned.
   * Relevant feedback in online discussions or via issues are addressed.
   * Personal task tracking is updated, if any

[] Local and forked repos are clean and up-to-date.

   * Correct feature branch checked out locally.
   * No uncommitted changes.
   * Local and forked repos both up-to-date with upstream.

[] Documentation is complete

   * All components documented? - functions? objects? data? scripts?
   * All appropriate user docs updated? - readme? package doc? vignettes? [not news!]
   * Any developer docs affected? - todo? milestones? before-release notes?
   * All modified files spell-checked.

[] Code is clean

   * Clean, rebuild (including documentation), and install all have no errors.
   * All tests pass.
   * Code coverage is checked (new/modified code should have tests).
   * R command check.

The final step, R command check, need not necessarily pass for packages not being submitted to CRAN, but you need a good reason to ignore any warning or error it reports. If you took a long time working through the checklist, double-check that there have been no additional commits on master or dev to sync. If anything significant was changed, you should probably start over.

[] Package feature for pull request
   
   * Local feature branch rebased on current `dev`, squashing to one commit.
   * DESCRIPTION file version is four digits.
   * Clean, rebuild (including documentation), install.
   * R command check.

The last two repeat automated checks just to be sure the rebase worked as expected. If there were significant conflicts you should review everything from scratch to verify nothing got messed up during conflict resolution. It is unfortunately easy to introduce a bug that way, especially in things that can't easily be tested, like documentation.

[] Final manual review

   * Try out code from user perspective - take it for a test drive.
   * Manually inspect all modified documentation - just one missing markdown "*" and half your documentation is in bold.

[] Submit pull request

## Project branches

This describes the long-term shared branches in the repository, how they evolve, and why they are set up and evolve as described.

### The `main` branch

This intended to be a sequence of releases, each tagged with its release version. Release versions follow semantic versioning and will always be in the "#.#.#" format, without a leading "v".

Every tagged commit is a release and every release is a tagged commit. However, due to errors and mistakes, there may be extra, untagged commits, skipped releases, and/or withdrawn releases. Once used, a release tag will never be reused to tag a different commit, ensuring releases are uniquely identified by their version. Untagged commits can always be identified by their unique hash.

A skipped release is created when internal work on a version stalls or is abandoned and instead a later version that was in development in parallel is released instead. Skipped releases should be documented in the changes file.

A withdrawn release is created when a released version is discovered to have significant errors. In this case the release tag is deleted, although the commit remains. Withdrawn releases should be documented in the changes file.

### The `dev` branch

This intended to be an integration branch and a staging area for releases to `main`. Some commits  may be tagged to facilitate identification, e.g. to identify a release candidate for testing. Developer tags will be formatted as #.#.#.# with the leading three numbers identifying the parent release version being modified, and the fourth being a strictly increasing integer > 9000 representing the development "release". Development version tags are similar to release versions, but do *not* follow semantic versioning. Semantic versioning would requires e.g. 1.2.3.9001 to be a step on the path before version 1.2.3 instead of a step on the path after. R requires this deviation if developer versions are to be used as package versions. Once a tag is assigned to a development commit, it should not be reused. If a commit is tagged in error, the tag can be deleted only after a following tag in sequence has been used. This prevents reuse of the deleted tag. A commit just to facilitate this may be required. It is fine to tag a commit with a different tag if that can be done without otherwise breaking the rules.

### Topic branches

Development happens in parallel on topic branches, but these are generally only visible in local repo clones or in contributor forks.

### Extended development

#### `<major-topic>` branches

Having only one branch for integration can get to be a problem when development is happening simultaneously on a large, cross-cutting feature and on smaller self-contained changes. Sharing large code changes will block up the `dev` branch if done there, requiring all the small changes to incorporate all the major changes too before they can be staged to `dev`, and preventing their release until the large feature is done.

The solution is to give the major topic its own branch for integration, independent of the generic `dev` branch. This will be a temporary, if long running, branch and will eventually be merged into `dev` and deleted when released. While development of the large feature is ongoing, use of this branch proceeds exactly like use of `dev`, except for the additional requirement to keep it up to date with changes on the actual `dev` branch. (Small features shouldn't have to stay in sync with large changes, but large changes should stay in sync with the small ones.). Such sync commits need to be merges and not rebases, as the major feature branch is shared and long-running. Individual commits from their local sub-topic branches still rebase onto the topic branch before publishing their changes for incorporation.

If there is a need to widely share a commit from the major topic branch, it should be tagged with a fifth .# version level, also > 9000 e,g, as #.#.#.#.9001, etc, where #.#.#.# is the development version it is currently based on. Due to the need to update the long-running major topic branch from dev, the #.#.#.# part will likely be different in each, so most shared versions will just look like #.#.#.#.9001. Again, this is not semantic versioning, but just a marker for an important release and a better name than the commit hash id.

One reason to widely share a major topic branch commit is if other major topics are underway simultaneously. This means that you will need to regularly pull from those projects too. But this must be done carefully to only pull from projects that are based on the same dev version as you.

If there is so much major development going on that you have too many feature branches under way at once to keep up with, you need to refactor the basis for the whole project to a modular design such that no large features cross-cut across too many separable code-sets.\

#### Long term support branches (none planned)

There is no plan to support old version. Once a new minor or major version is released, no further patch releases to the previous old versions should be expected. If for some reason this changes in the future, long term support can easily be implemented using additional permanent branches named for the major.minor version being supported (i.e. #.#). Each old version branch branches from `main` at the commit tagged as the latest original major.minor.patch release for the supported major.minor release. This long-term support branch then becomes the main branch for all future (patch only) releases of the supported old version. Each release is tagged just as on `main` with its #.#.# release, which will still be globally unique.

### Versioning and R

Version numbers are used internally by R in the `DESCRIPTION` file as well as in tags, making it critical that versioning is carefully handled. Tagged releases must have the same internal version id as the tag indicates. Not doing so is a critical error and should result in a withdrawn release if detected.

All development versions should use an internal version formatted like `#.#.#.#` where the `major.minor.patch` version is that of the version being modified and the fourth number is the `dev` branch version, starting with `9000`. This is managed by the maintainer. It is not so critical that internal R versions match tags, as for releases but it is a good idea. If you need to share a development version from your own topic branch, consider using a "major-topic" branch in your forked repo that you can be the maintainer for.

The reason to care about developer versions is so developers can always tell what version of a package they are using. Testing an R package usually involves installing it, but R won't cares what the version is; it will just replace the existing version with the one you ask - higher, the same, or lower, it doesn't care. Even if it is exactly the same package, it will just install it again. If you have added dependencies, it will install any that it needs that aren't already there. If you removed dependencies, it doesn't care if they are there or not. So this works fine ignoring versioning. Until you go to do some real work, and you forgot you still have the dev version of the package installed. You can't actually prevent this, but at least you can make sure you have a way to easily tell if you look. That's the purpose of the 9000+ element added to dev versions. If you do a SessionInfo(), the development-versioned package will stand out.

Since development happen in parallel, there is no way to keep all development versions in sequence across all branches unless there is central enforcement. This is too much trouble for day-to-day use on a topic branch, but for shared development versions on the `dev` branch this can be done by the maintainer.

To keep internal versions useful when developing requires only actions by the repo maintainer, although other developers need to be aware of brief periods when the repo will not be in a state to start a new feature branch.
   
   * When releasing from dev to master, the maintainer will make a pre-release commit that changes only the internal version on dev from #.#.#.9* to what its release version will be, probably #.#.#+1, but potentially to #.#+1.0 or even #+1.0.0. This should never be branched from.
   * This is then the commit the maintainer will release to main.
   * Immediately after merging, the maintainer will make a new commit on dev bumping the internal version from the release version to #.#.#.9000.
   * If for any reason a `dev` branch needs to be updated from main, a sync-merge will be needed. This should never be branched from.
   * After the sync merge the maintainer will immediately make a new commit bumping the version from #.#.# to #.#.#.9000.
   * All this applies the same to major-topic branches, except with an additional level of "#" and with the management commits being made by the major-topic branch maintainer, not the maintainer of the repo as a whole.

The developer wiil automatically get the correct development version when branching unless done where not allowed, and it will be adjusted correctly if when any `dev` branch changes need to be reconciled before publishing, if it was never changed by the developer.

