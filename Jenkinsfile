pipeline {
    agent any

    environment {
        PROJECT_ID = 'student-survey-452118'
        IMAGE_NAME = 'us-central1-docker.pkg.dev/student-survey-452118/my-repo/hw2'
        GOOGLE_APPLICATION_CREDENTIALS = '/var/lib/jenkins/jenkins-gke-key'
    }

    stages {
        stage('Clone Repository') {
            steps {
                cleanWs() // Ensures a clean workspace before cloning
                git credentialsId: 'git-token', url: 'https://github.com/Ashima277/SWE645', branch: 'main'
            }
        }

        stage('Check Git Status') {
            steps {
                sh 'git status'  // Check for any changes in the working directory
            }
        }

        stage('Test Docker') {
            steps {
                sh 'docker version'  // Check Docker version
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'  // Build Docker image with unique tag
            }
        }
        
        stage('Push to Artifact Registry') {
            steps {
                withCredentials([file(credentialsId: 'gcp-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh 'gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS'  // Use the environment variable
                    sh 'gcloud auth configure-docker us-central1-docker.pkg.dev'  // Configures Docker to authenticate with Google Artifact Registry
                    sh 'docker push $IMAGE_NAME:$BUILD_NUMBER'  // Push image to Artifact Registry
                }
            }
        }

        stage('Deploy to GKE') {
            steps {
                sh 'gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS'
                sh 'gcloud container clusters get-credentials hw-cluster --zone us-central1-c'

                sh 'kubectl set image deployment/survey-app survey-app=$IMAGE_NAME:$BUILD_NUMBER --record'  // Update the GKE deployment
            }
        }
    }
}
