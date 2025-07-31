pipeline {
    agent any
    tools {
        gradle 'Gradle-9'
    }
    environment {
        JAVA_HOME = '/opt/homebrew/opt/openjdk@21'
        PATH = "${JAVA_HOME}/bin:${PATH}"
        VERSION_NUMBER = '1.0'
        SLACK_WEBHOOK = 'https://hooks.slack.com/services/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'
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
                echo "🏗️ Running build with Java 21 - Build #${BUILD_NUMBER}"
                // Verify versions
                sh 'java -version'
                sh './gradlew --version'
                
                // Stop any existing daemons
                sh './gradlew --stop || true'
                
                // Build with Java 21 toolchain
                sh './gradlew clean build --no-daemon'
            }
        }
        stage('Test') {
            steps {
                echo '🧪 Running tests...'
                sh './gradlew test --no-daemon'
            }
            post {
                always {
                    // Record JUnit test results
                    junit testResults: 'build/test-results/test/*.xml', allowEmptyResults: true
                }
            }
        }
        stage('Package') {
            steps {
                echo '📦 Creating distribution...'
                sh './gradlew installDist --no-daemon'
                // Archive the built artifacts
                archiveArtifacts artifacts: 'build/distributions/*.tar, build/distributions/*.zip', fingerprint: true, allowEmptyArchive: true
            }
        }
    }
    post {
        success {
            script {
                def msg = """:white_check_mark: *Build #${env.BUILD_NUMBER}* for *${env.JOB_NAME}* succeeded with Java 21! ✅\n<${env.BUILD_URL}|Click here to view>"""
                sh """
                    curl -X POST -H 'Content-type: application/json' \\
                    --data '{\"text\": \"${msg}\"}' ${env.SLACK_WEBHOOK}
                """
                emailext(
                    attachLog: true,
                    body: EMAIL_BODY,
                    subject: EMAIL_SUBJECT_SUCCESS,
                    to: EMAIL_RECIPIENT,
                    mimeType: 'text/html'
                )
            }
        }
        failure {
            script {
                def msg = """:x: *Build #${env.BUILD_NUMBER}* for *${env.JOB_NAME}* failed. ❌\n<${env.BUILD_URL}|Click here to investigate>"""
                sh """
                    curl -X POST -H 'Content-type: application/json' \\
                    --data '{\"text\": \"${msg}\"}' ${env.SLACK_WEBHOOK}
                """
                emailext(
                    attachLog: true,
                    body: EMAIL_BODY,
                    subject: EMAIL_SUBJECT_FAILURE,
                    to: EMAIL_RECIPIENT,
                    mimeType: 'text/html'
                )
            }
        }
        always {
            cleanWs()
        }
    }
}
