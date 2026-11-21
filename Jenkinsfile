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
                    docker rm -f jenkins
                    docker run -d -p 8000:8000 --name jenkins $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
                '''
            }
        }
    }
}
