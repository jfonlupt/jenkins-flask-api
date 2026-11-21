pipeline {
    agent any
    environment {
        DOCKER_ID    = "dstdockerhub"
        DOCKER_IMAGE = "datascientestapi"
        DOCKER_TAG   = "v.${BUILD_ID}.0"
    }
    stages {
        stage('Building') {
            steps {
                // Ubuntu 24.04 interdit "pip install" hors environnement virtuel (PEP 668)
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install -r requirements.txt
                '''
            }
        }
        stage('Testing') {
            steps {
                sh '''
                    . venv/bin/activate
                    python -m unittest
                '''
            }
        }
        stage('Deploying') {
            steps {
                sh '''
                    docker build -t $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG .
                    docker rm -f flask-api || true
                    docker run -d --name flask-api -p 5000:5000 $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
                    sleep 3
                    curl -fsS http://localhost:5000/api/hello
                '''
            }
        }
    }
}
