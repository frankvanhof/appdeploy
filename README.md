# appdeploy
testing for GitOps

Some prerequisites if you want to make this run as-is. The image you find in deployment.yaml, is `image-registry.openshift-image-registry.svc:5000/karaf-log/fuse710-karaf-camel-log`. So you need a karaf-log namespace and push that image to it to make the deployment work. 


[Here](image-registry.openshift-image-registry.svc:5000/karaf-log/fuse710-karaf-camel-log) you can clone or fork a repo that contains code to build a Fuse bundle, wrap it in a Karaf container, and then wrap that in a container image that is pushed to an Openshift registry. Follow that repo's readme and make sure you work with a karaf-log namespace when you execute the `mvn -DskipTests oc:deploy -Popenshift` command.

Then when you install Openshift GitOps by following [these]([https://link](https://docs.openshift.com/container-platform/4.9/cicd/gitops/installing-openshift-gitops.html)) instructions, you should then be able to create an ArgoCD app and configure it to use this Git Repo to synch a namespace of your choosing. 

ToDo: Steps to deploy a GitLab Server on CRC and add GitLab CI to the karaf-log repo so we can add CI to trigger a GitOps cycle.