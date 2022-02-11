# appdeploy

_Testing for GitOps, a guide to deploying a Fuse on Karaf image with ArgoCD and Helm to a CRC cluster._

![Image](https://miro.medium.com/max/700/1*9q37KuHZFWC7XOZRSQpJ6Q.png)

- This was developed and tested on Windows. The CRC VM was given some more resources by running
```console
crc delete
crc start
# Increase defaults of 4 and 8192 to whatever you feel comfortable with
crc config set cpus 6 
crc config set memory 16384
```
The settings were changed from the start, so the configuration was not tested on default Windows CRC settings.
Tested on version
```console
PS C:\Users\frank> crc version
CodeReady Containers version: 1.39.0+ba8a8acc
OpenShift version: 4.9.15 (bundle installed at D:\devtools\crc\crc_hyperv_4.9.15.crcbundle)
```
It helps to have Git Bash installed on your windows system so you can copy paste and run linux commands from Red Hats documentation. Windows Powershell will even automatically open Git Bash when you try to execute a bash script.

Some prerequisites if you want to make this run as-is. 
- Download and install your latest [CRC](https://console.redhat.com/openshift/create/local).
- Open the web console, click on the ? symbol in the menu bar, choose command line tools and download at least the oc CLI and add the path to the binary in your %PATH% environment variable.
- Download maven if necessary, and configure redhat repos. Use this [settings.xml](https://github.com/frankvanhof/fuse710-karaf-camel-log/blob/9c28dd157a1ee80a3af59437a22c9d71602841f1/configuration/settings.xml). This is necesarry in the step where you clone and build the repo with the source code.

[Here](https://github.com/frankvanhof/fuse710-karaf-camel-log) you can clone or fork a repo that contains code to build a Fuse bundle, wrap it in a Karaf container, and then wrap that in a container image that is pushed to an Openshift registry. Follow that repo's readme and make sure you work with a karaf-log namespace when you execute the `mvn clean -DskipTests oc:deploy -Popenshift -Djkube.generator.from=karaf-log/fuse-karaf-openshift:1.10` command. 

Then when you install Openshift GitOps by following [these](https://docs.openshift.com/container-platform/4.9/cicd/gitops/installing-openshift-gitops.html) instructions, you should then be able to create an ArgoCD app and configure it to use this Git Repo to synch with a namespace of your choosing. Follow the installation instructions and test by creating the spring-boot application in the instructions. This does nothing more than watching a repo and pulling and deploying an image, but it demonstrates that privileges are set correctly and ArgoCD functions correctly. Besides, it has a nice UI to stare at.

ToDo: 
- Steps to deploy a GitLab Server on CRC and add GitLab CI to the karaf-log repo so we can add CI to trigger a complete CI + GitOps cycle.
- Now the ArgoCD app is created by hand in the UI. That is great for demonstration purposes, but we want to create Argo apps straight from Git, of course.
