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
                     builderImage = docker.build("${ACCOUNT_REGISTRY_PREFIX}/example_webapp_builder:${GIT_COMMIT_HASH}", "-f ./Dockerfile.builder .")
                     builderImage.push()
                    
                     builderImage.push("${env.GIT_BRANCH}")
                    builderImage.inside('-v $WORKSPACE:/output -u root') {
                        sh """
                           cd /output
                           lein uberjar
                        """
                    }
                }
            }
        }

         stage('Unit Tests') {
            steps {
                echo 'running unit tests in the builder image.'
                script {
                    builderImage.inside('-v $WORKSPACE:/output -u root') {
                    sh """
                       cd /output
                       lein test
                    """
                    }
                }
            }
        }
       
        stage('Build Production Image') {
            steps {
                echo 'Starting to build docker image'
                script {
                    productionImage = docker.build("${ACCOUNT_REGISTRY_PREFIX}/example_webapp:${GIT_COMMIT_HASH}")
                    productionImage.push()
                    productionImage.push("${env.GIT_BRANCH}")
                }
            }
        }
      
 
        
    }
}
