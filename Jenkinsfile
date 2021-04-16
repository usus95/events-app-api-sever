// prerequisites: a nodejs app must be deployed inside a kubernetes cluster
// TODO: look for all instances of [] and replace all instances of 
//       the 'variables' with actual values 
// variables:
//      https://github.com/usus95/events-app-api-sever.git
//      gcr.io/roidtc-april115/api-server-image@sha256:674c5254353f1030f6124d34aed080c9d9fe30a2b0234e9925b3b51908b0e441
//      oidtc-april115
//      cluster-1 
//      us-central1-c
//      the following values can be found in the yaml:
//      demo-api
//      demo-api (in the template/spec section of the deployment)

pipeline {
    agent any 
    options {
        // This is required if you want to clean before build
        skipDefaultCheckout(true)
    }
    stages {
        stage('Clean Up') {
            steps {
                // Clean before build
                cleanWs()
                echo "Building ${env.JOB_NAME}..."
            }
        }
        stage('Stage 1') {
            steps {
                echo 'Retrieving source from github' 
                git branch: 'main',
                    url: 'https://github.com/usus95/events-app-api-sever.git'
                echo 'Did we get the source?' 
                sh 'ls -a'
            }
        }
        stage('Stage 2') {
            steps {
                echo 'workspace and versions' 
                sh 'echo $WORKSPACE'
                sh 'gcloud version'
                sh 'node -v'
                sh 'npm -v'
            }
        }        
         stage('Stage 3') {
            environment {
                PORT = 8081
            }
            steps {
                echo 'install dependencies' 
                sh 'npm install'
                echo 'Run tests'
                sh 'npm test'
        
            }
        }        
         stage('Stage 4') {
            steps {
                echo "build id = ${env.BUILD_ID}"
                echo 'Tests passed on to build Docker container'
                sh "gcloud builds submit -t gcr.io/oidtc-april115/gcr.io/roidtc-april115/api-server-image@sha256:674c5254353f1030f6124d34aed080c9d9fe30a2b0234e9925b3b51908b0e441:v2.${env.BUILD_ID} ."
            }
        }        
         stage('Stage 5') {
            steps {
                echo 'Get cluster credentials'
                sh 'gcloud container clusters get-credentials cluster-1 --zone us-central1-c --project oidtc-april115'
                echo 'Update the image'
                echo "gcr.io/oidtc-april115/gcr.io/roidtc-april115/api-server-image@sha256:674c5254353f1030f6124d34aed080c9d9fe30a2b0234e9925b3b51908b0e441:2.${env.BUILD_ID}"
                sh "kubectl set image deployment/demo-api demo-api=gcr.io/oidtc-april115/gcr.io/roidtc-april115/api-server-image@sha256:674c5254353f1030f6124d34aed080c9d9fe30a2b0234e9925b3b51908b0e441:v2.${env.BUILD_ID} --record"
            }
        }
    }
}
