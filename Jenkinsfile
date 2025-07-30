pipeline {
    agent any

    tools {
        gradle 'Gradle-9' // Change this to match your Jenkins Gradle config name
    }

    environment {
        VERSION_NUMBER = '1.0'
        SLACK_WEBHOOK = 'https://hooks.slack.com/services/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'
    }

    stages {
        stage('Clone repository') {
            steps {
                echo '📥 Cloning repository...'
                git 'https://github.com/edogola4/java-todo.git'
            }
        }

        stage('Build') {
            steps {
                echo "🏗️ Running build - Build #${BUILD_NUMBER}"
                sh './gradlew build'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Running tests...'
                sh './gradlew test'
            }
        }
    }

    post {
        success {
            script {
                def msg = """:white_check_mark: *Build #${env.BUILD_NUMBER}* for *${env.JOB_NAME}* succeeded. ✅\n<${env.BUILD_URL}|Click here to view>"""
                sh """
                    curl -X POST -H 'Content-type: application/json' \\
                    --data '{\"text\": \"${msg}\"}' ${env.SLACK_WEBHOOK}
                """
            }
        }

        failure {
            script {
                def msg = """:x: *Build #${env.BUILD_NUMBER}* for *${env.JOB_NAME}* failed. ❌\n<${env.BUILD_URL}|Click here to investigate>"""
                sh """
                    curl -X POST -H 'Content-type: application/json' \\
                    --data '{\"text\": \"${msg}\"}' ${env.SLACK_WEBHOOK}
                """
            }
        }
    }
}
