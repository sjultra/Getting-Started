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


# Guide on using Azure DevOps with VS Code

Here are a few guides on how to use Microsoft's Visual Studio Code (VSCODE) Integrated Development Environment (IDE) as a text editor to edit and update projects hosted on their Azure DevOps Git repositories. 

- https://www.patrickriedl.at/using-visual-studio-code-with-azure-devops/
- https://blog.topqore.com/how-to-configure-vs-code-for-azure-devops/
- https://www.azuredevopslabs.com/labs/azuredevops/pullrequests/

Once VS Code is installed on your system it's important to also download and configure Git. Check this is done by running this command from a VS Code terminal:
```
git config -l
```
Make sure these two fields are configured properly:

```
user.name=Firstname Lastname
user.email=youremailaddress@organizationname.tld
```

#### Create a free tier [Azure DevOps](https://azure.microsoft.com/en-us/pricing/details/devops/azure-devops-services/) account

https://azure.microsoft.com/en-us/pricing/details/devops/azure-devops-services/


### Task:

1. Each component can be a feature (Navbar, Timeline, Contacts, ActionItems, Metrics, Sharepoint, etc).

1. Each component will have a set of tasks or subtasks.

1. Ensure that there is an existing **task** first.

1. When creating tasks, add a prefix to differentiate them, like **BE** for Back End tasks, **FE** for Front End tasks and **SPIKE** for tasks that require a detailed documentation/research of a topic/tool before passing to the development phase. Example: **FE - 180 Add routing component**.

1. Check the **task** to see if it has an assignee:

   1. If there is one ask if you can help.

   1. Otherwise assign the task to you, create a branch as shown in the following image: image.png.

1. Start to develop.

**SCOPE: The Task branch can contain other Task branches or no other branch at all.**

**Naming convention:**

```
task/<number>-<Task_Description>
```

Input: N/A Output:Feature

Tests: N/A

**Policies:**
Required minimum number of reviewers:0 Check for linked work items (enabled) Merge Type: all available

**Ideally each guide will follow a similar outline:**

1. Include a Sequence Diagram with PlantUML - https://www.azuredevops.tips/generateplantumlinpipeline-yaml/

1. Architecture overview with diagrams.py - https://diagrams.mingrammer.com
