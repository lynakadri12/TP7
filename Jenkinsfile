pipeline {
    agent any

    stages {

        // Phase 1: Test
        stage('Test') {
            steps {
                echo 'Running unit tests...'
                bat 'gradlew.bat test'

                // Archivage des résultats des tests unitaires
                junit '**/build/test-results/test/*.xml'

                // Génération des rapports de tests
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'build/reports/tests/test',
                    reportFiles: 'index.html',
                    reportName: 'Test Report'
                ])

                // Génération des rapports Cucumber
                cucumber buildStatus: 'UNSTABLE',
                    reportTitle: 'Cucumber Report',
                    fileIncludePattern: '**/*.json',
                    trendsLimit: 10,
                    classifications: [
                        [key: 'Browser', value: 'N/A']
                    ]
            }
        }

        // Phase 2: Code Analysis
        stage('Code Analysis') {
            steps {
                echo 'Running SonarQube analysis...'
                withSonarQubeEnv('SonarQube') {
                    bat 'gradlew.bat sonar "-Dsonar.projectKey=lynakadri12_TP7"'
                }
            }
        }

        // Phase 3: Code Quality (Quality Gates)
        stage('Code Quality') {
            steps {
                echo 'Checking Quality Gates...'
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        // Phase 4: Build
        stage('Build') {
            steps {
                echo 'Building JAR file...'
                bat 'gradlew.bat clean build -x test'

                // Génération de la documentation
                echo 'Generating Javadoc...'
                bat 'gradlew.bat javadoc'

                // Archivage du fichier JAR et de la documentation
                archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
                archiveArtifacts artifacts: 'build/docs/javadoc/**/*', fingerprint: true

                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'build/docs/javadoc',
                    reportFiles: 'index.html',
                    reportName: 'Javadoc'
                ])
            }
        }

        // Phase 5: Deploy
        stage('Deploy') {
            steps {
                echo 'Deploying to MyMavenRepo...'
                bat 'gradlew.bat publish'
            }
        }
    }

    // Phase 6: Notification
    post {
        success {
            script {
                echo 'Pipeline succeeded! Sending notifications...'

                try {
                    // Notification par email
                    emailext(
                        subject: " Jenkins Build SUCCESS: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                        body: """
                            <h2>Build Successful!</h2>
                            <p><strong>Job:</strong> ${env.JOB_NAME}</p>
                            <p><strong>Build Number:</strong> ${env.BUILD_NUMBER}</p>
                            <p><strong>Build URL:</strong> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                            <p>Le déploiement a été effectué avec succès sur MyMavenRepo.</p>
                        """,
                        mimeType: 'text/html',
                        to: 'kadrilyna7@gmail.com'
                    )
                    echo 'Email notification sent successfully'
                } catch (Exception e) {
                    echo "Failed to send email: ${e.message}"
                }

                try {
                    // Notification Slack
                    slackSend(
                        color: 'good',
                        message: " Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}\n<${env.BUILD_URL}|View Build>"
                    )
                    echo 'Slack notification sent successfully'
                } catch (Exception e) {
                    echo "Failed to send Slack notification: ${e.message}"
                }
            }
        }

        failure {
            script {
                echo 'Pipeline failed! Sending notifications...'

                try {
                    // Notification par email
                    emailext(
                        subject: " Jenkins Build FAILED: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                        body: """
                            <h2>Build Failed!</h2>
                            <p><strong>Job:</strong> ${env.JOB_NAME}</p>
                            <p><strong>Build Number:</strong> ${env.BUILD_NUMBER}</p>
                            <p><strong>Build URL:</strong> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                            <p style="color: red;">Le pipeline a échoué. Veuillez consulter les logs pour plus de détails.</p>
                            <p><a href="${env.BUILD_URL}console">Voir les logs complets</a></p>
                        """,
                        mimeType: 'text/html',
                        to: 'kadrilyna7@gmail.com'
                    )
                    echo 'Email notification sent successfully'
                } catch (Exception e) {
                    echo "Failed to send email: ${e.message}"
                }

                try {
                    // Notification Slack
                    slackSend(
                        color: 'danger',
                        message: " Build FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}\n<${env.BUILD_URL}|View Build>"
                    )
                    echo 'Slack notification sent successfully'
                } catch (Exception e) {
                    echo "Failed to send Slack notification: ${e.message}"
                }
            }
        }

        unstable {
            script {
                echo 'Pipeline is unstable! Sending notifications...'

                try {
                    // Notification par email
                    emailext(
                        subject: "Jenkins Build UNSTABLE: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                        body: """
                            <h2>Build Unstable!</h2>
                            <p><strong>Job:</strong> ${env.JOB_NAME}</p>
                            <p><strong>Build Number:</strong> ${env.BUILD_NUMBER}</p>
                            <p><strong>Build URL:</strong> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                            <p style="color: orange;">Le pipeline est instable. Des tests ont peut-être échoué.</p>
                        """,
                        mimeType: 'text/html',
                        to: 'kadrilyna7@gmail.com'
                    )
                    echo 'Email notification sent successfully'
                } catch (Exception e) {
                    echo "Failed to send email: ${e.message}"
                }

                try {
                    // Notification Slack
                    slackSend(
                        color: 'warning',
                        message: " Build UNSTABLE: ${env.JOB_NAME} #${env.BUILD_NUMBER}\n<${env.BUILD_URL}|View Build>"
                    )
                    echo 'Slack notification sent successfully'
                } catch (Exception e) {
                    echo "Failed to send Slack notification: ${e.message}"
                }
            }
        }

        always {
            echo 'Pipeline execution completed.'
        }
    }
}