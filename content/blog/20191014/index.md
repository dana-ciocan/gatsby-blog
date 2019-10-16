---
title: Git Flow for Beginners
date: "2019-10-14T09:30:32.169Z"
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

Master is perhaps the confusing one as all Git repositories start with a 'master' branch but it isn't special or immutable in any kind of way. In this model however, the 'master' branch becomes like a record that our releases are immortalised onto.

Every commit on the master branch represents a release that is ready for production, allowing us to trace the release history of our project from beginning to end.

### The 'develop' Branch

Develop branches are fairly commonly used in projects that use Git to develop code. However, in the Git Flow model, we are saying that this branch is only to be used for temporarily holding content that should be incorporated into our next release. This is like a 'working area' where we are getting code ready for deployment.

Generally, if you create a pull request to submit your work, it'll be merged into the 'develop' branch automatically once it has been approved, ready to be added to the next release once all the work is ready to be deployed.

## Temporary Branches

Aside from 'master' and 'develop' there are a few support branches that are purely used to merge work into one of the main branches.

These are technically not one continuous branch but can be treated as such. That is to say, there isn't a branch called 'feature' and a branch called 'hotfix', any work that will become a feature is just created on a branch pre-pended with 'feature/' and similarly 'hotfix/' for anything that should be merged into master as an urgent fix.

These branches are often deleted once the work has been merged, meaning they don't stick around like 'master' and 'develop' do. This is because the work is available on one of the main branches and no longer needs to be kept on the individual support branches (this is just duplication after all).

### Feature Branches

These are used to create individual 'features' and will generally be started when a developer starts a new isolated item of work that implements one particular new functionality for the project. Several commits can be added to represent the individual changes made to implement this feature.

Feature branches allow many different developers to work on one codebase simultaneously without interfering with each other. Think of a world without version control: if you wanted to change the same file as your colleague, you'd have to tell them you were going to edit this file, make the changes and then let your colleague know that they can now safely make changes because you're done. Using the feature branch approach avoids this entirely.

Feature branches are created from the 'develop' branch and merged back into the 'develop' branch once ready for production. The naming convention is to start the branch name with 'feature/' but this isn't strictly necessary. However, it helps other developers see what the branch is for so is highly recommended.

#### Creating a Feature Branch

To create a feature branch, just do the following:

    git checkout develop
    git checkout -b feature/<name-of-your-feature>

#### Finishing a Feature Branch

If you're not using a pull request model (in which case this will be done for you), you'll need to merge your feature back into the 'develop' branch manually, as follows:

    git checkout develop
    git merge --no-ff feature/<name-of-your-feature>
    git branch -D feature/<name-of-your-feature>
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

If there are any merge conflicts at this point (which there may well be), you'll have to fix them ane re-commit.

Next, the release branch can be removed as it is no longer necessary:

    git branch -D release-<release number>

### Hotfix Branches