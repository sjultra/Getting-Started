   <!-- Copyright 2020 SJULTRA, inc.

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License. -->

[go back](../Getting-Started)

# Git best practices with pull requests

An article about git best practices in agile projects to prevent the most common conflicts when many developers working in different time zones are collaborating together in a project.

Git pull requests allows developers to avoid code conflicts while improving consistency with peer review of any code commits.

A pull request holds the merge in a state that allows other developers to see what is being changed. If a developer agrees with the changes, she can proceed with accepting the pull request and executing the corresponding merge and then delete the supporting branch if needed.

When using pull requests developers making the changes are more conscious of what is being changed because the user interface highlights the code changes and files that are being affected with each commit.

Another great feature of pull requests is that it allows developers to have conversations about the changes, and even add annotations in specific lines of code. Those comments make the merging process more transparent and visible to the entire team, and it also helps with documentation.

Adapting the standard git workflow to pull requests is easy. This is the revised workflow:

# Git main branches

The main branches are the same. The origin server (or central repository like Github or Bitbucket) holds two main branches with an infinite lifetime:

## Main

The **Main** branch always reflects a production ready state, and it’s the branch that is deployed to the production server. Releases are tagged from **Main**.

## Dev

The **Dev** branch is used for development and integration of changes. It always reflects a state with the latest delivered development changes for the next release, and it’s deployed to the development environment to allow other Agile team members to have access to those new features and test them. <-- pushed to https://cpsdb.dev.vzxy.net

## Feature

The **Task** branch is a collection containing at least one **Task**. It reflects a state with the planned feature to be delivered for the **Dev** branch, and it’s deployed to the development environment to allow other Agile team members to have access to those new features and test them. <-- pushed to **\*WILL BE UPDATED
The **feature** is a branch used to deliver bundles of **tasks** into the **dev** branch. A task is only considered finished after it is merged into **dev\*\*.

## Task

The **Task** branch is the only way a commit should be added to a repository - the smallest change should be a task.

IMPORTANT: **Main**, **Dev**, **Feature** should never be modified directly.

# Git supporting branches

The supporting branches are the same as well. These are created temporarily in the system to make changes and collaborate without affecting the master and develop branches directly.

## Feature branches

These branches are created from the develop branch, and are utilized to develop new features.

## Release branches

These are created from the **Dev** branch when the team agrees that the code is stable and is ready to be released to production. This allows the team to isolate that release from the **Dev** branch, so others keep developing new features and integrating those changes in the **Dev** branch while the release is tested and approved. Release candidate branches are deployed to the staging server. Any bugs found during testing are fixed in that branch.

## Hotfix branches

These are created from the **Main** branch, and are used when changes on develop are not release-ready and a fix to production is required that cannot wait until the upcoming release is stable.

# Git branching and merging

The revisions of the workflow are noted in the merging process. These are the conventions to create and merge branches in the system:

## Feature branches

Branch off from **Dev** branch.
Create a pull request to merge back into **Dev** branch.
If everything looks okay, merge and delete the supporting branch, else create comments in the pull request until changes look fine, or close the pull request and cancel the merge.

##Release branches
Branch off from **Dev** branch.
Create a pull request to merge back into **Dev** AND another one to merge to **Main**.

If everything looks okay, merge and delete the supporting branch, else create comments in the pull requests until changes look fine, or close the pull requests and cancel the merge.

##Hotfix branches
Branch off from **Main**.
Create a pull request to merge back into **Dev** AND another one to merge to **Main**.
If everything looks okay, merge and delete the supporting branch, else create comments in the pull requests until changes look fine, or close the pull requests and cancel the merge.
Once you have merged your changes in any of those cases, ensure you pull those from origin to get them in your local environment.

## Repository Branches

- Main
- Hotfix
- Dev
- Feature
- Task

# Branches Policies:

## Main:

### SCOPE: The Main branch will contain only Dev pull requests which we will refer to as releases.

### Naming convention:

```
Main
```

Input: Dev
Output: Production (Manual promotion process)

**Rigurous QA and Code reviews are **MANDATORY** !**
Tests: Functionality tests & Manual testing after changes get staged

### Policies:

Required minimum number of reviewers:2
Allow requestors to approve their own changes (disabled)
Reset code reviewer votes when there are new changes (enabled)
Merge Type: Squash merge
Pipeline: Main

## Dev:

### SCOPE: The Dev branch can contain many Feature branches. But no other type.

### Naming convention:

```
Dev
```

Input: Feature
Output: Main (Manual promotion process)

**Rigorous QA and Code reviews are **MANDATORY** !**
Tests: Functionality tests & Manual testing after changes get staged

### Policies:

Required minimum number of reviewers:2
Allow requestors to approve their own changes (disabled)
Reset code reviewer votes when there are new changes (enabled)
Merge Type: rebase and fast-forward merge
Pipeline: Dev

## Feature:

1. Ensure that there is an existing **feature** first.
1. Check the **feature** to see if it has an assignee : 1. If there is one ask if you can help. 1. Otherwise assign the **feature** to you, create a branch as shown in the following image:
   ![image.png](/\_images/git-1.png =400x)

### SCOPE: The Feature branch can contain many Task branches. But no other type.

### Naming convention:

```
feat/<number>-<Task_Description>
```

Input:Task
Output:Dev

Tests: Functionality tests (ensure existing code still maintains expected functionality)

### Policies:

Required minimum number of reviewers:1
Check for linked work items (enabled)
Merge Type: Squash merge
Pipeline: Demo-feature

## Task:

1. Each component can be a feature
1. Each component will have a set of tasks or subtasks
1. Ensure that there is an existing **task** first.
1. When creating tasks, add a prefix to differentiate them, like **BE** for Back End tasks, **FE** for Front End tasks and **SPIKE** for tasks that require a detailed documentation/research of a topic/tool before passing to the development phase. Example: **FE - 180 Add routing component**
1. Check the **task** to see if it has an assignee:
   1. If there is one ask if you can help.
   1. Otherwise assign the task to you, create a branch as shown in the following image:
      ![image.png](/\_images/git-2.png =400x)
1. Start to develop.

### SCOPE: The Task branch can contain other Task branches or no other branch at all.

### Naming convention:

```
task/<number>-<Task_Description>
```

Input: N/A
Output:Feature

Tests: N/A

### Policies:

Required minimum number of reviewers:0
Check for linked work items (enabled)
Merge Type: all available

# Task Example:

#### Create a free tier GitHub account

https://github.com/join?plan=free&source=pricing-card-free

#### Check out https://github.com/sjultra/inspec-gcp-cis-benchmark

#### Make a commit to a test log file. Record your name and date.

#### Git commands for updating the local repo

    1. Open Terminal.

    2. Change the current working directory to your local project.

    3. Fetch the branches and their respective commits from the upstream repository. Commits to master will be stored in a local branch, upstream/master.

      $ git fetch upstream
       > remote: Counting objects: 75, done.
      > remote: Compressing objects: 100% (53/53), done.
       > remote: Total 62 (delta 27), reused 44 (delta 9)
      > Unpacking objects: 100% (62/62), done.
      > From https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY
      >  * [new branch]      master     -> upstream/master

    4. Check out your fork's local master branch.

      $ git checkout master
      > Switched to branch 'master'

    5. Merge the changes from upstream/master into your local master branch. This brings your fork's master branch into sync with the upstream repository, without losing your local changes.

      $ git merge upstream/master
      > Updating a422352..5fdff0f
      > Fast-forward
      >  README                    |    9 -------
      >  README.md                 |    7 ++++++
      >  2 files changed, 7 insertions(+), 9 deletions(-)
      >  delete mode 100644 README
      >  create mode 100644 README.md

    If your local branch didn't have any unique commits, Git will instead perform a "fast-forward":

      $ git merge upstream/master
      > Updating 34e91da..16c56ad
      > Fast-forward
      >  README.md                 |    5 +++--
      >  1 file changed, 3 insertions(+), 2 deletions(-)

# Changing git user account on Windows

1. Go to Control Panel -> Credential Manager -> Windows Credentials

1. Click on "Add a generic credential"

1. Fill the following and click OK when done.

Internet or network address: git:https://dev.azure.com/sjultra

Username: sample@vzxy.net

Password: \* \* \* \* \* \*


# Add git user to global settings

1. Open git Bash
2. run the following commands
    * git config --global user.email "{yourusername}@{domain}"
    * git config --global user.name 
      * this will verify that the username was set properly

Now you should be able to connect VC Code to Azure DevOps Repositories

