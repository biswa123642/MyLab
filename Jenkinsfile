pipeline{
    agent any
    tools {
        maven 'maven-3.9.9'
        jdk 'java-21'
    }
/*
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "52.188.125.203:8081"
        NEXUS_REPOSITORY = "BiswajitApp"
        NEXUS_CREDENTIAL_ID = "nexuscredential"
        ARTIFACT_VERSION = "${BUILD_NUMBER}"
    }
*/
    stages {

        stage ('Build'){
            steps {
                sh 'mvn clean install package'
            }
        }

        stage ('Deploy'){
            steps {
                script {
                    deploy adapters: [tomcat9(url: 'http://20.102.108.165:8080/', 
                        credentialsId: 'tomcatcredential')], 
                    war: '**/*.war',
                    onFailure: false,
                    contextPath: null
                }
            }
        }
/*
        stage('Publish Artifact Nexus') {
            steps {
                script {
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;

                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";

                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: ARTIFACT_VERSION,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging]
                            ]
                        );

                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
*/
    }

}
