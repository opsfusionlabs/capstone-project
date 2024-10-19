pipeline {
agent any
tools{
    maven "maven"
}
environment {
        APP_NAME = 'loginregisterapp'
        MAJOR_VERSION = "1.0"
        DOCKER_USER = "vedantdevops"
        BUILD_VERSION = "${MAJOR_VERSION}.${BUILD_NUMBER}"
        NEW_BUILD_DOCKER_IMAGE = "${DOCKER_USER}" + "/" + "${APP_NAME}:${BUILD_VERSION}" 
}
stages{
    stage("Clean"){
        steps{
            cleanWs()
        }
    }
    stage("Code Commit"){
        steps{
               git changelog: false, poll: false, url: 'https://github.com/opsfusionlabs/capstone-project.git'
        }
    }
    stage("Unit Test"){
        steps{
               sh 'mvn test'
        }
    }
    stage("Integration Test maven"){
        steps{
               sh 'mvn verify -DskipUnitTests'
        }
    }
    stage("Package"){
        steps{
                 sh 'mvn package'
        }
    }
   
    stage('Static code analysis: Sonarqube'){
         
            steps{
               script{
                   withSonarQubeEnv(credentialsId: sonarqube-api) {
         sh 'mvn clean package sonar:sonar'
    }
               }
            }
       }
    
}

}
