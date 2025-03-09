pipeline {
    agent any

    environment {
        PROJECT_ID = 'student-survey-452118'
        IMAGE_NAME = 'us-central1-docker.pkg.dev/student-survey-452118/my-repo/hw2'
    }

    stages {
        stage('Clone Repository') {
            steps {
                cleanWs()
                git credentialsId: 'git-token', url: 'https://github.com/Ashima277/SWE645', branch: 'main'
            }
        }

        stage('Check Git Status') {
            steps {
                sh 'git status'
            }
        }

        stage('Test Docker') {
            steps {
                sh 'docker version'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'  // Ensure correct image tagging
            }
        }
        
        stage('Push to Artifact Registry') {
            steps {
                withCredentials([file(credentialsId: 'gcp-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh 'gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS'
                    sh 'gcloud auth configure-docker us-central1-docker.pkg.dev'
                    sh 'docker push $IMAGE_NAME:$BUILD_NUMBER'
                }
            }
        }

        stage('Deploy to GKE') {
            steps {
                withCredentials([file(credentialsId: 'gcp-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh '''
                    gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS
                    gcloud config set project student-survey-452118
                    gcloud container clusters get-credentials hw-cluster --zone us-central1-c
                    kubectl set image deployment/survey-app hw2-sha256-1=$IMAGE_NAME:$BUILD_NUMBER --record
                    '''
                }
            }
        }
    }
}
