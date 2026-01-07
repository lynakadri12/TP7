pipeline{
    agent any
    stages{
        stage('build'){
            steps{
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
    }
}