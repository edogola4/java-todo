pipeline {
    agent any

    tools {
        gradle 'Gradle-9'
    }

    environment {
        VERSION_NUMBER = '1.0'
    }

    stages {
        stage('Clone repository') {
            steps {
                echo 'Cloning repository'
                git 'https://github.com/brianmarete/java-todo.git'
            }
        }

        stage('Set Permissions') {
            steps {
                echo 'Making gradlew executable'
                sh 'chmod +x ./gradlew'
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
            slackSend color: "good", message: "Build #${BUILD_NUMBER} ran successfully"
        }

        failure {
            slackSend color: "danger", message: "Build #${BUILD_NUMBER} failed"
        }
    }
}
