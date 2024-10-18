pipeline {
agent any
tools{
    maven "maven"
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
            sh "aws s3 cp /var/lib/jenkins/workspace/capstone-project/target/*.war s3://b100-capstone-project-login-app-artifactory/"
        }
    }
}

}