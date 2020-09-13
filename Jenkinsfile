pipeline {
  agent {
    kubernetes {
      label 'auto-bot' 
      yamlFile 'build-pod.yaml'  // the pod definition file
    }
  }
  environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        // path to your Nexus
        NEXUS_URL = "your-nexus-server.com"
        // Repository usage
        NEXUS_REPOSITORY = "maven-releases"
        // Credential id on Jenkins use to authenticate with Nexus
        NEXUS_CREDENTIAL_ID = "nexus-credentials"
        MAVEN_CLI_OPTS = "--batch-mode"
        MAVEN_OPTS = "-Dmaven.repo.local=.m2/repository"
  }
  stages {
    stage('build') {
      steps {
        container('maven') {
        sh """
        mvn ${MAVEN_CLI_OPTS} compile
        echo 'build step'
        """
        }
      }    
    }
        
    stage('test') {
      steps {
        container('maven') {
        sh """
        mvn ${MAVEN_CLI_OPTS} test
        echo 'code has been test'
        """
        }
      }    
    }
        
    stage('package') {
      steps {
        container('maven') {
        sh """
        mvn ${MAVEN_CLI_OPTS} package -Dmaven.test.skip=true
        echo 'Packing the code '
        """
        }
      }    
    }
    
    stage('deploy') {
      steps {
        container('maven') {
          script {
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                    pom = readMavenPom file: "pom.xml";
                    // Find built artifact under target folder
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    // Print some info from the artifact found
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    // Extract the path from the File found
                    artifactPath = filesByGlob[0].path;
                    // Assign to a boolean response verifying If the artifact name exists
                    artifactExists = fileExists artifactPath;
                    
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                // Artifact generated such as .jar, .ear and .war files.
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],

                                // Lets upload the pom.xml file for additional information for Transitive dependencies
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );

                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
          }
        }
      }    
    }
            
  }
}
