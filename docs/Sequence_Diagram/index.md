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

# Sequence Diagram

Here we will show how to create Diagrams using PlantUml from your computer or using the Pipeline.

## Using PlantUML on computer

In order to create diagrams using PlantUML we need first to have a text editor in order to write the code. We will be using Visual Studio Code as it has an extension in order to let us see the diagram.

1. Install VSCode

    [https://code.visualstudio.com/](https://code.visualstudio.com/)

1. Install Java SDK

    The VSCode uses plantuml.jar in order to create the images. So in order to create the images we first need to install Java.

    [https://www.java.com/en/download/](https://www.java.com/en/download/)

    ## Set the environment variable to the java path

    ![alt text](/_images/SQW1.png "System ENV")

    Press on Windows Start then type Env and select Edit System Environments.

    ![alt text](/_images/SQW2.png "Env")

    Click Edit on Path

    ![alt text](/_images/SQW3.png "Edit Path")

    Click New Add Path for Java. To check if everything is working as expected open the CMD and type `java` and you should get the Java Command Line Help

1. Install the extension

    [https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml](https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml)

1. Install GraphViz 

    [https://graphviz.gitlab.io/_pages/Download/Download_windows.html](https://graphviz.gitlab.io/_pages/Download/Download_windows.html )

1. Create a new file and give it a name, the file should end in .puml .

    ~~~~
    @startuml
    a->b
    b->c
    @enduml
    ~~~~

    After we create the file with the syntax that we want, we can press Alt+D or Ctrl+Alt+P and type > PlantUML: Preview Current Diagram, and a new tab should open and we should see the diagram.

    ![alt text](/_images/SQExample.png "Example")

## Using PlantUML from DevOPS Pipeline

1. Connect to https://dev.azure.com/

1. Locate the repository Drawings or follow this link: https://dev.azure.com/sjultra/VzxyTools/_git/Drawings

1. Clone the repository

`git clone git@ssh.dev.azure.com:v3/sjultra/VzxyTools/Drawings`

1. After the clone is done go to the folder code

`cd code/`

1. And then create a newfile there and write the code for the diagram. the file should end in .puml .

1. After the file is created and saved it is time to push the newly created file:

~~~~
cd ..
git add .
git commit -m "Add Name_of_file"
git push
~~~~

1. After the push is done the UMLPlant pipeline will create the diagram and in order to have the diagram

~~~~
git fetch
git pull
~~~~
And we should see the diagram saved with the same name but in .png and .jpeg format in the image folder.

## Syntax

The syntax to start and end a PlantUML file is :

~~~~
@startuml
--------
@enduml
~~~~

The !define statement helps you define a url to the style library

~~~~
!define AzurePuml link_to_style_lybrary
~~~~

The !includeurl statement defines the library. If you want to see all the icons available for this particular library head to https://github.com/RicardoNiepel/Azure-PlantUML/blob/master/AzureSymbols.md.

~~~~
AzureDevOps(azureDevOps,"Azure DevOps","https://dev.azure.com/azure-devops-tips")
~~~~

AzureDevOps is the name of the icon you want to display. It takes three parameter:

![alt text](/_images/SQAzureDevOPS.png "Sequence Diagram Azure DevOPS").

~~~~
AzureDevOps(objectreference,"label","description","summary")
object reference
label
technology
description
~~~~

Then you use the object reference to create the relationships between each icon.

## Declaring Participants

We can use the following keywords to declare the participant in the diagram:

* participant
* actor
* boundary
* control
* entity
* collections
* queue

~~~~
@startuml
participant participant as Foo
actor       actor       as Foo1
boundary    boundary    as Foo2
control     control     as Foo3
entity      entity      as Foo4
database    database    as Foo5
collections collections as Foo6
queue       queue       as Foo7
Foo -> Foo1 : To actor 
Foo -> Foo2 : To boundary
Foo -> Foo3 : To control
Foo -> Foo4 : To entity
Foo -> Foo5 : To database
Foo -> Foo6 : To collections
Foo -> Foo7: To queue
@enduml
~~~~

Also we can rename a participant using the `as` keyword.

![alt text](/_images/SQParticipants.png "Participants")