pipeline {
    agent any

    stages {

        stage('1 - Recuperation du code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Alaa-Rami/DevOps-AppGestionDesProjets.git'
            }
        }

        stage('2 - Creation du livrable Backend') {
            steps {
                dir('backend') {
                    sh 'mvn -Dhttps.protocols=TLSv1.2 clean install'
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
                archiveArtifacts artifacts: 'backend/target/*.jar, frontend/dist/**/*',
                                 fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Le Pipeline a reussi !'
        }
        failure {
            echo 'Le Pipeline a echoue. Verifier la console.'
        }
    }
}
