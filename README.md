# maersk_assignment_1

A car rental company called FastCarz has a .net Web Application and Web API which are recently
migrated from on-premise system to Azure cloud using Azure Web App Service
and Web API Service.
The on-premises system had 3 environments Dev, QA and Prod.
The code repository was maintained in TFS and moved to Azure GIT now. The TFS has daily builds which
triggers every night which build the solution and copy the build package to drop folder.
deployments were done to the respective environment manually. The customer is planning to setup
Azure DevOps Pipeline service for below requirements:
1) The build should trigger as soon as anyone in the dev team checks in code to master branch.
2) There will be test projects which will create and maintained in the solution along the Web and API.
The trigger should build all the 3 projects - Web, API and test.
The build should not be successful if any test fails.
3) The deployment of code and artifacts should be automated to Dev environment.
4) Upon successful deployment to the Dev environment, deployment should be easily promoted to QA
and Prod through automated process.
5) The deployments to QA and Prod should be enabled with Approvals from approvers only

____________________________________________________________________________________________________________

1) The build should trigger as soon as anyone in the dev team checks in code to master branch.

Login into Azure DevOps and navigate to the project.
Navigate to Pipelines –> Builds.
Select the Repository as Azure Repos Git, and select the Repository under Repository Drop Down, select the main branch and click continue.
Now click on empty job
Select the Triggers Tab and select the "Enable Continous Integration" Check box.

___________________________________________________________________________________________________________________________________
2. There will be test projects which will create and maintained in the solution along the Web and API.
The trigger should build all the 3 projects - Web, API and test.

Move back to the Tasks Tab,  Click + in Agent job, search for Visual Syudio Build and click add thrice (need to add three tasks).
Configure Visual Studio Build Solution Tasks. 
Select the first  Visual Syudio Build task and change the display name as Build Web APP. Ensure provinding solution path under Solution field and pass MS build arguments for publishing the project. eg. /p:DeployOnBuild=True /p:DeployDefaultTarget=WebPublish /p:WebPublishMethod=FileSystem /p:DeleteExistingFiles=True /p:publishUrl=$(build.artifactstagingdirectory)
Select the 2nd  Visual Syudio Build task and change the display name as Build Web API. Ensure provinding solution path of API project under Solution field and pass MS build arguments for buiding the project . /p:DeployOnBuild=True /p:DeployDefaultTarget=WebPublish /p:WebPublishMethod=FileSystem /p:DeleteExistingFiles=True /p:publishUrl=$(build.artifactstagingdirectory)
Select the 3rd Visual Syudio Build task and change the display name as Build Test Project. Ensure provinding solution path of Test project under Solution field and pass MS build arguments for buiding the project.
Click + in Agent job, search for Visual Studio Test and click add.
Configure this task to run the tests from the assemblies built by test project. 
Click + in Agent job, search for publish Artifacts task
Configure this task to publish the artifacts on drop location.

--------------------------------------------------------------------------------------------------------------------------------------------------

3) The deployment of code and artifacts should be automated to Dev environment.

Select Releases under Pipelines section.
Select the release pipeline, then choose create release.
Under Artifacts, select Drop. The build pipeline we created in the previous steps produces the output used for the artifact.
To the right-hand side of the Drop icon, select the Continuous deployment trigger. This release pipeline has an enabled CD trigger, which executes a deployment every time there is a new build artifact available. Optionally, you can disable the trigger, when your deployments require manual execution.
Provide a name of stage as Dev.
On the left hand side of the Stage, click on Pre-Deployment Condition.  under Select Trigger Choose "After Release" so that deployment will start asa sson as there is a new release created.
Select Tasks. The tasks are the activities of deployment process performs.
Configure different tasks for Deploying the code on the Dev Server. eg. Copy artifacts from build server to Dev Sever, Manage the app pool, create website etc.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

4) Upon successful deployment to the Dev environment, deployment should be easily promoted to QA
and Prod through automated process.

Edit the release definition
Click on Add under Stages, and add two new Stage
Rename stages as QA and Prod
Click on triggers on QA stage and select After Stage. Selectr Dev in the stages drop down to start the deployment after Dev Stage.
Click on triggers on Prod stage and select After Stage. Select QA in the stages drop down to start the deployment after Dev Stage.


--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 The deployments to QA and Prod should be enabled with Approvals from approvers only.
 Edit the release definition.
Select QA stage, and on left side click on PreDeployment Condition.
Enable Pre-Deployment approvals, and provide the name of QA deployment approvers in the approvers list. Approvers will be notified when there is a deployment request on QA enviornment. 
 
Select Prod stage, and on left side click on PreDeployment Condition.
Enable Pre-Deployment approvals, and provide the name of Prod deployment approvers in the approvers list. Approvers will be notified when there is a deployment request on Prod enviornment. 
 




