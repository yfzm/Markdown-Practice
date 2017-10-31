# Git Workflow

## Table of contents

- Overview of Git workflow
- Advantages of Git workflow
- Introduction to some typical workflows
    1. Centralized Workflow
    2. Feature branching Workflow
    3. Gitflow Workflow
    4. Forking Workflow

-------

## Overview of Git workflow

Workflow is the definition, execution and automation of business processes where tasks, information or documents are passed from one participant to another for action, according to a set of procedural rules.

`Git` is a version control system for tracking changes in computer files and coordinating work on those files among multiple people. It is primarily used for source code management in software development.

Using `Git` as a powerful tool, we can deal with the workflow efficiently.

------

## Advantages of Git workflow

1. The simplicity of the underlying model makes git workflow Easy to Comprehend, which ensures maximum productivity in a smaller time-frame.

2. Git workflow provides a set of consistent commands that follows a systematic pattern making it easier implement. Similar pattern for all the three categories of branches - feature, release and hot-fix, ensure minimal faux-pas as far as development or dev-operations are concerned.

3. Git workflow makes itself easily adaptable at any stage of project development lifecycle, as it does not force any pre-requisites. It does not matter how early or how far off has one reached in his development lifecycle; git-flow can be included and adapted almost instantly.

4. Git workflow ensures a clean and decipherable state of branches at any given moment in the project’s life-cycle.

-------

## Introduction to some typical workflows

### 1. **Centralized Workflow**

#### 1.1 Overview

The Centralized Workflow uses a central repository to serve as the single point-of-entry for all changes to the project. The default development branch is called `master` and all changes are committed into this branch. This workflow doesn’t require any other branches besides `master`.

#### 1.2 How it works

1. **Initialize the central repository**

    First, someone needs to create the central repository on a server. If it’s a new project, you can initialize an empty repository. Otherwise, you’ll need to import an existing Git or SVN repository.   
    
    Central repositories should always be bare repositories (they shouldn’t have a working directory), which can be created as follows:   
    ```bash
    ssh user@host git init --bare /path/to/repo.git
    ```   
    Be sure to use a valid SSH username for user, the domain or IP address of your server for host, and the location where you'd like to store your repo for /path/to/repo.git. Note that the .git extension is conventionally appended to the repository name to indicate that it’s a bare repository.

2. **Hosted central repositories**

    Central repositories are often created through 3rd party Git hosting services like Bitbucket Cloud or Bitbucket Server. The process of initializing a bare repository discussed above is handled for you by the hosting service. The hosting service will then provide an address for the central repository to access from your local repository.

3. **Clone the central repository**
    
    Next, each developer creates a local copy of the entire project. This is accomplished via the git clone command:   
    ```bash
    git clone ssh://user@host/path/to/repo.git
    ```   
    When you clone a repository, Git automatically adds a shortcut called origin that points back to the “parent” repository, under the assumption that you'll want to interact with it further on down the road. 

4. **Make changes and commit**
    
    Once the repository is cloned locally, a developer can make changes using the standard Git commit process: edit, stage, and commit. If you’re not familiar with the staging area, it’s a way to prepare a commit without having to include every change in the working directory. This lets you create highly focused commits, even if you’ve made a lot of local changes.   
    ```bash
    git status # View the state of the repo
    git add <some-file> # Stage a file
    git commit # Commit a file</some-file>
    ```   
    Remember that since these commands create local commits, John can repeat this process as many times as he wants without worrying about what’s going on in the central repository. This can be very useful for large features that need to be broken down into simpler, more atomic chunks.

5. **Push new commits to central repository**

    Once the local repository has new changes committed. These change will need to be pushed to share with other developers on the project.

6. **git push origin master**
    
    This command will push the new committed changes to the central repository. When pushing changes to the central repository, it is possible that updates from another developer have been previously pushed that contain code which conflict with the intended push updates. Git will output a message indicating this conflict. In this situation, git pull will first need to be executed. This conflict scenario will be expanded on in the following section.

7. **Managing conflicts**

    The central repository represents the official project, so its commit history should be treated as sacred and immutable. If a developer’s local commits diverge from the central repository, Git will refuse to push their changes because this would overwrite official commits.   
   
    Before the developer can publish their feature, they need to fetch the updated central commits and rebase their changes on top of them. This is like saying, “I want to add my changes to what everyone else has already done.” The result is a perfectly linear history, just like in traditional SVN workflows.   
   
    If local changes directly conflict with upstream commits, Git will pause the rebasing process and give you a chance to manually resolve the conflicts. The nice thing about Git is that it uses the same git status and git add commands for both generating commits and resolving merge conflicts. This makes it easy for new developers to manage their own merges. Plus, if they get themselves into trouble, Git makes it very easy to abort the entire rebase and try again (or go find help).


#### 1.3 Summary

As you can see, it’s possible to replicate a traditional Subversion development environment using only a handful of Git commands. This is great for transitioning teams off of SVN, but it doesn’t leverage the distributed nature of Git.

The Centralized Workflow is great for small teams. The conflict resolution process detailed above can form a bottleneck as your team scales in size. If your team is comfortable with the Centralized Workflow but wants to streamline its collaboration efforts, it's definitely worth exploring the benefits of the Feature Branch Workflow. By dedicating an isolated branch to each feature, it’s possible to initiate in-depth discussions around new additions before integrating them into the official project.

### 2. **Feature branching Workflow**

#### 2.1 Overview

The Feature Branch Workflow assumes a central repository, and master represents the official project history. Instead of committing directly on their local master branch, developers create a new branch every time they start work on a new feature. Feature branches should have descriptive names, like animated-menu-items or issue-#1061. The idea is to give a clear, highly-focused purpose to each branch. Git makes no technical distinction between the master branch and feature branches, so developers can edit, stage, and commit changes to a feature branch.
 
In addition, feature branches can (and should) be pushed to the central repository. This makes it possible to share a feature with other developers without touching any official code. Since master is the only “special” branch, storing several feature branches on the central repository doesn’t pose any problems. Of course, this is also a convenient way to back up everybody’s local commits. The following is a walk-through of the life-cycle of a feature branch.

#### 2.2 How it works

1. **Start with the master branch**

    All feature branches are created off the latest code state of a project. This guide assumes this is maintained and updated in the master branch.

    ```bash
    git checkout master
    git fetch origin
    git reset --hard origin/master
    ```   
    This switches the repo to the master branch, pulls the latest commits and resets the repo's local copy of master to match the latest version.

2. **Create a new-branch**

    Use a separate branch for each feature or issue you work on. After creating a branch, check it out locally so that any changes you make will be on that branch.
    ```bash
    git checkout -b new-feature
    ```   
    This checks out a branch called new-feature based on master, and the -b flag tells Git to create the branch if it doesn’t already exist.

3. **Update, add, commit, and push changes**

    On this branch, edit, stage, and commit changes in the usual fashion, building up the feature with as many commits as necessary. Work on the feature and make commits like you would any time you use Git. When ready, push your commits, updating the feature branch on Bitbucket.
    ```bash
    git status
    git add <some-file>
    git commit
    ```

4. **Push feature branch to remote**

    It’s a good idea to push the feature branch up to the central repository. This serves as a convenient backup, when collaborating with other developers, this would give them access to view commits to the new branch.
    ```bash
    git push -u origin new-feature
    ```   
    This command pushes new-feature to the central repository (origin), and the -u flag adds it as a remote tracking branch. After setting up the tracking branch, git push can be invoked without any parameters to automatically push the new-feature branch to the central repository. To get feedback on the new feature branch, create a pull request in a repository management solution like Bitbucket Cloud or Bitbucket Server. From there, you can add reviewers and make sure everything is good to go before merging.

5. **Resolve feedback**

    Now teammates comment and approve the pushed commits. Resolve their comments locally, commit, and push the suggested changes to Bitbucket. Your updates appear in the pull request.

6. **Merge your pull request**

    Before you merge, you may have to resolve merge conflicts if others have made changes to the repo. When your pull request is approved and conflict-free, you can add your code to the master branch. Merge from the pull request in Bitbucket.

#### 2.3 Pull requests

Aside from isolating feature development, branches make it possible to discuss changes via pull requests. Once someone completes a feature, they don’t immediately merge it into master. Instead, they push the feature branch to the central server and file a pull request asking to merge their additions into master. This gives other developers an opportunity to review the changes before they become a part of the main codebase.

Code review is a major benefit of pull requests, but they’re actually designed to be a generic way to talk about code. You can think of pull requests as a discussion dedicated to a particular branch. This means that they can also be used much earlier in the development process. For example, if a developer needs help with a particular feature, all they have to do is file a pull request. Interested parties will be notified automatically, and they’ll be able to see the question right next to the relevant commits.

Once a pull request is accepted, the actual act of publishing a feature is much the same as in the Centralized Workflow. First, you need to make sure your local master is synchronized with the upstream master. Then, you merge the feature branch into master and push the updated master back to the central repository.

Pull requests can be facilitated by product repository management solutions like Bitbucket Cloud or Bitbucket Server.

#### 2.4 Summary

This workflow helps organize and track branches that are focused on business domain feature sets. Other Git workflows like the Git Forking Workflow and the Gitflow Workflow are repo focused and can leverage the Git Feature Branch Workflow to manage their branching models. This document demonstrated a high-level code example and fictional example for implementing the Git Feature Branch Workflow. Some key associations to make with the Feature Branch Workflow are:

- focused on branching patterns
- can be leveraged by other repo oriented workflows
- promotes collaboration with team members through pull requests and merge reviews

Utilizing `git rebase` during the review and merge stages of a feature branch will create enforce a cohesive Git history of feature merges. A feature branching model is a great tool to promote collaboration within a team environment.

### 3. **Gitflow Workflow**

#### 3.1 Overview

Gitflow is really just an abstract idea of a Git workflow. This means it dictates what kind of branches to set up and how to merge them together. We will touch on the purposes of the branches below. The git-flow toolset is an actual command line tool that has an installation process. The installation process for git-flow is straightforward. Packages for git-flow are available on multiple operating systems. On OSX systems, you can execute brew install git-flow. On windows you will need to download and install git-flow. After installing git-flow you can use it in your project by executing git flow init. Git-flow is a wrapper around Git. The git flow init command is an extension of the default git init command and doesn't change anything in your repository other than creating branches for you.

#### 3.2 How it works

##### 3.2.1 Develop and Master Branches

Instead of a single master branch, this workflow uses two branches to record the history of the project. The master branch stores the official release history, and the develop branch serves as an integration branch for features. It's also convenient to tag all commits in the master branch with a version number.   

The first step is to complement the default master with a develop branch. A simple way to do this is for one developer to create an empty develop branch locally and push it to the server:

```
git branch develop
git push -u origin develop
```   
This branch will contain the complete history of the project, whereas master will contain an abridged version. Other developers should now clone the central repository and create a tracking branch for develop.

When using the git-flow extension library, executing git flow init on an existing repo will create the develop branch:

```
$ git flow init

Initialized empty Git repository in ~/project/.git/
No branches exist yet. Base branches must be created now.
Branch name for production releases: [master]
Branch name for "next release" development: [develop]

How to name your supporting branch prefixes?
Feature branches? [feature/]
Release branches? [release/]
Hotfix branches? [hotfix/]
Support branches? [support/]
Version tag prefix? []

$ git branch
* develop
master
```

##### 3.2.2 Feature Branches

Each new feature should reside in its own branch, which can be pushed to the central repository for backup/collaboration. But, instead of branching off of master, feature branches use develop as their parent branch. When a feature is complete, it gets merged back into develop. Features should never interact directly with master.

Note that feature branches combined with the develop branch is, for all intents and purposes, the Feature Branch Workflow. But, the Gitflow Workflow doesn’t stop there.

Feature branches are generally created off to the latest develop branch.

1. **Creating a feature branch**

    Without the git-flow extensions:
    ```
    git checkout develop
    git checkout -b feature_branch
    ```
    When using the git-flow extension:
    ```
    git flow feature start feature_branch
    ```
    Continue your work and use Git like you normally would.

2. **Finishing a feature branch**

    When you’re done with the development work on the feature, the next step is to merge the feature_branch into develop.

    Without the git-flow extensions:
    ```
    git checkout develop
    git merge feature_branch
    ```

    Using the git-flow extensions:
    ```
    git flow feature finish feature_branch
    ```

##### 3.2.3 Release Branches

Once develop has acquired enough features for a release (or a predetermined release date is approaching), you fork a release branch off of develop. Creating this branch starts the next release cycle, so no new features can be added after this point—only bug fixes, documentation generation, and other release-oriented tasks should go in this branch. Once it's ready to ship, the release branch gets merged into master and tagged with a version number. In addition, it should be merged back into develop, which may have progressed since the release was initiated.

Using a dedicated branch to prepare releases makes it possible for one team to polish the current release while another team continues working on features for the next release. It also creates well-defined phases of development (e.g., it's easy to say, “This week we're preparing for version 4.0,” and to actually see it in the structure of the repository).

Making release branches is another straightforward branching operation. Like feature branches, release branches are based on the develop branch. A new release branch can be created using the following methods.

Without the git-flow extensions:
```
git checkout develop
git checkout -b release/0.1.0
```
When using the git-flow extensions:
```
$ git flow release start 0.1.0
Switched to a new branch 'release/0.1.0'
```
Once the release is ready to ship, it will get merged it into master and develop, then the release branch will be deleted. It’s important to merge back into develop because critical updates may have been added to the release branch and they need to be accessible to new features. If your organization stresses code review, this would be an ideal place for a pull request.

To finish a release branch, use the following methods:

Without the git-flow extensions:
```
git checkout develop
git merge release/0.1.0
```
Or with the git-flow extension:
```
git checkout master
git checkout merge release/0.1.0
git flow release finish '0.1.0'
```

##### 3.2.4 **Hotfix Branches**

Maintenance or “hotfix” branches are used to quickly patch production releases. Hotfix branches are a lot like release branches and feature branches except they're based on master instead of develop. This is the only branch that should fork directly off of master. As soon as the fix is complete, it should be merged into both master and develop (or the current release branch), and master should be tagged with an updated version number.

Having a dedicated line of development for bug fixes lets your team address issues without interrupting the rest of the workflow or waiting for the next release cycle. You can think of maintenance branches as ad hoc release branches that work directly with master. A hotfix branch can be created using the following methods:

Without the git-flow extensions:
```
git checkout master
git checkout -b hotfix_branch
```
When using the git-flow extensions: 
```
$ git flow hotfix start hotfix_branch
```
Similar to finishing a release branch, a hotfix branch gets merged into both master and develop.
```
git checkout master
git merge hotfix_branch
git checkout develop
git merge hotfix_branch
git branch -D hotfix_branch
$ git flow hotfix finish hotfix_branch
```

#### 3.3 Summary

Some key takeaways to know about Gitflow are:

- The workflow is great for a release-based software workflow.
- Gitflow offers a dedicated channel for hotfixes to production.
 
The overall flow of Gitflow is:

1. A develop branch is created from master
2. A release branch is created from develop
3. Feature branches are created from develop
4. When a feature is complete it is merged into the release branch
5. When the release branch is done it is merged into develop and master
6. If an issue in master is detected a hotfix branch is created from master
7. Once the hotfix is complete it is merged to both develop and master

### 4. **Forking Workflow**

#### 4.1 Overview

As in the other Git workflows, the Forking Workflow begins with an official public repository stored on a server. But when a new developer wants to start working on the project, they do not directly clone the official repository.

Instead, they fork the official repository to create a copy of it on the server. This new copy serves as their personal public repository—no other developers are allowed to push to it, but they can pull changes from it (we’ll see why this is important in a moment). After they have created their server-side copy, the developer performs a git clone to get a copy of it onto their local machine. This serves as their private development environment, just like in the other workflows.

When they're ready to publish a local commit, they push the commit to their own public repository—not the official one. Then, they file a pull request with the main repository, which lets the project maintainer know that an update is ready to be integrated. The pull request also serves as a convenient discussion thread if there are issues with the contributed code. The following is a step-by-step example of this workflow.


1. A developer 'forks' an 'official' server-side repository. This creates their own server-side copy.
2. The new server-side copy is cloned to their local system.
3. A Git remote path for the 'official' repository is added to the local clone.
4. A new local feature branch is created.
5. The developer makes changes on the new branch.
6. New commits are created for the changes.
7. The branch gets pushed to the developer's own server-side copy.
8. The developer opens a pull request from the new branch to the 'official' repository.
9. The pull request gets approved for merge and is merged into the original server-side repository
 
To integrate the feature into the official codebase, the maintainer pulls the contributor’s changes into their local repository, checks to make sure it doesn’t break the project, merges it into his local master branch, then pushes the master branch to the official repository on the server. The contribution is now part of the project, and other developers should pull from the official repository to synchronize their local repositories.

It’s important to understand that the notion of an “official” repository in the Forking Workflow is merely a convention. In fact, the only thing that makes the official repository so official is that it’s the public repository of the project maintainer.

#### 4.2 How it works

1. **Clone your fork**

    Next each developer needs to clone their own public forked repository. They can do this with the familiar git clone command.

    Assuming the use of Bitbucket to host these repositories, developers on a project should have their own Bitbucket account and they should clone their forked copy of the repository with:
    ```bash
    git clone https://user@bitbucket.org/user/repo.git
    ```
2. **Adding a remote**

    Whereas other Git workflows use a single origin remote that points to the central repository, the Forking Workflow requires two remotes—one for the official repository, and one for the developer’s personal server-side repository. While you can call these remotes anything you want, a common convention is to use origin as the remote for your forked repository (this will be created automatically when you run git clone) and upstream for the official repository.
    ```
    git remote add upstream https://bitbucket.org/maintainer/repo
    ```
    You’ll need to create the upstream remote yourself using the above command. This will let you easily keep your local repository up-to-date as the official project progresses. Note that if your upstream repository has authentication enabled (i.e., it's not open source), you'll need to supply a username, like so:
    ```
    git remote add upstream https://user@bitbucket.org/maintainer/repo.git
    ```
    This requires users to supply a valid password before cloning or pulling from the official codebase.

3. **Working in a branch: making & pushing changes**

    In the developer's local copy of the forked repository they can edit code, commit changes, and create branches just like in other Git workflows:
    ```bash
    git checkout -b some-feature
    # Edit some code
    git commit -a -m "Add first draft of some feature"
    ```
    All of their changes will be entirely private until they push it to their public repository. And, if the official project has moved forward, they can access new commits with git pull:
    ```bash
    git pull upstream master
    ```
    Since developers should be working in a dedicated feature branch, this should generally result in a fast-forward merge.

4. **Making a Pull Request**

    Once a developer is ready to share their new feature, they need to do two things. First, they have to make their contribution accessible to other developers by pushing it to their public repository. Their origin remote should already be set up, so all they should have to do is the following:
    ```bash
    git push origin feature-branch
    ```
    This diverges from the other workflows in that the origin remote points to the developer’s personal server-side repository, not the main codebase.

    Second, they need to notify the project maintainer that they want to merge their feature into the official codebase. Bitbucket provides a “pull request” button that leads to a form asking you to specify which branch you want to merge into the official repository. Typically, you’ll want to integrate your feature branch into the upstream remote’s master branch.

#### 4.3 Summary

To recap, the Forking Workflow is commonly used in public open-source projects. Forking is a git clone operation executed on a server copy of a projects repo. A Forking Workflow is often used in conjunction with a Git hosting service like Bitbucket. A high-level example of a Forking Workflow is:

1. You want to contribute to an open source library hosted at bitbucket.org/userA/open-project
2. Using Bitbucket you create a fork of the repo to bitbucket.org/YourName/open-project
3. On your local system you execute git clone on https://bitbucket.org/YourName/open-project to get a local copy of the repo
4. You create a new feature branch in your local repo
5. Work is done to complete the new feature and git commit is executed to save the changes
6. You then push the new feature branch to your remote forked repo
7. Using Bitbucket you open up a pull request for the new branch against the original repo at bitbucket.org/userA/open-project

The Forking Workflow helps a maintainer of a project open up the repository to contributions from any developer without having to manually manage authorization settings for each individual contributor. This gives the maintainer more of a "pull" style workflow. Most commonly used in open-source projects, the Forking Workflow can also be applied to private business workflows to give more authoritative control over what is merged into a release. This can be useful in teams that have Deploy Managers or strict release cycles.