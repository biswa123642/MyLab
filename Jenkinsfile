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
                nexusArtifactUploader artifacts: [[artifactId: 'mydetails', classifier: '', file: 'target/mydetails-0.0.8.war', type: 'war']], credentialsId: 'c5efe778-2efe-485d-9411-96ecfb60f443', groupId: 'com.BISWAJIT', nexusUrl: '3.94.148.27:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'Biswajit-Release', version: '0.0.8'
                
                
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
