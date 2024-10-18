pipeline {
agent any
tools{
    maven "maven"
}
environment {
        APP_NAME = 'loginregisterapp'
        MAJOR_VERSION = "1.0"
        BUILD_VERSION = "${MAJOR_VERSION}.${BUILD_NUMBER}"
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
    stage("Artifactory"){
        steps{
            sh "aws s3 cp $WORKSPACE/target/*.war s3://b100-capstone-project-login-app-artifactory/${APP_NAME}-${BUILD_VERSION}.war "
        }
    }
}

}