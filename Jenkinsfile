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
        
        //Stage3 : Publish the artifacts to Nexus
        stage ('Publish to Nexus'){
            steps {    
                nexusArtifactUploader artifacts: [[artifactId: 'mydetails', classifier: '', file: 'target/mydetails-0.0.8.war', type: 'war']], credentialsId: 'b9fa9687-ccbc-4196-86e1-6488fbfece9e', groupId: 'com.BISWAJIT', nexusUrl: '100.26.232.221:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'biswajit-release', version: '0.0.8'
            }
        }
        
        // Stage3 : Deploying
        stage ('Deploy'){
            steps {
                echo ' Deploying...........'

            }
        }

        
        
    }

}
