pipeline {
    agent any

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
                        sh 'cmake . && make'
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
