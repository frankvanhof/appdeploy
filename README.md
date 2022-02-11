# appdeploy

_Testing for GitOps, a guide to deploying a Fuse on Karaf image with ArgoCD and Helm to a CRC cluster._

![Image](https://miro.medium.com/max/700/1*9q37KuHZFWC7XOZRSQpJ6Q.png)

- This was developed and tested on Windows. The CRC VM was given some more resources by running
```console
# Docs say this only works on new VMs
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
It helps to have [Git Bash](https://git-scm.com/download/win) installed on your windows system so you can copy paste and run linux bash commands from Red Hat's documentation. Windows Powershell will even automatically open Git Bash when you try to execute a bash shell script.

Some prerequisites if you want to make this run as-is. 
- Download and install your latest [CRC](https://console.redhat.com/openshift/create/local).
- Open the web console, click on the ? symbol in the menu bar, choose command line tools and download at least the oc CLI and add the path to the binary in your %PATH% environment variable.
- Download maven if necessary, and configure redhat repos. Use this [settings.xml](https://github.com/frankvanhof/fuse710-karaf-camel-log/blob/9c28dd157a1ee80a3af59437a22c9d71602841f1/configuration/settings.xml). This is necesarry in the step where you clone and build the repo with the source code.

[Here](https://github.com/frankvanhof/fuse710-karaf-camel-log) you can clone or fork a repo that contains source code and Maven configuration to build a Fuse bundle, wrap it in a Karaf container, and then wrap that in a container image that is pushed to an Openshift registry. Follow that repo's readme and make sure you work with a karaf-log namespace when you execute the `mvn clean -DskipTests oc:deploy -Popenshift -Djkube.generator.from=karaf-log/fuse-karaf-openshift:1.10` command. Now the image is available in the image registry of the Openshift cluster.

Then install Openshift GitOps by following [these](https://docs.openshift.com/container-platform/4.9/cicd/gitops/installing-openshift-gitops.html) instructions. Follow the installation instructions and test your work by creating the spring-boot petclinic application as per the docs. This does nothing more than watching a git repo and pulling and deploying an image, but it verifies that privileges are set correctly and ArgoCD functions correctly. Besides, it has a nice UI to stare at and you can add owners and pets to your heart's desire. 

You should then be able to create an ArgoCD app and configure it to use this Git Repo to synch with a namespace of your choosing. Make that a new namespace and check the auto-create namespace checkbox. Something like test-gitops sounds appropriate to me. 

At this point, we have CD of that same image mentioned in the values.yaml file in this repository whenever we change something in the helm-deploy folder, or cause the Openshift namespace to be out of synch with the manifests in the helm-deploy folder. If we also want to change something in the Camel code and have that redeployed by Argo, we first have to push a new image to Openshift and force Argo to Synch, either via a nonsense Git commit, or via a forced synch through the UI or CLI. 

So the next step is to configure the CI pipeline in the source code git repo so that a commit resuts in a build and a new image and an updated chart.yaml so ArgoCD would synch the namespace with the new image. Additional pipelines would take care of commits in folders in a manifest repo like this one that are watched by GitOps instances in differente environments, like test, staging and production.

ToDo: 
- Deploy a GitLab Server on CRC and add GitLab CI to the karaf-log repo so we can add CI to trigger a complete CI + GitOps CD cycle. Useful tips [here](https://levelup.gitconnected.com/gitops-in-kubernetes-with-gitlab-ci-and-argocd-9e20b5d3b55b)
- Now the ArgoCD app is created by hand in the UI. That is great for demonstration purposes, but we want to create Argo apps straight from Git, of course.
- Add karaf-amq source code repo + add deployment yamls for that source to this manifest repo + add automatic deployment of an amq broker to that deployment somehow.
