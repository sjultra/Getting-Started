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

# Build a web app using office 365 api calls using nodejs

1. Reproduce Sequence Diagram with PlantUML - https://www.azuredevops.tips/generateplantumlinpipeline-yaml/
1. Architecture overview with diagrams.py - https://diagrams.mingrammer.com
1. Test Plan - https://acloudguru.com/hands-on-labs/build-and-test-a-node-js-app-in-azure-devops
1. Azure DevOps Pipeline - https://docs.microsoft.com/en-us/learn/modules/deploy-nodejs/
1. Office 365 API Documentation - https://docs.microsoft.com/en-us/office/office-365-management-api/get-started-with-office-365-management-apis
1. Office 365 API example - https://docs.microsoft.com/en-us/graph/tutorials/node?tutorial-step=3


When you create an application that needs access to secured services like the Office 365 Management APIs, you need to provide a way to let the service know if your application has rights to access it. The Office 365 Management APIs use Azure AD to provide authentication services that you can use to grant rights for your application to access them.

There are four key steps:

Register your application in Azure AD. To allow your application access to the Office 365 Management APIs, you need to register your application in Azure AD. This allows you to establish an identity for your application and specify the permission levels it needs to access the APIs.

Get Office 365 tenant admin consent. An Office 365 tenant admin must explicitly grant consent to allow your application to access their tenant data by means of the Office 365 Management APIs. The consent process is a browser-based experience that requires the tenant admin to sign in to the Azure AD consent UI and review the access permissions that your application is requesting, and then either grant or deny the request. After consent is granted, the UI redirects the user back to your application with an authorization code in the URL. Your application makes a service-to-service call to Azure AD to exchange this authorization code for an access token, which contains information about both the tenant admin and your application. The tenant ID must be extracted from the access token and stored for future use.

Request access tokens from Azure AD. Using your application's credentials as configured in Azure AD, your application requests additional access tokens for a consented tenant on an ongoing basis, without the need for further tenant admin interaction. These access tokens are called app-only tokens because they do not include information about the tenant admin.

Call the Office 365 Management APIs. The app-only access tokens are passed to the Office 365 Management APIs to authenticate and authorize your application.

The following diagram shows the sequence of consent and access token requests.