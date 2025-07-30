pipeline {
    agent any

    tools {
        gradle 'Gradle-9' // Ensure this matches your Jenkins tool config
    }

    environment {
        VERSION_NUMBER = '1.0'
        SLACK_WEBHOOK = 'https://hooks.slack.com/services/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' // Replace with your actual webhook
    }

    stages {
        stage('Clone repository') {
            steps {
                echo 'Cloning repository...'
                git 'https://github.com/edogola4/java-todo.git'
            }
        }

        stage('Build') {
            steps {
                echo "Running build: Build #${BUILD_NUMBER}"
                sh './gradlew build'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh './gradlew test'
            }
        }
    }

    post {
        success {
            script {
                def message = """:white_check_mark: *Build #${env.BUILD_NUMBER}* of *${env.JOB_NAME}* succeeded! \n<${env.BUILD_URL}|View Build>"""
                sh """
                    curl -X POST -H 'Content-type: application/json' \\
                    --data '{\"text\": \"${message}\"}' \\
                    ${env.SLACK_WEBHOOK}
                """
            }
        }

        failure {
            script {
                def message = """:x: *Build #${env.BUILD_NUMBER}* of *${env.JOB_NAME}* failed. \n<${env.BUILD_URL}|View Build>"""
                sh """
                    curl -X POST -H 'Content-type: application/json' \\
                    --data '{\"text\": \"${message}\"}' \\
                    ${env.SLACK_WEBHOOK}
                """
            }
        }
    }
}
