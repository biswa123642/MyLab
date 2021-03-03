pipeline{
    //Directives
    agent any
    tools {
        maven 'maven'
    }

    stages {
        // Specify various stage with in stages

        // stage 1. Build
        stage ('Build'){
            steps {
                sh 'mvn clean install package'
            }
        }

        // Stage2 : Testing
        stage ('Test'){
            steps {
                echo ' testing......'

            }
        }

        // Stage3 : Publish the artifact ti nexus
        stage ('Publish to nexus'){
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'mydetails', classifier: '', file: 'target/mydetails-0.0.8.war', type: 'war']], credentialsId: '8c3c75a7-f58f-4981-95bb-1a8296205482', groupId: 'com.BISWAJIT', nexusUrl: '3.239.59.223:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'biswajit-release', version: '0.0.8'
                
                
            }
        }
      
        //Stage4 : Deploying
        stage ('Deploy'){
            steps {
                echo 'deploying.......'
            }
        }

    }

}
