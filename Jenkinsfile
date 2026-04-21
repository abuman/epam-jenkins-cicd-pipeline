@Library('shared-lib') _

pipeline {
    agent {
        docker {
            image 'node:18-alpine'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        IMAGE_NAME     = "${env.BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}"
        IMAGE_TAG      = 'v1.0'
        HOST_PORT      = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
        APP_PORT       = '3000'
        DOCKERHUB_USER = 'albertisac'
        DOWNSTREAM_JOB = "${env.BRANCH_NAME == 'main' ? 'Deploy_to_main' : 'Deploy_to_dev'}"
        FULL_IMAGE     = "${DOCKERHUB_USER}/${IMAGE_NAME}"
    }

    stages {

        stage('Lint Dockerfile') {
            steps {
                script { dockerUtils.lintDockerfile() }
            }
        }

        stage('Install') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script { dockerUtils.buildImage("${FULL_IMAGE}", "${IMAGE_TAG}") }
            }
        }

        stage('Scan Image') {
            steps {
                script { dockerUtils.scanImage("${FULL_IMAGE}:${IMAGE_TAG}") }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script { dockerUtils.pushImage("${FULL_IMAGE}", "${IMAGE_TAG}") }
            }
        }

        stage('Trigger Deploy') {
            steps {
                build job: "${DOWNSTREAM_JOB}",
                      parameters: [string(name: 'IMAGE_TAG', value: "${IMAGE_TAG}")],
                      wait: false
            }
        }
    }

    post {
        success { echo "Branch '${env.BRANCH_NAME}' built and pushed. Triggered: ${DOWNSTREAM_JOB}" }
        failure { echo "Pipeline failed on branch '${env.BRANCH_NAME}'" }
    }
}
