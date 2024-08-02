pipeline {
    agent any

    environment {
        // Define environment variables
        SONARQUBE_URL = 'http://your-sonarqube-server'
        SONARQUBE_TOKEN = credentials('sonarqube-token')
    }

    stages {
        stage('Pre-Build: Linting') {
            steps {
                script {
                    if (env.PROJECT_TYPE == 'C' || env.PROJECT_TYPE == 'C++') {
                        sh 'cppcheck --enable=all .'
                    } else if (env.PROJECT_TYPE == 'Java') {
                        sh 'java -jar checkstyle.jar -c /google_checks.xml src'
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    if (env.PROJECT_TYPE == 'C' || env.PROJECT_TYPE == 'C++') {
                        sh 'make'
                    } else if (env.PROJECT_TYPE == 'Java') {
                        sh './gradlew build'
                    }
                }
            }
        }

        stage('Unit Test and Code Coverage') {
            steps {
                script {
                    if (env.PROJECT_TYPE == 'C' || env.PROJECT_TYPE == 'C++') {
                        sh 'make test'
                        sh 'gcov main.cpp'
                    } else if (env.PROJECT_TYPE == 'Java') {
                        sh './gradlew test jacocoTestReport'
                    }
                }
            }
        }

        stage('Static Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    script {
                        if (env.PROJECT_TYPE == 'C' || env.PROJECT_TYPE == 'C++') {
                            sh 'sonar-scanner -Dsonar.projectKey=my_project -Dsonar.sources=. -Dsonar.host.url=$SONARQUBE_URL -Dsonar.login=$SONARQUBE_TOKEN'
                        } else if (env.PROJECT_TYPE == 'Java') {
                            sh './gradlew sonarqube -Dsonar.projectKey=my_project -Dsonar.host.url=$SONARQUBE_URL -Dsonar.login=$SONARQUBE_TOKEN'
                        }
                    }
                }
            }
        }

        stage('Gate Conditions') {
            steps {
                script {
                    def coverage = readFile('coverage.txt').trim()
                    if (coverage.toInteger() < 80) {
                        error "Coverage is below 80%"
                    }
                }
            }
        }
    }

    post {
        always {
            emailext(
                to: 'supriyaveeramally@gmail.com',
                subject: "Build ${currentBuild.fullDisplayName}",
                body: "Build ${currentBuild.fullDisplayName} completed with status: ${currentBuild.currentResult}. Check the build logs.",
                attachLog: true
            )
        }
    }
}
