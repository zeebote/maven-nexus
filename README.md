# Maven-Nexus
This repo provide jenkins file for a pipeline job to run a simple maven app on Kubernetes cluster then upload artifact to Nexus. <br>
It is used as example in my [Jenkins CasC and JobDSL plugin](https://github.com/zeebote/jenkins-config-as-code.git) to create jenkins job from groovy script.  
This job was configured to trigger when a change pushed to Github and It is also assume that Github hook trigger and Kubernetes cluster already configured on Jenkins master
