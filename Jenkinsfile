pipeline {
    agent any

    environment {
        PORT = ''
        IMAGE_NAME = ''
    }

    stages {
        stage('Docker Test') {
            steps {
        sh 'docker version'
        sh 'docker ps'
            }
        }

        stage('Checkout') {
            steps {
                git 'https://github.com/abuman/epam-jenkins-cicd-pipeline.git'
            }
        }

        stage('Set Env') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        env.PORT = '3000'
                        env.IMAGE_NAME = 'nodemain:v1.0'
                    } else {
                        env.PORT = '3001'
                        env.IMAGE_NAME = 'nodedev:v1.0'
                    }
                }
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test || true'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                # Remove all containers created from this image (ignore if none exist)
                docker rm -f $(docker ps -aq --filter "ancestor=$IMAGE_NAME") || true
                docker run -d -p $PORT:$PORT $IMAGE_NAME
                '''
            }
        }
    }
}