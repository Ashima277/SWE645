pipeline {
    agent any

    environment {
        PROJECT_ID = 'student-survey-452118'
        IMAGE_NAME = 'us-central1-docker.pkg.dev/student-survey-452118/my-docker-repo/survey'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git credentialsId: 'git-token', url: 'https://github.com/Ashima277/SWE645', branch: 'main'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }
        
        stage('Push to Artifact Registry') {
            steps {
                sh 'gcloud auth configure-docker us-central1-docker.pkg.dev'
                sh 'docker push $IMAGE_NAME:$BUILD_NUMBER'
            }
        }

        stage('Deploy to GKE') {
            steps {
                sh 'kubectl set image deployment/survey-app survey-app=$IMAGE_NAME:$BUILD_NUMBER --record'
            }
        }
    }
}
