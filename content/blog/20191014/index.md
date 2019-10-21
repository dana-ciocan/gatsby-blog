---
title: Git Flow for Beginners
date: "2019-10-21T13:30:32.169Z"
description: Demystifying Git Flow
---

Getting to grips with the nitty gritty of Git Flow can be a bit daunting if you've never used it before. This article aims to demystify one of the more common branching models for Git.

The [definitive article on Git Flow](https://nvie.com/posts/a-successful-git-branching-model/) was written by Vincent Driessen but it is very much aimed at confident Git users so don't feel too bad if you didn't understand it all. This post will take you through the finer details and you'll be a Git Flow Pro in no time!

## Key Git Concepts

In order to understand Git Flow, you really need to understand the following key Git concepts:

* **Branching and Merging** - read the [Git Pro chapter on Basic Branching and Merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
* **Working with Remotes** - you will need to push to a remote at points
* **Tagging** - least important, but you should know how to tag commits

I may look at writing some more detailed posts on these concepts myself in future so watch this space!

## Permanent Branches

In Git Flow, the idea is that there are two main 'permanent' branches that all other branches are either created from or merged into. These branches are called 'develop' and 'master'.

### The 'master' Branch

Master is perhaps a slightly confusing name, as all Git repositories start with a 'master' branch but it isn't special or immutable in any kind of way. In this model however, the master branch becomes like a record of all the releases of a project. Every commit on the master branch represents a release that is ready for production, allowing anyone to trace the release history of the project from beginning to end.

### The 'develop' Branch

In Git Flow, the 'develop' branch is used to temporarily hold content that should be incorporated into the next release. This is like a 'working area' where code is being prepared for deployment. Generally, if developers create pull requests (using a service like Github or Bitbucket) to submit work, it can be merged into the 'develop' branch automatically once approved, ready to be added to the next release once all the work is deemed ready to be deployed.

## Temporary Branches

Aside from 'master' and 'develop' there are a few supporting branch types that are purely used to merge work into either of these main branches.

Although these are technically not one continuous branch, they can be treated as such. That is to say, there isn't a branch called 'feature' and a branch called 'hotfix', any work that will become a feature on develop is just created on a branch pre-pended with 'feature/' and similarly 'hotfix/' for anything that should be merged into master as an urgent fix.

These branches are usually deleted once the work has been merged, meaning they don't stick around like 'master' and 'develop' do. This is because once the work has been merged, it is always available on the develop or master branch and no longer needs to be kept on the individual support branches.

### Feature Branches

These are used to create individual 'features' and will generally be created when a developer starts a new isolated item of work that implements one particular new functionality for the project. Several commits can be added to represent the individual changes made to implement this feature.

Feature branches allow many different developers to work on one codebase simultaneously without interfering with each other. Think of a world without version control: if you wanted to change the same file as your colleague, you'd have to tell them you were going to edit this file, make the changes and then let your colleague know that they can now safely make changes because you're done. Using the feature branch approach avoids this entirely.

Feature branches are created from the 'develop' branch and merged back into the 'develop' branch once ready for production. The naming convention is to start the branch name with 'feature/' but this isn't strictly necessary. However, it helps other developers see what the branch is for so is highly recommended.

#### Creating a Feature Branch

To create a feature branch, just do the following:

    git checkout develop
    git checkout -b feature/<name-of-your-feature>

#### Finishing a Feature Branch

If you're not using a pull request model (if you are, this will be done for you by whichever source control platform you are using), you'll need to merge your feature back into the 'develop' branch manually, as follows:

    git checkout develop
    git merge --no-ff feature/<name-of-your-feature>
    git branch -d feature/<name-of-your-feature>
    git push origin develop

Using the 'no-ff' option on the merge means the entire commit history is kept for all the features. This means feature branches can be deleted after they've been merged, because all the commits are now on the 'develop' branch and are therefore no longer required to be kept separately.

### Release Branches

Release branches are used to get work ready for a new production release. Creating a release branch is effectively saying that the codebase is nearly in a fit state for deployment and that all the features currently on develop should be deployed soon.

New features should not be started on the release branch at this point (these should be started from 'develop' instead as usual). Last minute bug fixes can be completed on the release branch however, as long as this is contributing to the scheduled release.

This means that work for the next release can be started on 'develop' at the same time as a scheduled release is being prepared on the release branch. When working in big teams, this can be particularly advantageous, as people don't have to wait for the release to be deployed before continuing work on new features.

#### Creating a Release Branch

Release branches are created from 'develop' when the work on that branch is in a state that's ready to be deployed.

    git checkout develop
    git checkout -b release-<release number>
    *** Make changes to reflect new version number (if necessary)***
    git add .
    git commit -m "Version <release number>"

In some projects, a file will need to be updated to reflect the new version number, but this is not always the case. It depends on the workflow of individual projects.

The new release branch may stay around for a while, during which time the work is checked and any bugs found are fixed.

#### Finishing a Release Branch

When the release branch is finally ready to be deployed, you'll need to do some housekeeping. First of all, the release branch is merged into 'master' so we have a record of this release on that branch:

    git checkout master
    git merge --no-ff release-<release number>
    git tag -a <release number>

The commit is tagged so that it can be referenced easily in future.

Remember that we may have done some bug fixes etc. on the release branch after it was created. So we also need to merge the release branch back into 'develop' to make sure this is up-to-date:

    git checkout develop
    git merge --no-ff release-<release number>

If there are any merge conflicts at this point (which there may well be), you'll have to fix them and re-commit.

Next, the release branch can be removed as it is no longer necessary:

    git branch -d release-<release number>

### Hotfix Branches

Hotfix branches are generally used to fix urgent bugs that have been found in the live version of a project, but can also be used if an urgent new feature is requested by the client that needs to be made live before the next official release of the project. Using a hotfox branch for this purpose means work can continue on the main release while the live code is being patched.

#### Creating a Hotfix Branch

Hotfix branches are always branched off from the 'master' branch.

    git checkout master
    git checkout -b hotfix-<hotfix number>
    *** Make changes to reflect new version number (if necessary)***
    git add .
    git commit -m "Version <release number>"

Now the bug can be fixed and the relevant commits made with confidence that the environment in which this fix is being applied is sufficiently isolated that it will not affect any other work being done on the project.

#### Finishing a Hotfix Branch

Once the work is finished, the hotfix branch needs to be merged back into master:

    git checkout master
    git merge --no-ff hotfix-<hotfix number>
    git tag -a "<hotfix number>"

Next it needs to be merged into develop, as it does not contain the changes just yet. If this isn't done, the next release branch will not contain the hotfix code, as the next release branch will be branched off from develop, not master and would this would lead to confusing inconsistencies.

    git checkout developer
    git merge --no-ff hotfix-<hotfix number>

The only thing to be aware of is that if a release branch has already been created, the hotfix branch should be merged into it instead of develop. This is because once the release has been finished, the work will merge into develop anyway, so there is no need to do two merges.

Once the hotfix branch has been merged, it is usually deleted:

    git branch -d hotfix-<hotfix number>

I hope this guide to Git Flow has been helpful - let me know what you think!
