pipeline {
    agent any

    triggers {
        cron('H * * * *')
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/ramichatti/DevOps-AppGestionDesProjets-main-RamiChatti.git'
            }
        }

        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh './mvnw clean package'
                }
            }
        }
    }

    post {
        success {
            echo '✅ Backend CI : BUILD SUCCESS'
        }

        failure {
            echo '❌ Backend CI : BUILD FAILURE'
        }
    }
}