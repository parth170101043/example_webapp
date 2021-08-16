def builderImage
def productionImage
def ACCOUNT_REGISTRY_PREFIX
def GIT_COMMIT_HASH

pipeline {
    agent any
    stages {
        stage('Checkout Source Code and Logging Into Registry') {
            steps {
                echo 'Logging Into the Private ECR Registry'
                script {
                   
                      GIT_COMMIT_HASH = sh (script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)
                    ACCOUNT_REGISTRY_PREFIX = "277442681126.dkr.ecr.us-east-2.amazonaws.com"
                    sh """
                    echo $GIT_COMMIT_HASH
                    aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 277442681126.dkr.ecr.us-east-2.amazonaws.com
                    """
                }
            }
        }

        stage('Make A Builder Image') {
             options {
              timeout(time: 7, unit: 'MINUTES')   // timeout on this stage
          }
            steps {
                echo 'Starting to build the project builder docker image'
                script {
                     builderImage = docker.build("${ACCOUNT_REGISTRY_PREFIX}/example-webapp-builder:${GIT_COMMIT_HASH}", "-f ./Dockerfile.builder .")
                     builderImage.push()
                }
            }
        }

       

      
 
        
    }
}
