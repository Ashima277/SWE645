pipeline {
    agent any

    environment {
        PROJECT_ID = 'student-survey-452118'
        IMAGE_NAME = 'us-central1-docker.pkg.dev/student-survey-452118/my-repo/hw2'
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
        sh 'git log -1 --oneline'
    }
         }
        stage('Test Docker') {
    steps {
        sh 'docker version'
    }
}
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }
        
        stage('Push to Artifact Registry') {
            steps {
                withCredentials([googleServiceAccount(credentialsId: 'gsa')]) {
                sh 'gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS
                sh 'gcloud auth configure-docker us-central1-docker.pkg.dev'
                sh 'docker push $IMAGE_NAME:$BUILD_NUMBER'
                }
            }
        }

        stage('Deploy to GKE') {
            steps {
                sh 'kubectl set image deployment/survey-app survey-app=$IMAGE_NAME:$BUILD_NUMBER --record'
            }
        }
    }
}
