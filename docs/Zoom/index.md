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

# Build a web app using Zoom api calls using nodejs

1. Reproduce Sequence Diagram with PlantUML - <https://www.azuredevops.tips/generateplantumlinpipeline-yaml/>
1. Architecture overview with diagrams.py - <https://diagrams.mingrammer.com>
1. Test Plan - <https://acloudguru.com/hands-on-labs/build-and-test-a-node-js-app-in-azure-devops>
1. Azure DevOps Pipeline - <https://docs.microsoft.com/en-us/learn/modules/deploy-nodejs/>
1. Using the Zoom API Documentation - <https://marketplace.zoom.us/docs/sdk/native-sdks/web/reference>
1. Using the Zoom API Sample App - <https://github.com/zoom/sample-app-node>

## Step 1: Get your house in order (pre-requisites)

## Step 2: Sign-up for developer account

<https://developers.zoom.us/>

## Step 3: Download meetingsdk-web-sample

<https://github.com/zoom/meetingsdk-web-sample>

## Step 4: Create your test app

You will find your SDK key, and secret key from the Zoom developer dashboard:

1. Navigate to: <https://marketplace.zoom.us/>
2. Click on "Manage"
3. Click on "Develop"
4. Click on "Build App"
5. Click on "SDK" *NOTE: JWT apps are being deprecated in June 2023*
6. Fill in required information (put in whatever you like here, it's not important for this tutorial)
7. Find your "SDK" credentials, we need the key and secret, note those down somewhere

## Step 5: Modify your project

We need to change two lines in the index.js file from the meetingsdk-web-sample repository.

Add your keys below:

```javascript
const SDK_KEY = "YOUR_SDK_KEY";
const SDK_SECRET = "YOUR_SDK_SECRET";
```

## Step 6: Run your program

1. Navigate to where you downloaded the meetingsdk-web-sample repository
2. Run `npm install` to make sure you have all the right packages
3. Run `npm start` to run the program and open up the port (localhost:9999)

You will see something like the below:

```shell
> websdk-local@2.6.0 start
> set NODE_ENV=development && set BABEL_ENV=development && node corp.js --corp

https false
disableCORP false
[HPM] Proxy created: /meeting.html  ->  http://127.0.0.1:9998/
ℹ ｢wds｣: Project is running at http://0.0.0.0:9999/
ℹ ｢wds｣: webpack output is served from /static
ℹ ｢wds｣: Content not from webpack is served from /Users/seanfay/dev/iben/meetingsdk-web-sample/Local
ℹ ｢wds｣: 404s will fallback to /index.html
Listening at http://127.0.0.1:9999/index.html
ℹ ｢wds｣: Project is running at http://0.0.0.0:9998/
ℹ ｢wds｣: webpack output is served from /static
ℹ ｢wds｣: Content not from webpack is served from /Users/seanfay/dev/iben/meetingsdk-web-sample/Local
ℹ ｢wds｣: 404s will fallback to /index.html
Listening at http://127.0.0.1:9999/index.html
ℹ ｢wdm｣: asset index.min.js 6.68 MiB [emitted] (name: index)
asset meeting.min.js 6.68 MiB [emitted] (name: meeting)
runtime modules 51.7 KiB 26 modules

...

ℹ ｢wdm｣: Compiled successfully.
ℹ ｢wdm｣: Compiling...
ℹ ｢wdm｣: Compiling...
ℹ ｢wdm｣: assets by status 13.4 MiB [cached] 2 assets
cached modules 6.47 MiB (javascript) 51.7 KiB (runtime) [cached] 66 modules
./js/index.js 4.85 KiB [built]
webpack 5.28.0 compiled successfully in 247 ms
ℹ ｢wdm｣: Compiled successfully.
ℹ ｢wdm｣: assets by status 13.4 MiB [cached] 2 assets
cached modules 6.47 MiB (javascript) 51.7 KiB (runtime) [cached] 66 modules
./js/index.js 4.85 KiB [built]
webpack 5.28.0 compiled successfully in 291 ms
ℹ ｢wdm｣: Compiled successfully.
```

Point your browser to <http://0.0.0.0:9999/>

![Zoom WebSDK](zoom_websdk_screenshot1.png)

## Step 7: Join a scheduled meeting using the sample app

From the Zoom web interface (<https://zoom.us/meeting/schedule>), schedule a meeting. Note the meeting number as we will use it shortly. Join the meting as the host using the desktop app, or the web version (either works).

From the Zoom WebSDK (<http://0.0.0.0:9999/>), input your meeting number and a password if you set one. Then join it!

## Step 8: Celebrate

If everything worked you should see your self twice, one will be the name in the field to the far left in the Zoom WebSDK (e.g., Local2.6.0Mac#chrome/...) and the host session.

## Further Reading
