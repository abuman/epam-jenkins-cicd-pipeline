@Library('shared-lib') _

pipeline {
    agent any

    environment {
        IMAGE_NAME     = "${env.BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}"
        IMAGE_TAG      = 'v1.0'
        HOST_PORT      = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
        APP_PORT       = '3000'
        DOCKERHUB_USER = 'albertisac'
        DOWNSTREAM_JOB = "${env.BRANCH_NAME == 'main' ? 'Deploy_to_main' : 'Deploy_to_dev'}"
        //FULL_IMAGE     = "${DOCKERHUB_USER}/${IMAGE_NAME}"
    }

    stages {

        stage('Test Lib') {
            steps {
                echo 'Shared lib loaded successfully'
            }
        }

//         stage('Init') {
//             steps {
//                 script {
//                     // Safer approach for guaranteed resolve of variable
//                     env.FULL_IMAGE = "${env.DOCKERHUB_USER}/${env.IMAGE_NAME}"
//                 }
//             }
//         }

//         stage('Lint Dockerfile') {
//             steps {
//                 script { dockerUtils.lintDockerfile() }
//             }
//         }

//         stage('Install') {
//             steps {
//                 sh 'npm install'
//             }
//         }

//         stage('Test') {
//             steps {
//                 sh 'npm test'
//             }
//         }

//         stage('Build Docker Image') {
//             agent {
//                 docker {
//                     // ↓ use an image that has both Node and Docker CLI
//                     image 'docker:24-dind'
//                     args '-v /var/run/docker.sock:/var/run/docker.sock'
//                     reuseNode true  // ← stays on same workspace, doesn't re-checkout
//                 }
//             }
//             steps {
//                 script { dockerUtils.buildImage("${env.FULL_IMAGE}", "${IMAGE_TAG}") }
//             }
//         }
        
//         stage('Scan Image') {
//             steps {
//                 script { dockerUtils.scanImage("${env.FULL_IMAGE}:${IMAGE_TAG}") }
//             }
//         }

//         stage('Push to Docker Hub') {
//             steps {
//                 script { dockerUtils.pushImage("${env.FULL_IMAGE}", "${IMAGE_TAG}") }
//             }
//         }

//         stage('Trigger Deploy') {
//             steps {
//                 build job: "${DOWNSTREAM_JOB}",
//                       parameters: [string(name: 'IMAGE_TAG', value: "${IMAGE_TAG}")],
//                       wait: false
//             }
//         }
//     }

//     post {
//         success { echo "Branch '${env.BRANCH_NAME}' built and pushed. Triggered: ${DOWNSTREAM_JOB}" }
//         failure { echo "Pipeline failed on branch '${env.BRANCH_NAME}'" }
//     }
// }
