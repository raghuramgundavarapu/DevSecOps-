
pipeline {
    agent any

    stages {
        stage('Verify Pipeline') {
            steps {
                echo 'Jenkins pipeline is working!'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t devsecops-app:v1 .'
            }
        }

        stage('Verify Docker Image') {
            steps {
                sh 'docker image inspect devsecops-app:v1'
            }
        }
    }
}

