pipeline {
    agent any

    environment {
        REPO_URL = "https://github.com/你的用户名/jenkins-github-demo.git"
        DEPLOY_BRANCH = "deploy"
        APP_NAME = "jenkins-github-demo"
        DEPLOY_DIR = "deploy_workspace"
    }

    stages {
        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Build App') {
            steps {
                sh '''
                    rm -rf dist
                    mkdir -p dist
                    cp app.js dist/app.js
                    cp package.json dist/package.json
                '''
            }
        }

        stage('Prepare Deploy Branch Content') {
            steps {
                sh '''
                    rm -rf ${DEPLOY_DIR}
                    mkdir -p ${DEPLOY_DIR}
                    cp dist/app.js ${DEPLOY_DIR}/app.js
                    cp dist/package.json ${DEPLOY_DIR}/package.json
                    cp deploy/Dockerfile ${DEPLOY_DIR}/Dockerfile
                '''
            }
        }

        stage('Push Build Result To GitHub Deploy Branch') {
            steps {
                withCredentials([usernamePa                withCredentials([usernusernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_TOKEN')]) {
                    sh '''
                        cd ${DEPLOY_DIR}
                        git init
                        git config user.name "jenkins"
                        git config user.email "jenkins@example.com"
                        git checkout -b ${DEPLOY_BRANCH}
                        git add .
                        git commit -m "Deploy build from Jenkins ${BUILD_NUMBER}" || true

                        git remote add origin https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/你的用户名/jenkins-github-demo.git

                        git push -f origin ${DEPLOY_BRANCH}
                    '''
                }
            }
        }

        stage('Pull Deploy Branch And Deploy Service') {
            steps {
                sh '''
                    rm -rf deploy_clone
                    git clone -b ${DEPLOY_BRANCH} ${REPO_URL} deploy_clone

                    docker rm -f ${APP_NAME} || true
                    docker build -t ${APP_NAME}:latest deploy_clone
                    docker run -d --name ${APP_NAME} -p 3001:3000 ${APP_NAME}:latest
                                }
        }
    }

    post {
        success {
            echo 'Build and deployment completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
