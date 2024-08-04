pipeline {
    agent any

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
