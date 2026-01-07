pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                bat 'gradlew.bat build'
                archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
            }
        }

        stage('Test') {
            steps {
                bat 'gradlew.bat test'

                junit '**/build/test-results/test/*.xml'

                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'build/reports/tests/test',
                    reportFiles: 'index.html',
                    reportName: 'Test Report'
                ])
            }
             stage('Code Analysis') {
                        steps {
                            echo 'Running SonarQube analysis...'
                            withSonarQubeEnv('SonarQube') {
                                bat """
                                    gradlew.bat sonar ^
                                    -Dsonar.projectKey=tp7-project ^
                                    -Dsonar.projectName="TP7 Project" ^
                                    -Dsonar.host.url=http://localhost:9000 ^
                                    -Dsonar.token=YOUR_SONAR_TOKEN
                                """
                            }
                        }
                    }
        }
    }
}