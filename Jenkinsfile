pipeline {
    environment {
        IMAGE_NAME = "docker-test"
        dockerHubCredentials = 'admin'
        SHORT_COMMIT = sh(script: 'git rev-parse --short HEAD', returnStdout: true)
    }
 
    agent any
 
    stages {
        stage('Cloning Git') {
            steps {
                git([url: 'https://github.com/aryawpratama/test-docker.git', branch: 'master'])
            }
        }
 
        stage('Building image') {
            steps {
                script {
                    sh 'docker build . -t 192.168.10.5:5000/aryawpratama/${IMAGE_NAME}:${SHORT_COMMIT}'
                }
            }
        }
        
        stage('Deploy Image') {
            steps {
                script {
                    // Use Jenkins credentials for Docker Hub login
                    withCredentials([usernamePassword(credentialsId: dockerHubCredentials, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "docker login 192.168.10.5:5000 -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
                        sh 'docker push --all-tags 192.168.10.5:5000/aryawpratama/${IMAGE_NAME}'
                    }
                }
            }
        }

        
        stage('Dangling Containers') {
            steps {
                script {
                    sh 'docker ps -q -f status=exited | xargs --no-run-if-empty docker rm'
                }
            }
        }

        stage('Dangling Images') {
            steps {
                script {
                    sh 'docker images -q -f dangling=true | xargs --no-run-if-empty docker rmi'
                }
            }
        }
    
        stage('Dangling Volumes') {
            steps {
                script {
                    sh 'docker volume ls -qf dangling=true | xargs -r docker volume rm'
                }
            }
        }
    }
}
