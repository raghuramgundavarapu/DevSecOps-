
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
                sh 'docker build --pull -t devsecops-app:v2 .'
            }
        }

        stage('Verify Docker Image') {
            steps {
                sh 'docker image inspect devsecops-app:v2'
            }
        }

        stage('Trivy Security Scan') {
            steps {
                echo 'Scanning Docker image for vulnerabilities...'
                sh 'trivy image --scanners vuln --severity HIGH,CRITICAL --exit-code 1 devsecops-app:v2'
            }
        }
    }
}

