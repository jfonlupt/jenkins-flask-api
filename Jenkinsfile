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
                sh 'docker build -t $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG .'
            }
        }
        stage('Testing') {
            steps {
                sh 'docker run --rm $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG python -m unittest -v'
            }
        }
        stage('Deploying') {
            steps {
                sh '''
                    docker rm -f flask-api || true
                    docker run -d --name flask-api -p 5000:5000 $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
                    sleep 3
                    curl -fsS http://localhost:5000/api/hello
                '''
            }
        }
    }
}
