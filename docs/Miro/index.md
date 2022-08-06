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

# Build a demo hello world app in Node.js for Miro

<https://miro.com/online-strategic-planning-tool/>

1. Include a Sequence Diagram with PlantUML - <https://www.azuredevops.tips/generateplantumlinpipeline-yaml/>
1. Architecture overview with diagrams.py - <https://diagrams.mingrammer.com>
1. Leverage Azure DevOps Pipeline - <https://docs.microsoft.com/en-us/learn/modules/deploy-nodejs/>
1. Utilize a Test Plan - <https://acloudguru.com/hands-on-labs/build-and-test-a-node-js-app-in-azure-devops>
1. Create a hello world demo app <https://developers.miro.com/docs/getting-started>

## Step 1: Sign up for a Free Miro Account

Navigate to www.miro.com and sign up for a free account. Use any email address you like, and once you are logged in, Miro will prompt you to create your initial team. Miro will step you through the registration process, it's very streamlined and simple. Once you finish through the onboarding process, you will be in board creation mode. For what we are going to do this is sufficient. However, if you want to play around with the tech, and build some flowcharts, or mind maps, go ahead! This how to isn't going anywhere.

## Step 2: Setup a Developer Team in Miro

Once you are ready to resume this tutorial, click on the icon of your initials in the upper right hand side (e.g., JS for John Smith) and chose settings. From there, navigate to the last tab called "Your apps". There should not be any apps, but instead, there will be a link to Miro documentation on how to build them. From here, you will find a link to "Create a Developer team in Miro", which is <https://miro.com/app/dashboard/?createDevTeam=1>. Click this link to build a developer team, you will be prompted to create an app, so click the "Build App" button.

## Step 3: Create a Miro App (dev app)

You will be prompted to agree to certain terms and conditions (read those!) in order to move forward and create our app. Once you have accepted the terms, check the box, and chose "+ Create new app".

Give your app a meaningful name, and consider the "Expire user authentication token" checkbox, it is beyond the scope of this tutorial, but something to consider if you will be doing some real application creation using their REST API.

Chose your developer team (by default called Dev team), and click "Create app".

## Step 4: Configure your App in Miro

The interface should drop you into the properties of your new app, starting with "App Credentials". If not, don't fret, you can click the name of your app in the list, and it will take you to the properties of your app.

First order of business is to set the permissions for our app, to do this you will scroll down until you get to the permissions section. From here we need to check the first two boxes:

1. boards:read
2. boards:write

These permissions give the API read and write access to your app, which we will need to get your boards (read), and to create a new board (write).

## Step 5: Install your App

Once your permissions are set, click the button to "Install app and get OAuth token", we will need this token to call the API. Chose your dev team when it prompts you for what team are we deploying this app to. Click on "Install & authorize". Note down in a safe place the OAuth token, we will need this in our javascript app. If you lose it, fear not, you can follow the same instructions and Miro will generate a new one for you.

## Step 6: Create a Board in Miro

Once we have our token, it's now time to create a board (or two). The simplist way is to click on the "+" character to create a new board, and use one of the many templates Miro offers. Create a few so we have a list of more than one, then we can move on.

### Creating a board (by token)

Below are two JavaScript programs that will create a new board (no template, just a blank board), as well as get your list of boards associated to your token (and by extension your team). For both, we need to change:

From:

```javascript
Authorization: 'Bearer eyJtaXJvLm9...'
```

To:

```javascript
Authorization: 'Bearer your_token'
```

#### Creating a board

```javascript
const options = {
  method: 'POST',
  headers: {
    Accept: 'application/json',
    'Content-Type': 'application/json',
    Authorization: 'Bearer eyJtaXJvLm9...'
  },
  body: JSON.stringify({
    name: 'My Hello World',
    policy: {
      permissionsPolicy: {
        collaborationToolsStartAccess: 'all_editors',
        copyAccess: 'anyone',
        sharingAccess: 'team_members_with_editing_rights'
      },
      sharingPolicy: {
        access: 'private',
        inviteToAccountAndBoardLinkAccess: 'no_access',
        organizationAccess: 'private',
        teamAccess: 'private'
      }
    },
    description: 'My Hello World Board'
  })
};

fetch('https://api.miro.com/v2/boards', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

To execute the board creation program, paste it into your text editor of choice (VSCode works great!). Save it to the directory of your chosing (Desktop works fine for this), and from the commend line execute the following:

```script
node miro_create_board.js
```

***Replace `miro_create_board.js` with whatever you named your file.***

It will output something similar to the folowing:

```json
❯ node miro_create_board.js
(node:30481) ExperimentalWarning: The Fetch API is an experimental feature. This feature could change at any time
(Use `node --trace-warnings ...` to show where the warning was created)
{
  id: 'uXjVOgqtYA8=',
  type: 'board',
  name: 'My Hello World',
  description: 'My Hello World Board',
  links: {
    self: 'http://api.miro.com/v2/boards/...',
    related: 'http://api.miro.com/v2/boards/...=/members?limit=20&offset=0'
  },
  createdAt: '2022-08-06T00:27:06Z',
  createdBy: { id: '...', type: 'user', name: 'John Smith' },
  currentUserMembership: {
    id: '...',
    type: 'board_member',
    name: 'John Smith',
    role: 'owner'
  },
  modifiedAt: '2022-08-06T00:27:06Z',
  modifiedBy: { id: '...', type: 'user', name: 'John Smith' },
  owner: { id: '...', type: 'user', name: 'John Smith' },
  permissionsPolicy: {
    collaborationToolsStartAccess: 'all_editors',
    copyAccess: 'anyone',
    copyAccessLevel: 'anyone',
    sharingAccess: 'team_members_with_editing_rights'
  },
  policy: {
    permissionsPolicy: {
      collaborationToolsStartAccess: 'all_editors',
      copyAccess: 'anyone',
      copyAccessLevel: 'anyone',
      sharingAccess: 'team_members_with_editing_rights'
    },
    sharingPolicy: {
      access: 'private',
      inviteToAccountAndBoardLinkAccess: 'no_access',
      organizationAccess: 'private',
      teamAccess: 'private'
    }
  },
  sharingPolicy: {
    access: 'private',
    inviteToAccountAndBoardLinkAccess: 'no_access',
    organizationAccess: 'private',
    teamAccess: 'private'
  },
  team: { id: '...', type: 'team', name: 'Dev team' },
  viewLink: 'https://miro.com/app/board/...'
}
```

#### Get My Boards

```javascript
const options = {
  method: 'GET',
  headers: {
    Accept: 'application/json',
    Authorization: 'Bearer eyJtaXJvL...'
  }
};

fetch('https://api.miro.com/v2/boards', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

For the 2nd script, the one that reads in a list of all boards you have created (even the one we created above!), execute it on the command line just like you did the program above:

```script
node miro_get_boards.js
```

***Replace `miro_get_boards.js` with whatever you named your file.***

It will output something similar to the folowing:

```json
❯ node miro_get_boards.js
(node:30501) ExperimentalWarning: The Fetch API is an experimental feature. This feature could change at any time
(Use `node --trace-warnings ...` to show where the warning was created)
{
  size: 3,
  offset: 0,
  limit: 20,
  total: 3,
  data: [
    {
      id: '...',
      type: 'board',
      name: 'Concept Board',
      description: '',
      links: [Object],
      createdAt: '2022-08-06T00:22:45Z',
      createdBy: [Object],
      currentUserMembership: [Object],
      modifiedAt: '2022-08-06T00:22:53Z',
      modifiedBy: [Object],
      owner: [Object],
      permissionsPolicy: [Object],
      policy: [Object],
      sharingPolicy: [Object],
      team: [Object],
      viewLink: 'https://miro.com/app/board/...'
    },
    {
      id: '...',
      type: 'board',
      name: 'Post It Notes Wall',
      description: '',
      links: [Object],
      createdAt: '2022-08-06T00:23:19Z',
      createdBy: [Object],
      currentUserMembership: [Object],
      modifiedAt: '2022-08-06T00:23:24Z',
      modifiedBy: [Object],
      owner: [Object],
      permissionsPolicy: [Object],
      policy: [Object],
      sharingPolicy: [Object],
      team: [Object],
      viewLink: 'https://miro.com/app/board/...'
    },
    {
      id: '...',
      type: 'board',
      name: 'My Hello World',
      description: 'My Hello World Board',
      links: [Object],
      createdAt: '2022-08-06T00:27:06Z',
      createdBy: [Object],
      currentUserMembership: [Object],
      modifiedAt: '2022-08-06T00:27:06Z',
      modifiedBy: [Object],
      owner: [Object],
      permissionsPolicy: [Object],
      policy: [Object],
      sharingPolicy: [Object],
      team: [Object],
      viewLink: 'https://miro.com/app/board/...'
    }
  ],
  links: {
    self: 'http://api.miro.com/v2/boards?query=&owner=&limit=20&offset=0&sort=default{&team_id}'
  },
  type: 'list'
}
```

***Note: the last board created, in our case the one we created with the other program, shows up last, and by default will be called "My Hello World".***

## Further Reading

We just scratched the surface of Miro and the REST API that they employ. There are other end-points you can explore, and tutorials for several of them. Start here <https://developers.miro.com/docs/rest-api-how-tos>, and see what else you can create!
