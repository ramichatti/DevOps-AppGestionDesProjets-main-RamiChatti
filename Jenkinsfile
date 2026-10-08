pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        DOCKER_IMAGE = 'ramichatti/appgestion-backend:latest'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/ramichatti/DevOps-AppGestionDesProjets-main-RamiChatti.git'
            }
        }

        stage('Build & Test Backend') {
            steps {
                dir('backend') {
                    sh './mvnw clean verify'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('backend') {
                    withSonarQubeEnv('SonarQube') {
                        sh '''
                            ./mvnw org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
                                -Dsonar.projectKey=appgestion-backend \
                                -Dsonar.projectName="AppGestion Backend"
                        '''
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker Build') {
            steps {
                dir('backend') {
                    sh 'docker build -t ${DOCKER_IMAGE} .'
                }
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            -u "$DOCKER_USERNAME" \
                            --password-stdin

                        docker push ${DOCKER_IMAGE}

                        docker logout
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ CI/CD SUCCESS : Build + Tests + SonarQube + Quality Gate + Docker Build + Docker Push'
        }

        failure {
            echo '❌ CI/CD FAILURE'
        }
    }
}
