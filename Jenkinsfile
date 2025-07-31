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
        stage('Upgrade Gradle') {
            steps {
                echo '⬆️ Upgrading Gradle wrapper to 8.14.3...'
                // Verify current versions
                sh 'java -version'
                sh './gradlew --version'
                
                // Stop any daemons
                sh './gradlew --stop || true'
                
                // Download Gradle 8.14.3 and use it to upgrade wrapper
                sh '''
                    echo "Downloading Gradle 8.14.3..."
                    if [ ! -d "/tmp/gradle-8.14.3" ]; then
                        cd /tmp
                        curl -L -o gradle-8.14.3-bin.zip https://services.gradle.org/distributions/gradle-8.14.3-bin.zip
                        unzip -q gradle-8.14.3-bin.zip
                    fi
                    
                    # Use downloaded Gradle to update wrapper
                    cd $WORKSPACE
                    /tmp/gradle-8.14.3/bin/gradle wrapper --gradle-version=8.14.3 --distribution-type=bin
                    
                    # Verify the upgrade worked
                    echo "Wrapper upgraded! New version:"
                    ./gradlew --version
                '''
            }
        }
        stage('Test with New Gradle') {
            steps {
                echo '🧪 Testing with upgraded Gradle...'
                sh '''
                    export GRADLE_OPTS="-Dorg.gradle.java.home=$JAVA_HOME"
                    ./gradlew clean build --no-daemon
                '''
            }
        }
        stage('Commit Wrapper Changes') {
            steps {
                echo '💾 Committing Gradle wrapper upgrade...'
                sh '''
                    # Configure git if needed
                    git config user.email "jenkins@example.com"
                    git config user.name "Jenkins CI"
                    
                    # Add only the wrapper files
                    git add gradle/wrapper/gradle-wrapper.properties
                    git add gradle/wrapper/gradle-wrapper.jar
                    git add gradlew
                    git add gradlew.bat
                    
                    # Check if there are changes to commit
                    if git diff --staged --quiet; then
                        echo "No wrapper changes to commit"
                    else
                        git commit -m "Upgrade Gradle wrapper to 8.14.3 for Java 21 support"
                        git push origin master
                        echo "Gradle wrapper upgrade committed and pushed!"
                    fi
                '''
            }
        }
    }
    post {
        success {
            script {
                def msg = """:white_check_mark: *Gradle Upgrade Build #${env.BUILD_NUMBER}* succeeded. ✅\nGradle wrapper upgraded to 8.14.3. Ready for Java 21!\n<${env.BUILD_URL}|Click here to view>"""
                sh """
                    curl -X POST -H 'Content-type: application/json' \\
                    --data '{\"text\": \"${msg}\"}' ${env.SLACK_WEBHOOK}
                """
            }
        }
        failure {
            script {
                def msg = """:x: *Gradle Upgrade Build #${env.BUILD_NUMBER}* failed. ❌\n<${env.BUILD_URL}|Click here to investigate>"""
                sh """
                    curl -X POST -H 'Content-type: application/json' \\
                    --data '{\"text\": \"${msg}\"}' ${env.SLACK_WEBHOOK}
                """
            }
        }
    }
}
