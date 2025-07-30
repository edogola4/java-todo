pipeline {
    agent any

    tools {
        gradle 'Gradle-9'
    }

    environment {
        VERSION_NUMBER = '1.0'
        SLACK_WEBHOOK = 'https://hooks.slack.com/services/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'
    }

    stages {
        stage('Clone repository') {
            steps {
                echo 'Cloning repository'
                git 'https://github.com/edogola4/java-todo.git'
            }
        }

        stage('Build') {
            steps {
                echo "Build number ${BUILD_NUMBER}"
                sh './gradlew build'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing the project'
                sh './gradlew test'
            }
        }
    }

    post {
        success {
            sh '''
                curl -X POST -H 'Content-type: application/json' \
                --data "{\"text\": \":white_check_mark: *Build #${BUILD_NUMBER}* of *${JOB_NAME}* succeeded! \\n<${BUILD_URL}|View Build>\"}" \
                $SLACK_WEBHOOK
            '''
        }

        failure {
            sh '''
                curl -X POST -H 'Content-type: application/json' \
                --data "{\"text\": \":x: *Build #${BUILD_NUMBER}* of *${JOB_NAME}* failed. \\n<${BUILD_URL}|View Build>\"}" \
                $SLACK_WEBHOOK
            '''
        }
    }
}
