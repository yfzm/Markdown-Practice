# `Git` Workflow

## Table of contents

- Overview of `Git` workflow
- Advantages of `Git` workflow
- Introduction to some typical workflows
    1. Centralized Workflow
    2. Feature branching Workflow
    3. Gitflow Workflow
    4. Forking Workflow

-------

## Overview of `Git` workflow

Workflow is the definition, execution and automation of business processes where tasks, information or documents are passed from one participant to another for action, according to a set of procedural rules.

`Git` is a version control system for tracking changes in computer files and coordinating work on those files among multiple people. It is primarily used for source code management in software development.

Using `Git` as a powerful tool, we can deal with the workflow efficiently.

------

## Advantages of `Git` workflow

1. The simplicity of the underlying model makes git workflow Easy to Comprehend, which ensures maximum productivity in a smaller time-frame.

2. Git workflow provides a set of consistent commands that follows a systematic pattern making it easier implement. Similar pattern for all the three categories of branches - feature, release and hot-fix, ensure minimal faux-pas as far as development or dev-operations are concerned.

3. Git workflow makes itself easily adaptable at any stage of project development lifecycle, as it does not force any pre-requisites. It does not matter how early or how far off has one reached in his development lifecycle; git-flow can be included and adapted almost instantly.

4. Git workflow ensures a clean and decipherable state of branches at any given moment in the project’s life-cycle.

-------

## Introduction to some typical workflows

### 1. Centralized Workflow

#### How it works

the Centralized Workflow uses a central repository to serve as the single point-of-entry for all changes to the project. The default development branch is called `master` and all changes are committed into this branch. This workflow doesn’t require any other branches besides `master`.

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


### Summary

As you can see, it’s possible to replicate a traditional Subversion development environment using only a handful of Git commands. This is great for transitioning teams off of SVN, but it doesn’t leverage the distributed nature of Git.

The Centralized Workflow is great for small teams. The conflict resolution process detailed above can form a bottleneck as your team scales in size. If your team is comfortable with the Centralized Workflow but wants to streamline its collaboration efforts, it's definitely worth exploring the benefits of the Feature Branch Workflow. By dedicating an isolated branch to each feature, it’s possible to initiate in-depth discussions around new additions before integrating them into the official project.

## 2. Feature branching Workflow

### How it works
