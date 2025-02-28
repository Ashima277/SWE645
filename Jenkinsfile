pipeline {
    agent any

    environment {
        PROJECT_ID = 'student-survey-452118'
        CLUSTER_NAME = 'hw-cluster-1'
        ZONE = 'us-central1-c'
        IMAGE_NAME = 'aashima2707/hw2:latest'
        KUBE_CONFIG = credentials('gke-credentials') // ID from Jenkins
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/Ashima277/SWE645.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $hw2 ."
                sh "docker login -u aashima2707 -p docker_pswd"
                sh "docker push $hw2"
            }
        }

        stage('Deploy to GKE') {
            steps {
                withCredentials([file(credentialsId: 'gke-credentials', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh '''
                    gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS
                    gcloud container clusters get-credentials $hw-cluster-1 --zone $us-central1-c --project $student-survey-452118
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml
                    '''
                }
            }
        }
    }
}
