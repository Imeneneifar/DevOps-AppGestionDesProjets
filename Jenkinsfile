pipeline {
    agent any

    environment {
        IMAGE = 'imenneifar/studentmang-app'
    }

    stages {

        stage('1 - Recuperation du code') {
            steps {
                checkout scm
            }
        }

        stage('2 - Creation du livrable Backend') {
            steps {
                dir('backend') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('3 - Creation du livrable Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm ci'
                    sh 'npm run build'
                }
            }
        }

        stage('4 - Archivage des livrables') {
            steps {
                archiveArtifacts artifacts: 'backend/target/*.jar, frontend/dist/**/*', fingerprint: true
            }
        }

        stage('5 - Creation de l image Docker') {
            steps {
                dir('backend') {
                    sh "docker build -t ${IMAGE}:${BUILD_NUMBER} -t ${IMAGE}:latest ."
                }
            }
        }

        stage('6 - Push de l image sur Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh "docker push ${IMAGE}:${BUILD_NUMBER}"
                    sh "docker push ${IMAGE}:latest"
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
        success {
            echo 'Le Pipeline a reussi !'
        }
        failure {
            echo 'Le Pipeline a echoue. Verifier la console.'
        }
    }
}
