pipeline {
    agent any

    environment {
        IMAGE_NAME     = 'jenkins-flask-api'
        CONTAINER_NAME = 'flask-api'
        APP_PORT       = '5000'
    }

    stages {
        stage('Build') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} -t ${IMAGE_NAME}:latest .'
            }
        }

        stage('Test') {
            steps {
                sh 'docker run --rm ${IMAGE_NAME}:${BUILD_NUMBER} python -m unittest -v'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true
                    docker run -d --name ${CONTAINER_NAME} -p ${APP_PORT}:5000 ${IMAGE_NAME}:${BUILD_NUMBER}
                    sleep 3
                    curl -fsS http://localhost:${APP_PORT}/api/hello
                '''
            }
        }
    }

    post {
        success {
            echo "API deployee : http://<IP de la VM>:${APP_PORT}/api/hello"
        }
        failure {
            echo 'Le pipeline a echoue : voir les logs de l etape en rouge.'
        }
    }
}
