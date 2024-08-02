pipeline {
    agent any

    environment {
        GITHUB_REPO = 'https://github.com/supriyaveeramally/projects.git'
        SONARQUBE_URL = 'http://localhost:9000'
        SONARQUBE_AUTH_TOKEN = 'your_sonarqube_token'
        EMAIL_RECIPIENTS = 'supriyaveeramally@gmail.com'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: "${https://github.com/supriyaveeramally/projects}", branch: 'main'
            }
        }
        
        stage('Pre-Build: Syntax Checks / Linting') {
            steps {
                script {
                    // Add your syntax check / linting commands here
                    sh 'cppcheck --enable=all .'
                    sh 'python3 -m pylint **/*.py'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Add your build commands here
                    sh 'make'
                }
            }
        }

        stage('Unit Test and Code Coverage') {
            steps {
                script {
                    // Add your unit test and code coverage commands here
                    sh 'make test'
                    sh 'gcov main.c'
                }
            }
        }

        stage('Static Analysis') {
            steps {
                script {
                    // Add your static analysis commands here
                    withSonarQubeEnv('SonarQube') {
                        sh 'sonar-scanner'
                    }
                }
            }
        }

        stage('Send Email') {
            steps {
                script {
                    emailext body: 'The build is complete.',
                             subject: 'Build Notification',
                             to: "${https://github.com/supriyaveeramally/projects}",
                             attachLog: true
                }
            }
        }
    }

    post {
        always {
            script {
                // Add any cleanup or final steps here
                echo 'Pipeline completed.'
            }
        }
    }
}
