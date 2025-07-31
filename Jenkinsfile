pipeline {
    agent any
    tools {
        gradle 'Gradle-9'  // Make sure this matches your Jenkins Gradle installation name
    }
    environment {
        JAVA_HOME = '/opt/homebrew/opt/openjdk@21'  // Direct path to Java 21
        PATH = "${JAVA_HOME}/bin:${PATH}"
        VERSION_NUMBER = '1.0'
        // Slack
        SLACK_WEBHOOK = 'https://hooks.slack.com/services/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'
        // Email
        EMAIL_BODY = """
            <p>EXECUTED: Job <b>'${env.JOB_NAME}:${env.BUILD_NUMBER})'</b></p>
            <p>View console output at 
            "<a href='${env.BUILD_URL}'>${env.JOB_NAME}:${env.BUILD_NUMBER}</a>"</p> 
            <p><i>(Build log is attached.)</i></p>
        """
        EMAIL_SUBJECT_SUCCESS = "Status: 'SUCCESS' - Job '${env.JOB_NAME}:${env.BUILD_NUMBER}'"
        EMAIL_SUBJECT_FAILURE = "Status: 'FAILURE' - Job '${env.JOB_NAME}:${env.BUILD_NUMBER}'"
        EMAIL_RECIPIENT = 'brandon14ogola@gmail.com'
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
                // Add Java version verification
                sh 'java -version'
                // Stop any existing Gradle daemons
                sh './gradlew --stop || true'
                // Clear Gradle cache to be safe
                sh 'rm -rf ~/.gradle/caches/ || true'
                // Set Gradle properties and run build
                sh '''
                    export GRADLE_OPTS="-Dorg.gradle.java.home=$JAVA_HOME"
                    ./gradlew clean build --no-daemon
                '''
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
                // Slack notification
                def msg = """:white_check_mark: *Build #${env.BUILD_NUMBER}* for *${env.JOB_NAME}* succeeded. ✅\n<${env.BUILD_URL}|Click here to view>"""
                sh """
                    curl -X POST -H 'Content-type: application/json' \\
                    --data '{\"text\": \"${msg}\"}' ${env.SLACK_WEBHOOK}
                """
                // Email notification
                emailext(
                    attachLog: true,
                    body: EMAIL_BODY,
                    subject: EMAIL_SUBJECT_SUCCESS,
                    to: EMAIL_RECIPIENT
                )
            }
        }
        failure {
            script {
                // Slack notification
                def msg = """:x: *Build #${env.BUILD_NUMBER}* for *${env.JOB_NAME}* failed. ❌\n<${env.BUILD_URL}|Click here to investigate>"""
                sh """
                    curl -X POST -H 'Content-type: application/json' \\
                    --data '{\"text\": \"${msg}\"}' ${env.SLACK_WEBHOOK}
                """
                // Email notification
                emailext(
                    attachLog: true,
                    body: EMAIL_BODY,
                    subject: EMAIL_SUBJECT_FAILURE,
                    to: EMAIL_RECIPIENT
                )
            }
        }
    }
}
