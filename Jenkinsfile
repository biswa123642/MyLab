def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]

pipeline{
    agent { label "slave" }
    tools {
        maven 'maven-3.9.9'
        jdk 'java-21'
    }

    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = credentials('nexusurl')
        NEXUS_REPOSITORY = "BiswajitApp"
        NEXUS_CREDENTIAL_ID = "nexuscredential"
        ARTIFACT_VERSION = "${BUILD_NUMBER}"
        TOMCAT_URL = credentials('tomcaturl')
    }

    stages {

        stage ('Build'){
            steps {
                sh 'mvn clean install package'
            }
        }

        stage('SonarQube Code Analysis') {
            steps {
              withSonarQubeEnv('sonar') {
                  sh "mvn sonar:sonar -Dsonar.projectKey=myapplication -Dsonar.projectName='myapplication'"
              }
            }
        }

        stage("SonarQube Quality Gate Check") {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                  script {
                     waitForQualityGate abortPipeline: true
                  }
                }
            }
        }

        stage ('Deploy'){
            steps {
                script {
                    deploy adapters: [tomcat9(url: TOMCAT_URL, 
                        credentialsId: 'tomcatcredential')], 
                    war: '**/*.war',
                    onFailure: false,
                    contextPath: null
                }
            }
        }

        stage('Publish Artifact Nexus') {
            steps {
                script {
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

    }

    post {
        always {
            echo 'Slack Notifications.'
            slackSend channel: '#myjenkins',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }

}
