pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        IMAGE_NAME = "bobby1212/sample-app"
        IMAGE_TAG = "latest"
        KUBE_CONFIG = "/var/lib/jenkins/.kube/config"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Using local sample.war — skipping Git clone'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Push to DockerHub') {
    steps {
        script {
            retry(3) {
                sh '''
                    echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }
    }
}


        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh """
                    kubectl --kubeconfig=${KUBE_CONFIG} set image deployment/sampleapp sampleapp=${IMAGE_NAME}:${IMAGE_TAG} --record || \
                    kubectl --kubeconfig=${KUBE_CONFIG} apply -f /root/k8s-deploy.yaml
                    """
                }
            }
        }
    }
}
