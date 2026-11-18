
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'raghuram3040/devsecops-app'
        IMAGE_TAG = "v${BUILD_NUMBER}"
        K8S_HOST = '172.31.8.154'
    }

    stages {
        stage('Verify Pipeline') {
            steps {
                echo 'Jenkins pipeline is working!'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build --pull \
                      -t devsecops-app:${IMAGE_TAG} \
                      -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Verify Docker Image') {
            steps {
                sh 'docker image inspect devsecops-app:${IMAGE_TAG}'
            }
        }

        stage('Trivy Security Scan') {
            steps {
                sh '''
                    trivy image --scanners vuln \
                      --severity HIGH,CRITICAL \
                      --exit-code 1 \
                      ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_TOKEN'
                )]) {
                    sh '''
                        set +x
                        echo "$DOCKER_TOKEN" | docker login \
                          -u "$DOCKER_USER" \
                          --password-stdin

                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    ssh -i /var/lib/jenkins/.ssh/k8s_deploy_key \
                      -o BatchMode=yes \
                      -o StrictHostKeyChecking=yes \
                      jenkins-deploy@${K8S_HOST} \
                      "sudo -n /usr/local/bin/jenkins-k8s-deploy ${IMAGE_NAME}:${IMAGE_TAG}"
                '''
            }
        }
    }

    post {
        success {
            echo 'Docker image published and Kubernetes deployment successful!'
        }
        failure {
            echo 'Pipeline failed. Check the Jenkins console output.'
        }
    }
}

