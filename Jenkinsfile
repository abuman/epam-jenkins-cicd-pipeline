pipeline {
    agent any

    // tools {
    //     nodejs 'NodeJS'
    // }

    environment {
        // Dynamically set image name and port based on branch
        IMAGE_NAME = "${env.BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}"
        IMAGE_TAG  = 'v1.0'
        HOST_PORT  = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
        APP_PORT   = '3000'
    }

    stages {
        stage('Docker Test') {
            steps {
        sh 'docker version'
        sh 'docker ps'
            }
        }

        stage('Debug Env') {
            steps {
        sh 'echo BRANCH_NAME=$BRANCH_NAME'
        sh 'echo IMAGE_NAME=$IMAGE_NAME'
             }
        }

        stage('Install') {
            steps {
                echo "Installing dependencies on branch: ${env.BRANCH_NAME}"
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Deploy Container') {
            steps {
                echo "Deploying ${IMAGE_NAME}:${IMAGE_TAG} on host port ${HOST_PORT}..."
                script {
                    // Stop and remove any container using the target host port (minimises downtime)
                    sh """
                        # Find and stop any container already bound to the target port
                        EXISTING=\$(docker ps -q --filter "publish=${HOST_PORT}")
                        if [ -n "\$EXISTING" ]; then
                            echo "Stopping container(s) on port ${HOST_PORT}: \$EXISTING"
                            docker stop \$EXISTING
                            docker rm -f \$EXISTING
                        fi
 
                        # Also remove any stopped container with the same image name
                        OLD=\$(docker ps -aq --filter "ancestor=${IMAGE_NAME}:${IMAGE_TAG}")
                        if [ -n "\$OLD" ]; then
                            echo "Removing old containers for ${IMAGE_NAME}:${IMAGE_TAG}: \$OLD"
                            docker rm -f \$OLD
                        fi

                        # Catch any leftover container with the same name
                        docker rm -f ${IMAGE_NAME} 2>/dev/null || true
                    """
 
                    // Start the new container immediately after cleanup
                    sh """
                        docker run -d \
                            --expose ${APP_PORT} \
                            -p ${HOST_PORT}:${APP_PORT} \
                            --name ${IMAGE_NAME} \
                            ${IMAGE_NAME}:${IMAGE_TAG}
                    """
 
                    echo "Container ${IMAGE_NAME} is running — accessible on port ${HOST_PORT}"
                }
            }
        }
    }
     post {
        success {
            echo "Pipeline succeeded for branch '${env.BRANCH_NAME}'. App is live on port ${HOST_PORT}."
        }
        failure {
            echo "Pipeline FAILED for branch '${env.BRANCH_NAME}'. Check the logs above."
        }
    }
}