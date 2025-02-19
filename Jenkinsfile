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
    stage('Static code analysis: Sonarqube'){
        steps{
            script{
                withSonarQubeEnv('SonarQube') { 
                sh 'mvn clean package sonar:sonar'
                    }
            }
        }
    }
    stage("Quality Gate") {
        steps {
            timeout(time: 1, unit: 'HOURS') {
            waitForQualityGate abortPipeline: true
            }
        }
    }
          
    stage("Artifactory"){
        steps{
            sh "aws s3 cp $WORKSPACE/target/*.war s3://b100-capstone-project-login-app-artifactory/${APP_NAME}-${BUILD_VERSION}.war "
        }
    }
    stage("Build Docker Image"){
        steps{
            sh ''' cd $WORKSPACE ''' 
            sh "docker build -t ${NEW_BUILD_DOCKER_IMAGE} . "

        }
    }
    stage("image scan"){
        steps{
           sh """   
                trivy image --scanners vuln ${NEW_BUILD_DOCKER_IMAGE} > scan.txt
                cat scan.txt
            """
        }
    }
    stage ('Registery Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                    sh ' docker login -u $USERNAME -p $PASSWORD '
                }
            }  
    }
    stage ('Push Docker Image') {
            steps {
                sh " docker push ${NEW_BUILD_DOCKER_IMAGE} "
            }  
    }
    stage("Update Mainfest Files"){
           steps{
                script {
                            echo "Assume the Deploy feature/alpha is Success"
                        
                            timeout(time: 1, unit: 'MINUTES') {
                                env.userChoice = input message: "Do you want to Deploy ${NEW_BUILD_DOCKER_IMAGE} of Application ? ",
                                    parameters: [choice(name: 'New Deploymnet Conformation', choices: 'no\nyes', description: 'Choose "yes" if you want to deploy this build')]
                            }
                            if (userChoice == 'yes') {
                        
                                build job: 'k8-manifest-files', parameters: [string(name: 'docker_image_name', value: "${APP_NAME}"), string(name: 'release_version_tag_id', value: "${BUILD_VERSION}")]
                            }
                            else if(userChoice == 'no') {
                                echo "Process abort by: ${user}"
                            }
                                
                        }
        }
    }

}

}