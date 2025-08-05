pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "mavishek/java-demo"
    }

    tools {
        maven 'MAVEN3'         // must match Maven tool name in Jenkins
        jdk 'JDK11'            // must match JDK tool name in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/mavishk/java-demo.git  // replace with your GitHub URL
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $DOCKER_IMAGE:latest
                    '''
                }
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig-cred-id', variable: 'KUBECONFIG')]) {
                    sh '''
                        kubectl --kubeconfig=$KUBECONFIG apply -f deployment.yaml
                        kubectl --kubeconfig=$KUBECONFIG apply -f service.yaml
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment Successful!'
        }
        failure {
            echo '❌ Deployment Failed!'
        }
    }
}

