# Tekton Pipelines
Steps to create a pipeline:
- Create custom or install existing reusable Tasks
- Create a Pipeline and PipelineResources to define your application's delivery pipeline
- Create a PersistentVolumeClaim to provide the volume/filesystem for pipeline execution or provide a VolumeClaimTemplate which creates a PersistentVolumeClaim
- Create a PipelineRun to instantiate and invoke the pipeline

## Pipeline Overview
![image](https://user-images.githubusercontent.com/36239840/133944055-7e25394c-ec19-41af-b267-2bbae7c0242c.png)
## Install OpenShift Pipelines Operator
- Make sure you are on the <b>Administrator</b> perspective on the web console.
- Go to <b>Operators > OperatorHub</b> and search for 'OpenShift Pipelines'.
- Click on <b>Red Hat OpenShift Pipelines Operator</b>, and then install.
- Leave the default Settings and click on <b>Subscribe</b>.
- Once the operator is installed, you can find it in <b>Operators > Installed Operators</b> section.
## View & Edit the buildah cluster task
- From the administrator perspective, go to <b>Pipelines > Tasks</b> and go to the 'Cluster Tasks' tab.
- Select the 'buildah' cluster task<br>
![buildah](https://user-images.githubusercontent.com/36239840/133586709-55ceca42-2be3-4d2b-ba3d-df07074d1e6d.JPG)
- Click on 'Actions' dropdown from the top right, and select 'Edit Cluster Task'.<br>
![buildah-1](https://user-images.githubusercontent.com/36239840/133586909-b929b851-fd0e-4423-ad77-2c43f286b282.JPG)
- Look for the variable ```TLSVERIFY``` in the YAML document. Notice that the default value of ```TLSVERIFY``` is set to true.<br>
![tlsverify](https://user-images.githubusercontent.com/36239840/133587742-c729f55e-c9d1-4e3b-bd1d-bc7ae0a3a9b3.JPG)
- Change the default value of ```TLSVERIFY``` from ```true``` to ```false```. Click 'Save' and then 'Reload' to save your changes.
![TLSVERIFYFALSE](https://user-images.githubusercontent.com/36239840/133588009-d3b37815-d268-4cea-aedd-8e3dbc989514.JPG)


## Create project & Service account
Create a project for the sample application that you will be using in this tutorial:
```
oc new-project ci-env
oc new-project dev-env
oc new-project stage-env
```
Make sure you are in the ```ci-env``` project
```
oc project ci-env
```
Run the following command to see the pipeline service account:
```
oc get serviceaccount pipeline
```

```
oc adm policy add-scc-to-user privileged system:serviceaccount:ci-env:pipeline -n ci-env
oc adm policy add-scc-to-user privileged system:serviceaccount:ci-env:pipeline -n dev-env
oc adm policy add-scc-to-user privileged system:serviceaccount:ci-env:pipeline -n stage-env
oc adm policy add-role-to-user edit system:serviceaccount:ci-env:pipeline -n ci-env
oc adm policy add-role-to-user edit system:serviceaccount:ci-env:pipeline -n dev-env
oc adm policy add-role-to-user edit system:serviceaccount:ci-env:pipeline -n stage-env
```
## Create Tasks
- create apply-manifest task
```
oc create -f https://raw.githubusercontent.com/IBMDeveloperMEA/oc-devops/main/tasks/apply-manifest-task.yaml -n ci-env
```
- create update-deployment task
```
oc create -f https://raw.githubusercontent.com/IBMDeveloperMEA/oc-devops/main/tasks/update-deployment-task.yaml -n ci-env
```
- create test-app task
```
oc create -f https://raw.githubusercontent.com/IBMDeveloperMEA/oc-devops/main/tasks/test-task.yaml -n ci-env
```
```
tkn task ls
```
```
tkn clustertask ls
```
## Create Pipeline
```
oc create -f https://raw.githubusercontent.com/IBMDeveloperMEA/oc-devops/main/pipeline.yaml -n ci-env
```
```
tkn pipeline ls
```
## Create PersistentVolumeClaim (PVC)
```
oc create -f https://raw.githubusercontent.com/IBMDeveloperMEA/oc-devops/main/pvc.yaml -n ci-env
```
```
oc get pvc -n ci-env
```
## Trigger Pipeline
```
tkn pipeline start e2e-pipeline
```
```
tkn pipelinerun ls
```
```
tkn pipeline logs -f
```
## Triggers
```
oc create -f https://raw.githubusercontent.com/IBMDeveloperMEA/oc-devops/main/trigger/trigger-template.yaml -n ci-env
```
```
oc create -f https://raw.githubusercontent.com/IBMDeveloperMEA/oc-devops/main/trigger/trigger-binding.yaml -n ci-env
```
```
oc create -f https://raw.githubusercontent.com/IBMDeveloperMEA/oc-devops/main/trigger/event-listener.yaml -n ci-env
```
## Resources
- <a href='https://github.com/openshift/pipelines-tutorial'>OpenShift Pipelines Tutorial</a>
- <a href='https://github.com/tektoncd/catalog'>Tekton Reusable Tasks</a>
- <a href='https://github.com/vladsancira/nodejs-tekton'>Nodejs Tekton Pipelines Tutorial</a>
- <a href='https://hub.tekton.dev/tekton/task/git-clone'>Tekton git-clone ClusterTask</a>
- <a href='https://developer.ibm.com/tutorials/tekton-triggers-101/'>Tekton Triggers</a>
