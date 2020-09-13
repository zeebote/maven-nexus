# Maven-Nexus
This repo provide jenkins file for a pipeline job to run a simple maven app on Kubernetes cluster then upload artifact to nexus. <br>
It is used as example in my [Jenkins CasC and JobDSL plugin](https://github.com/zeebote/jenkins-config-as-code.git) to create jenkins job from groovy script.  
The job was configured to trigger when a change pushed to Github. <br>
This job assumed that you already have Github hook trigger and Kubernetes cluster configured on jenkins master
