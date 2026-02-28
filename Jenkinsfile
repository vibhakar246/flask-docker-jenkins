pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'flask-app:latest'
        CONTAINER_NAME = 'flask-app-container'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t ${DOCKER_IMAGE} .'
                }
            }
        }
        
        stage('Stop Old Container') {
            steps {
                script {
                    sh '''
                        if [ "$(docker ps -aq -f name=${CONTAINER_NAME})" ]; then
                            docker stop ${CONTAINER_NAME} || true
                            docker rm ${CONTAINER_NAME} || true
                        fi
                    '''
                }
            }
        }
        
        stage('Run New Container') {
            steps {
                script {
                    sh 'docker run -d -p 5000:5000 --name ${CONTAINER_NAME} ${DOCKER_IMAGE}'
                }
            }
        }
        
        stage('Test Application') {
            steps {
                script {
                    sh '''
                        sleep 5
                        echo "Testing root endpoint:"
                        curl http://localhost:5000/ || exit 1
                        echo ""
                        echo "Testing health endpoint:"
                        curl http://localhost:5000/health || exit 1
                        echo ""
                        echo "✅ Application is running!"
                    '''
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed'
        }
        success {
            echo '✅ Flask app deployed successfully!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}
